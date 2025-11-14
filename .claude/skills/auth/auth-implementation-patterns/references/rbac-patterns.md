# RBAC: Role-Based Access Control Patterns

## Core Model

```
User → Role(s) → Permission(s) → Resource
```

## Pattern 1: Simple RBAC (Flat)

```typescript
enum Role {
    ADMIN = 'admin',
    MODERATOR = 'moderator',
    USER = 'user'
}

const rolePermissions: Record<Role, string[]> = {
    [Role.ADMIN]: ['create', 'read', 'update', 'delete'],
    [Role.MODERATOR]: ['read', 'update'],
    [Role.USER]: ['read']
};

function checkPermission(user: User, action: string): boolean {
    return rolePermissions[user.role]?.includes(action) ?? false;
}
```

## Pattern 2: Hierarchical RBAC

```typescript
const roleHierarchy: Record<Role, Role[]> = {
    [Role.ADMIN]: [Role.ADMIN, Role.MODERATOR, Role.USER],
    [Role.MODERATOR]: [Role.MODERATOR, Role.USER],
    [Role.USER]: [Role.USER]
};

function hasRole(userRole: Role, requiredRole: Role): boolean {
    return roleHierarchy[userRole].includes(requiredRole);
}

// Admin can do anything a moderator or user can do
app.delete('/users/:id',
    authenticate,
    requireRole(Role.ADMIN),
    deleteUser
);
```

## Pattern 3: Permission-Based RBAC

More flexible than role-based alone:

```typescript
enum Permission {
    READ_USERS = 'read:users',
    CREATE_USERS = 'create:users',
    UPDATE_USERS = 'update:users',
    DELETE_USERS = 'delete:users',
    MANAGE_ROLES = 'manage:roles'
}

const rolePermissions: Record<Role, Permission[]> = {
    [Role.ADMIN]: Object.values(Permission),
    [Role.MODERATOR]: [Permission.READ_USERS, Permission.UPDATE_USERS],
    [Role.USER]: [Permission.READ_USERS]
};

function requirePermission(...required: Permission[]) {
    return (req, res, next) => {
        const userPermissions = rolePermissions[req.user.role] ?? [];
        
        if (!required.every(p => userPermissions.includes(p))) {
            return res.status(403).json({ error: 'Insufficient permissions' });
        }
        next();
    };
}

// Usage
app.post('/users',
    authenticate,
    requirePermission(Permission.CREATE_USERS),
    createUser
);
```

## Pattern 4: Resource-Based RBAC

Control access to specific resource instances:

```typescript
async function requireOwnership(resourceType: 'post' | 'comment') {
    return async (req, res, next) => {
        if (!req.user) return res.status(401).end();
        
        // Admins can do anything
        if (req.user.role === Role.ADMIN) return next();
        
        const resourceId = req.params.id;
        const resource = resourceType === 'post'
            ? await db.posts.findById(resourceId)
            : await db.comments.findById(resourceId);
        
        if (!resource) return res.status(404).end();
        
        // Must be owner
        if (resource.userId !== req.user.id) {
            return res.status(403).json({ error: 'Not authorized' });
        }
        
        next();
    };
}

// Usage
app.delete('/posts/:id',
    authenticate,
    requireOwnership('post'),
    deletePost
);
```

## Pattern 5: Attribute-Based RBAC (Advanced)

Consider user attributes, resource attributes, environment:

```typescript
interface AccessContext {
    user: {
        id: string;
        role: Role;
        department: string;
        status: 'active' | 'suspended';
    };
    resource: {
        type: string;
        owner: string;
        classification: 'public' | 'internal' | 'confidential';
    };
    context: {
        time: Date;
        location: string;
        ipAddress: string;
    };
}

function checkAccess(context: AccessContext): boolean {
    const { user, resource, context } = context;
    
    // Admin can access anything
    if (user.role === Role.ADMIN && user.status === 'active') {
        return true;
    }
    
    // Owner can access their own resources
    if (resource.owner === user.id && resource.classification !== 'confidential') {
        return true;
    }
    
    // Moderators can access internal and public resources
    if (user.role === Role.MODERATOR && resource.classification !== 'confidential') {
        return true;
    }
    
    // Users can only access public resources
    if (resource.classification === 'public') {
        return true;
    }
    
    return false;
}
```

## Implementation Strategy

### 1. Define Roles
```
- Admin: Full access
- Manager: Team access + approvals
- Employee: Self + public data
```

### 2. Map to Permissions
```
Admin: [all permissions]
Manager: [read:team, update:team, approve:requests]
Employee: [read:own, update:own]
```

### 3. Implement Middleware
```typescript
const authorizeAsync = (roles: Role[], permissions?: Permission[]) => {
    return async (req, res, next) => {
        if (!roles.includes(req.user.role)) {
            return res.status(403).json({ error: 'Insufficient role' });
        }
        
        if (permissions) {
            const userPerms = rolePermissions[req.user.role];
            if (!permissions.every(p => userPerms.includes(p))) {
                return res.status(403).json({ error: 'Insufficient permissions' });
            }
        }
        
        next();
    };
};
```

### 4. Test Permissions
```typescript
// Unit test
test('Admin can delete users', () => {
    const admin = { role: Role.ADMIN };
    expect(checkPermission(admin, 'delete')).toBe(true);
});

test('User cannot delete users', () => {
    const user = { role: Role.USER };
    expect(checkPermission(user, 'delete')).toBe(false);
});
```

## Best Practices

✓ Keep roles count < 10 (avoid role explosion)
✓ Use permission constants (don't hardcode strings)
✓ Always validate server-side (frontend UI is just UX)
✓ Log permission denials for audit
✓ Review role assignments quarterly
✓ Implement separation of duties (no user can both create AND approve)
✓ Use centralized authorization service for consistency

