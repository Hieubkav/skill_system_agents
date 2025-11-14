# Async Operations & Webhooks

Comprehensive guide to long-running tasks, job queues, webhooks, and real-time updates.

## Table of Contents

- [Async Operations](#async-operations)
- [Job Queues](#job-queues)
- [Webhooks](#webhooks)
- [Server-Sent Events (SSE)](#server-sent-events-sse)
- [WebSockets](#websockets)
- [Best Practices](#best-practices)

---

## Async Operations

### Long-Running Tasks

When operations take >30 seconds, use async pattern:

**Step 1: Accept Request**
```http
POST /api/v1/reports/generate
Content-Type: application/json

{
  "type": "annual_summary",
  "year": 2025
}

Response (202 Accepted):
{
  "id": "job_abc123",
  "status": "queued",
  "statusUrl": "/api/v1/jobs/job_abc123",
  "estimatedTime": 300
}
```

**Step 2: Check Status**
```http
GET /api/v1/jobs/job_abc123

Response (200 OK):
{
  "id": "job_abc123",
  "status": "processing",
  "progress": 65,
  "createdAt": "2025-10-16T10:00:00Z",
  "estimatedCompletion": "2025-10-16T10:05:00Z"
}
```

**Step 3: Get Result**
```http
GET /api/v1/jobs/job_abc123

Response (200 OK):
{
  "id": "job_abc123",
  "status": "completed",
  "progress": 100,
  "result": {
    "reportUrl": "/api/v1/reports/annual_summary_2025.pdf",
    "size": 2456789,
    "pages": 42
  },
  "createdAt": "2025-10-16T10:00:00Z",
  "completedAt": "2025-10-16T10:05:00Z"
}
```

### Status Values

- `queued` - Task queued, not started
- `processing` - Task in progress
- `completed` - Task finished successfully
- `failed` - Task failed with errors
- `cancelled` - Task cancelled by user

---

## Job Queues

### Bull Queue (Node.js + Redis)

```javascript
const Queue = require('bull');

// Create queue
const reportQueue = new Queue('reports', {
  redis: { host: 'localhost', port: 6379 }
});

// Add job to queue
app.post('/api/reports/generate', async (req, res) => {
  const job = await reportQueue.add({
    type: req.body.type,
    year: req.body.year,
    userId: req.user.id
  }, {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 2000
    }
  });

  res.status(202).json({
    id: job.id,
    status: 'queued',
    statusUrl: `/api/jobs/${job.id}`
  });
});

// Process jobs
reportQueue.process(async (job) => {
  const { type, year, userId } = job.data;
  
  // Update progress
  job.progress(10);
  
  // Generate report
  const report = await generateReport(type, year);
  
  job.progress(50);
  
  // Save to storage
  const url = await saveToStorage(report);
  
  job.progress(100);
  
  return { reportUrl: url };
});

// Check job status
app.get('/api/jobs/:id', async (req, res) => {
  const job = await reportQueue.getJob(req.params.id);
  
  if (!job) {
    return res.status(404).json({ error: 'Job not found' });
  }

  const state = await job.getState();
  const progress = job.progress();

  res.json({
    id: job.id,
    status: state,
    progress,
    createdAt: new Date(job.timestamp),
    result: state === 'completed' ? job.returnvalue : null
  });
});
```

### Laravel Queue

```php
// Job class
class GenerateReportJob implements ShouldQueue
{
    public $type;
    public $year;
    public $userId;

    public function handle()
    {
        $report = $this->generateReport($this->type, $this->year);
        $url = Storage::put('reports', $report);
        
        // Trigger webhook
        event(new ReportGenerated($this->userId, $url));
    }
}

// Dispatch job
Route::post('/api/reports/generate', function (Request $request) {
    $job = new GenerateReportJob(
        $request->type,
        $request->year,
        auth()->id()
    );
    
    dispatch($job);

    return response()->json([
        'id' => $job->id,
        'status' => 'queued',
        'statusUrl' => "/api/jobs/{$job->id}"
    ], 202);
});
```

---

## Webhooks

### Webhook Basics

Webhooks notify your server when events occur in external services.

### Webhook Payload

```http
POST https://your-api.com/webhooks/payment
Content-Type: application/json
X-Webhook-Signature: sha256=abc123...

{
  "event": "payment.succeeded",
  "timestamp": "2025-10-16T10:30:00Z",
  "id": "evt_abc123",
  "data": {
    "id": "pay_xyz789",
    "amount": 100.00,
    "currency": "USD",
    "customer": "cus_123"
  }
}
```

### Webhook Security (HMAC Signature)

**Generate signature (sender):**
```javascript
const crypto = require('crypto');

const payload = JSON.stringify(webhookData);
const signature = crypto
  .createHmac('sha256', webhookSecret)
  .update(payload)
  .digest('hex');

// Send with header
headers['X-Webhook-Signature'] = `sha256=${signature}`;
```

**Verify signature (receiver):**
```javascript
const verifyWebhook = (payload, signature, secret) => {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');

  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(`sha256=${expectedSignature}`)
  );
};

app.post('/webhooks/payment', (req, res) => {
  const signature = req.headers['x-webhook-signature'];
  const payload = JSON.stringify(req.body);

  if (!verifyWebhook(payload, signature, process.env.WEBHOOK_SECRET)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }

  // Process webhook
  processPaymentWebhook(req.body);

  res.status(200).json({ received: true });
});
```

### Webhook Retry Logic

```javascript
const axios = require('axios');

const sendWebhook = async (url, data, secret, attempt = 1) => {
  const maxAttempts = 3;
  const backoffMs = Math.pow(2, attempt) * 1000; // Exponential backoff

  const payload = JSON.stringify(data);
  const signature = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');

  try {
    await axios.post(url, data, {
      headers: {
        'Content-Type': 'application/json',
        'X-Webhook-Signature': `sha256=${signature}`
      },
      timeout: 10000
    });

    console.log('Webhook delivered successfully');
  } catch (error) {
    if (attempt < maxAttempts) {
      console.log(`Webhook failed, retrying in ${backoffMs}ms (attempt ${attempt}/${maxAttempts})`);
      setTimeout(() => sendWebhook(url, data, secret, attempt + 1), backoffMs);
    } else {
      console.error('Webhook failed after max attempts');
      // Log to dead letter queue
    }
  }
};
```

### Webhook Management API

```javascript
// Register webhook
app.post('/api/webhooks', async (req, res) => {
  const webhook = await Webhook.create({
    url: req.body.url,
    events: req.body.events, // ['payment.succeeded', 'payment.failed']
    secret: generateSecret(),
    userId: req.user.id
  });

  res.status(201).json(webhook);
});

// List webhooks
app.get('/api/webhooks', async (req, res) => {
  const webhooks = await Webhook.find({ userId: req.user.id });
  res.json(webhooks);
});

// Delete webhook
app.delete('/api/webhooks/:id', async (req, res) => {
  await Webhook.deleteOne({
    _id: req.params.id,
    userId: req.user.id
  });
  res.status(204).end();
});

// Webhook delivery logs
app.get('/api/webhooks/:id/deliveries', async (req, res) => {
  const deliveries = await WebhookDelivery.find({
    webhookId: req.params.id
  }).sort({ createdAt: -1 }).limit(50);

  res.json(deliveries);
});
```

---

## Server-Sent Events (SSE)

One-way real-time updates from server to client.

### Server Implementation

```javascript
app.get('/api/events', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');

  // Send initial message
  res.write('data: {"message": "Connected"}\n\n');

  // Send updates every 5 seconds
  const intervalId = setInterval(() => {
    const data = { timestamp: Date.now(), value: Math.random() };
    res.write(`data: ${JSON.stringify(data)}\n\n`);
  }, 5000);

  // Clean up on disconnect
  req.on('close', () => {
    clearInterval(intervalId);
    res.end();
  });
});
```

### Client Implementation

```javascript
const eventSource = new EventSource('/api/events');

eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Received:', data);
};

eventSource.onerror = (error) => {
  console.error('SSE error:', error);
  eventSource.close();
};
```

### Named Events

```javascript
// Server
res.write('event: notification\n');
res.write(`data: ${JSON.stringify({ message: 'New notification' })}\n\n`);

res.write('event: update\n');
res.write(`data: ${JSON.stringify({ status: 'completed' })}\n\n`);

// Client
eventSource.addEventListener('notification', (event) => {
  console.log('Notification:', JSON.parse(event.data));
});

eventSource.addEventListener('update', (event) => {
  console.log('Update:', JSON.parse(event.data));
});
```

---

## WebSockets

Two-way real-time communication.

### Server (Socket.io)

```javascript
const io = require('socket.io')(server, {
  cors: {
    origin: 'https://myapp.com',
    credentials: true
  }
});

// Authentication middleware
io.use(async (socket, next) => {
  const token = socket.handshake.auth.token;
  try {
    const user = await verifyToken(token);
    socket.userId = user.id;
    next();
  } catch (err) {
    next(new Error('Authentication failed'));
  }
});

// Connection handling
io.on('connection', (socket) => {
  console.log(`User ${socket.userId} connected`);

  // Join room
  socket.join(`user:${socket.userId}`);

  // Handle events
  socket.on('message', (data) => {
    console.log('Received message:', data);
    socket.emit('message-received', { id: data.id, status: 'received' });
  });

  // Broadcast to room
  socket.on('notification', (data) => {
    io.to(`user:${data.userId}`).emit('notification', data);
  });

  // Disconnect
  socket.on('disconnect', () => {
    console.log(`User ${socket.userId} disconnected`);
  });
});

// Send notification from API
app.post('/api/notifications', async (req, res) => {
  const notification = await Notification.create(req.body);

  // Broadcast to user via WebSocket
  io.to(`user:${req.body.userId}`).emit('notification', notification);

  res.status(201).json(notification);
});
```

### Client (Socket.io)

```javascript
const socket = io('https://api.example.com', {
  auth: {
    token: accessToken
  }
});

socket.on('connect', () => {
  console.log('Connected to WebSocket');
});

socket.on('notification', (data) => {
  console.log('New notification:', data);
  showNotification(data);
});

socket.emit('message', { text: 'Hello server' });

socket.on('disconnect', () => {
  console.log('Disconnected from WebSocket');
});
```

---

## Best Practices

### 1. Return 202 Accepted for Async Operations

```javascript
res.status(202).json({
  id: job.id,
  status: 'queued',
  statusUrl: `/api/jobs/${job.id}`
});
```

### 2. Provide Status Endpoints

```javascript
GET /api/jobs/:id
```

### 3. Implement Webhook Retries

Retry failed webhooks with exponential backoff (1s, 2s, 4s, 8s...).

### 4. Secure Webhooks with HMAC

Always verify webhook signatures to prevent unauthorized requests.

### 5. Use Job Queues

Don't block HTTP requests with long operations. Use queues (Bull, Sidekiq, Celery).

### 6. Set Timeouts

```javascript
await axios.post(webhookUrl, data, { timeout: 10000 });
```

### 7. Log Webhook Deliveries

Store webhook delivery attempts for debugging:

```javascript
await WebhookDelivery.create({
  webhookId: webhook.id,
  url: webhook.url,
  payload: data,
  status: response.status,
  response: response.data,
  attemptNumber: attempt,
  createdAt: new Date()
});
```

### 8. Implement Dead Letter Queues

Move failed jobs to DLQ for manual review.

### 9. Support Webhook Testing

```javascript
// Test webhook endpoint
app.post('/api/webhooks/:id/test', async (req, res) => {
  const webhook = await Webhook.findById(req.params.id);
  
  await sendWebhook(webhook.url, {
    event: 'webhook.test',
    timestamp: new Date().toISOString()
  }, webhook.secret);

  res.json({ sent: true });
});
```

### 10. Monitor Webhook Health

Track delivery success rates, latency, and failures.

---

## Comparison

| Method | Use Case | Pros | Cons |
|--------|----------|------|------|
| **Polling** | Simple status checks | Easy to implement | Inefficient, delayed |
| **Webhooks** | Event notifications | Efficient, real-time | Requires public endpoint |
| **SSE** | Server-to-client updates | Simple, HTTP-based | One-way only |
| **WebSockets** | Bidirectional chat | Full duplex, low latency | Complex, stateful |
| **Long Polling** | Fallback for old browsers | Works everywhere | Inefficient |

---

## Testing Webhooks

```javascript
describe('Webhooks', () => {
  test('should verify valid signature', () => {
    const payload = JSON.stringify({ event: 'test' });
    const signature = generateSignature(payload, secret);
    
    expect(verifyWebhook(payload, signature, secret)).toBe(true);
  });

  test('should reject invalid signature', () => {
    const payload = JSON.stringify({ event: 'test' });
    const signature = 'invalid_signature';
    
    expect(verifyWebhook(payload, signature, secret)).toBe(false);
  });

  test('should retry failed webhooks', async () => {
    const mockAxios = jest.spyOn(axios, 'post')
      .mockRejectedValueOnce(new Error('Network error'))
      .mockResolvedValueOnce({ status: 200 });

    await sendWebhook(url, data, secret);

    expect(mockAxios).toHaveBeenCalledTimes(2);
  });
});
```
