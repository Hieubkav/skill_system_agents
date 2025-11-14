# Skill Examples

Các ví dụ cụ thể về các loại Agent Skills khác nhau để tham khảo.

## Table of Contents

- [Example 1: Simple Single-File Skill](#example-1-simple-single-file-skill)
- [Example 2: Read-Only Skill với Tool Permissions](#example-2-read-only-skill-với-tool-permissions)
- [Example 3: Script-Based Skill](#example-3-script-based-skill)
- [Example 4: Multi-File Skill với Progressive Disclosure](#example-4-multi-file-skill-với-progressive-disclosure)
- [Example 5: Skill với Assets và Templates](#example-5-skill-với-assets-và-templates)
- [Example 6: Data Processing Skill](#example-6-data-processing-skill)

---

## Example 1: Simple Single-File Skill

**Use Case**: Skill đơn giản, tất cả trong SKILL.md

### Folder Structure
```
~/.claude/skills/git/
|---commit-helper/
    |---SKILL.md
    |---README.md
```

### SKILL.md
```yaml
---
name: commit-helper
description: Generate well-formatted git commit messages following conventional commits standard. Use when creating commits, writing commit messages, or when user says "git commit", "commit message", or needs help with commit formatting.
---

# Commit Helper

## Level 1: Overview

Generate conventional commit messages that follow best practices: type(scope): description format.

## Prerequisites

- Git repository initialized
- Changes staged with `git add`

## What This Skill Does

1. Analyzes staged changes with `git diff --cached`
2. Suggests appropriate commit type (feat, fix, docs, etc.)
3. Generates conventional commit message
4. Creates commit with proper formatting

---

## Level 2: Quick Start

### Basic Usage

```bash
# 1. Stage your changes
git add .

# 2. Ask AI AGENT
"Help me create a commit message"

# 3. AI AGENT will analyze changes and suggest:
git commit -m "feat(auth): add login endpoint"
```

### Commit Types

- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation only
- **style**: Code style changes
- **refactor**: Code refactoring
- **test**: Adding tests
- **chore**: Maintenance tasks

---

## Common Use Cases

### Case 1: Feature Addition
```bash
git commit -m "feat(api): add user profile endpoint

- Add GET /api/users/:id endpoint
- Include avatar URL in response
- Add unit tests for profile retrieval"
```

### Case 2: Bug Fix
```bash
git commit -m "fix(auth): resolve token expiration issue

Token was expiring 1 hour early due to timezone calculation"
```

### Case 3: Documentation
```bash
git commit -m "docs(readme): update installation instructions"
```

---

## Best Practices

- Keep first line ≤ 50 characters
- Use imperative mood ("add" not "added")
- Include scope in parentheses
- Add detailed body for complex changes
- Reference issue numbers if applicable

## Troubleshooting

| Issue | Solution |
|-------|----------|
| No changes staged | Run `git add` first |
| Message too long | Split into multiple commits |
| Wrong commit type | Refer to commit types list above |
```

### README.md
```markdown
# Commit Helper

Tạo git commit messages theo chuẩn conventional commits.
```

---

## Example 2: Read-Only Skill với Tool Permissions

**Use Case**: Skill chỉ đọc và phân tích, không modify files

### Folder Structure
```
~/.claude/skills/code-quality/
|---code-reviewer/
    |---SKILL.md
    |---README.md
    |---reference/
        |---checklist.md
```

### SKILL.md
```yaml
---
name: code-reviewer
description: Review code for quality, security, and best practices without making changes. Use for code review, security audit, or when user says "review code", "check code quality", "security review".
allowed-tools: Read, Grep, Glob
---

# Code Reviewer

## Level 1: Overview

Analyze code for quality issues, security vulnerabilities, and adherence to best practices. Read-only skill that provides detailed feedback without modifying files.

## Prerequisites

- Source code repository access
- Code follows standard structure

## What This Skill Does

1. Reads code files with Read tool
2. Searches for patterns with Grep
3. Analyzes code quality and security
4. Provides actionable feedback
5. No modifications to codebase

---

## Level 2: Quick Start

### Basic Usage

```bash
# AI AGENT will:
# 1. Use Glob to find relevant files
# 2. Use Read to analyze code
# 3. Use Grep to find patterns
# 4. Generate report (no edits)
```

### Key Steps

1. Identify files to review
2. Read and analyze code
3. Check against [checklist.md](./reference/checklist.md)
4. Generate report with findings
5. Suggest improvements (don't implement)

---

## Level 3: Reference Guides

- [checklist.md](./reference/checklist.md) - Use when: Need comprehensive review criteria

---

## Common Use Cases

### Case 1: Security Review
- Check for SQL injection vulnerabilities
- Identify exposed credentials
- Find insecure API calls

### Case 2: Code Quality Review
- Identify code smells
- Find unused variables
- Check naming conventions

### Case 3: Performance Review
- Identify N+1 queries
- Find inefficient algorithms
- Spot memory leaks

---

## Best Practices

- Focus on high-impact issues first
- Provide examples of good alternatives
- Link to documentation for best practices
- Prioritize security over style
- Be constructive in feedback

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Too many files | Use Glob patterns to filter |
| False positives | Provide context in analysis |
| Missing context | Read related files for full picture |
```

### reference/checklist.md
```markdown
# Code Review Checklist

## Security
- [ ] No hardcoded credentials
- [ ] Input validation present
- [ ] SQL injection prevention
- [ ] XSS protection
- [ ] CSRF tokens used

## Quality
- [ ] Functions < 50 lines
- [ ] DRY principle followed
- [ ] Clear naming conventions
- [ ] Error handling present
- [ ] Comments for complex logic

## Performance
- [ ] No N+1 queries
- [ ] Efficient algorithms
- [ ] Proper indexing
- [ ] Caching where appropriate
- [ ] No memory leaks
```

---

## Example 3: Script-Based Skill

**Use Case**: Skill sử dụng external scripts để xử lý

### Folder Structure
```
~/.claude/skills/data/
|---csv-processor/
    |---SKILL.md
    |---README.md
    |---scripts/
        |---process.py
        |---validate.py
        |---transform.py
```

### SKILL.md
```yaml
---
name: csv-processor
description: Process, validate, and transform CSV files with Python scripts. Use when working with CSV data, data cleaning, or user mentions CSV processing, data transformation, or tabular data manipulation.
---

# CSV Processor

## Level 1: Overview

Process CSV files using Python scripts for data cleaning, validation, and transformation.

## Prerequisites

- Python 3.7+
- pandas library: `pip install pandas`
- CSV files to process

## What This Skill Does

1. Validates CSV structure
2. Cleans and transforms data
3. Generates processed output
4. Creates summary reports

---

## Level 2: Quick Start

### Basic Usage

```bash
# 1. Validate CSV
python scripts/validate.py input.csv

# 2. Process data
python scripts/process.py input.csv --output cleaned.csv

# 3. Transform to different format
python scripts/transform.py cleaned.csv --format json
```

### Key Steps

1. Validate input CSV structure
2. Run processing script with options
3. Review output and summary
4. Transform to desired format if needed

---

## Common Use Cases

### Case 1: Data Cleaning
```bash
python scripts/process.py data.csv \
  --remove-duplicates \
  --fill-missing \
  --output clean_data.csv
```

### Case 2: Data Validation
```bash
python scripts/validate.py data.csv \
  --schema schema.json \
  --report validation_report.txt
```

### Case 3: Format Transformation
```bash
python scripts/transform.py data.csv \
  --format json \
  --output data.json
```

---

## Script Options

### process.py
- `--remove-duplicates`: Remove duplicate rows
- `--fill-missing`: Fill missing values
- `--normalize`: Normalize column names
- `--output FILE`: Output file path

### validate.py
- `--schema FILE`: JSON schema for validation
- `--report FILE`: Validation report output
- `--strict`: Strict validation mode

### transform.py
- `--format FORMAT`: Output format (json, xml, excel)
- `--output FILE`: Output file path
- `--pretty`: Pretty print output

---

## Best Practices

- Always validate before processing
- Backup original files
- Check summary reports
- Use appropriate options for task
- Test with small sample first

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Script not found | Check scripts/ directory exists |
| pandas missing | Run `pip install pandas` |
| Invalid CSV | Use validate.py to identify issues |
| Large files slow | Process in chunks with --chunk-size |
```

### scripts/process.py
```python
#!/usr/bin/env python3
import pandas as pd
import argparse

def process_csv(input_file, output_file, remove_duplicates=False, fill_missing=False):
    df = pd.read_csv(input_file)
    
    if remove_duplicates:
        df = df.drop_duplicates()
    
    if fill_missing:
        df = df.fillna('')
    
    df.to_csv(output_file, index=False)
    print(f"Processed: {len(df)} rows")

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('input', help='Input CSV file')
    parser.add_argument('--output', required=True, help='Output CSV file')
    parser.add_argument('--remove-duplicates', action='store_true')
    parser.add_argument('--fill-missing', action='store_true')
    args = parser.parse_args()
    
    process_csv(args.input, args.output, args.remove_duplicates, args.fill_missing)
```

---

## Example 4: Multi-File Skill với Progressive Disclosure

**Use Case**: Skill phức tạp với nhiều features, dùng separate files

### Folder Structure
```
~/.claude/skills/api/
|---rest-api-designer/
    |---SKILL.md
    |---README.md
    |---reference/
        |---endpoints.md
        |---authentication.md
        |---versioning.md
        |---best-practices.md
```

### SKILL.md
```yaml
---
name: rest-api-designer
description: Design RESTful APIs following industry best practices. Use when creating API endpoints, designing REST routes, API architecture, or user mentions REST API, API design, endpoints, or API documentation.
---

# REST API Designer

## Level 1: Overview

Design well-structured RESTful APIs with proper endpoints, authentication, versioning, and documentation.

## Prerequisites

- Understanding of HTTP methods
- Basic REST principles
- API framework chosen (Express, Flask, FastAPI, etc.)

## What This Skill Does

1. Design RESTful endpoint structure
2. Recommend authentication patterns
3. Implement API versioning
4. Generate OpenAPI/Swagger specs
5. Apply REST best practices

---

## Level 2: Quick Start

### Basic Workflow

1. Define resources (User, Product, Order, etc.)
2. Design CRUD endpoints for each resource
3. Add authentication and authorization
4. Implement versioning strategy
5. Document with OpenAPI spec

### Key Principles

- Use nouns for resources (not verbs)
- Use HTTP methods correctly (GET, POST, PUT, DELETE)
- Implement proper status codes
- Version your API from start
- Document everything

---

## Level 3: Reference Guides

- [endpoints.md](./reference/endpoints.md) - Use when: Designing endpoint structure
- [authentication.md](./reference/authentication.md) - Use when: Adding auth to API
- [versioning.md](./reference/versioning.md) - Use when: Planning API versions
- [best-practices.md](./reference/best-practices.md) - Use when: Need comprehensive guidelines

---

## Quick Example

```
GET    /api/v1/users          # List users
GET    /api/v1/users/:id      # Get user
POST   /api/v1/users          # Create user
PUT    /api/v1/users/:id      # Update user
DELETE /api/v1/users/:id      # Delete user

GET    /api/v1/users/:id/posts   # Get user's posts (nested resource)
```

---

## Common Use Cases

- Case 1: Design new REST API from scratch
- Case 2: Add authentication to existing API
- Case 3: Implement API versioning
- Case 4: Generate OpenAPI documentation
- Case 5: Refactor non-RESTful endpoints

---

## Best Practices

- Use plural nouns for collections
- Implement pagination for lists
- Use proper HTTP status codes
- Include rate limiting
- Provide detailed error messages
- Version API from day one
- Document with OpenAPI/Swagger

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Non-RESTful URLs | Use resource nouns, not action verbs |
| Inconsistent naming | Follow convention: /api/v1/resource-name |
| Missing versioning | Add /v1/ to all endpoints |
| Poor error messages | Return JSON with error code and message |
```

### reference/endpoints.md
```markdown
# Endpoint Design Guide

## Resource Naming

✅ **Good**:
- `/api/v1/users`
- `/api/v1/products`
- `/api/v1/orders`

❌ **Bad**:
- `/api/v1/getUsers` (verb in URL)
- `/api/v1/user` (singular)
- `/api/users` (no version)

## HTTP Methods

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Retrieve resource(s) | `GET /users` |
| POST | Create resource | `POST /users` |
| PUT | Update entire resource | `PUT /users/123` |
| PATCH | Update partial resource | `PATCH /users/123` |
| DELETE | Delete resource | `DELETE /users/123` |

## Nested Resources

```
GET /users/:userId/posts           # User's posts
GET /users/:userId/posts/:postId   # Specific post
POST /users/:userId/posts          # Create post for user
```

Limit nesting to 2 levels maximum.

## Query Parameters

```
GET /users?page=2&limit=20                    # Pagination
GET /products?category=electronics&sort=price  # Filtering & sorting
GET /search?q=laptop&fields=name,description  # Search & field selection
```

## Response Format

```json
{
  "data": [...],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150
  },
  "links": {
    "next": "/api/v1/users?page=2",
    "prev": null
  }
}
```
```

---

## Example 5: Skill với Assets và Templates

**Use Case**: Skill generate files từ templates

### Folder Structure
```
~/.claude/skills/documentation/
|---api-docs-generator/
    |---SKILL.md
    |---README.md
    |---assets/
        |---template-openapi.yaml
        |---template-readme.md
        |---template-changelog.md
```

### SKILL.md
```yaml
---
name: api-docs-generator
description: Generate API documentation from templates including OpenAPI specs, README, and CHANGELOG. Use when creating API docs, OpenAPI specs, or user mentions API documentation, swagger, or API reference.
---

# API Docs Generator

## Level 1: Overview

Generate comprehensive API documentation using pre-built templates for OpenAPI, README, and CHANGELOG.

---

## Level 2: Quick Start

### Template Usage

1. **OpenAPI Spec**: Use [template-openapi.yaml](./assets/template-openapi.yaml)
2. **README**: Use [template-readme.md](./assets/template-readme.md)
3. **CHANGELOG**: Use [template-changelog.md](./assets/template-changelog.md)

### Basic Workflow

1. Choose appropriate template
2. Fill in API-specific details
3. Customize sections as needed
4. Generate final documentation

---

## Common Use Cases

### Case 1: New API Documentation
- Copy template-openapi.yaml
- Fill in endpoints and schemas
- Add examples

### Case 2: Update CHANGELOG
- Copy template-changelog.md
- Add new version section
- Document changes

---

## Best Practices

- Keep OpenAPI spec up-to-date
- Document breaking changes clearly
- Include examples for all endpoints
- Version documentation with code
```

### assets/template-openapi.yaml
```yaml
openapi: 3.0.0
info:
  title: Your API Name
  version: 1.0.0
  description: API description here

servers:
  - url: https://api.example.com/v1

paths:
  /users:
    get:
      summary: List users
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
```

---

## Example 6: Data Processing Skill

**Use Case**: Complex data processing workflow

### Folder Structure
```
~/.claude/skills/data/
|---json-processor/
    |---SKILL.md
    |---README.md
    |---reference/
        |---transformations.md
        |---filters.md
    |---scripts/
        |---merge.py
        |---filter.py
        |---transform.py
```

### SKILL.md
```yaml
---
name: json-processor
description: Process, merge, filter, and transform JSON files with advanced operations. Use when working with JSON data, data merging, filtering, or user mentions JSON processing, data transformation, or JSON manipulation.
---

# JSON Processor

## Level 1: Overview

Advanced JSON processing with merge, filter, and transform capabilities using Python scripts.

## Prerequisites

- Python 3.7+
- jq (optional, for quick queries)
- JSON files to process

## What This Skill Does

1. Merge multiple JSON files
2. Filter data with conditions
3. Transform structure and values
4. Validate JSON schema
5. Generate reports

---

## Level 2: Quick Start

### Basic Operations

```bash
# Merge JSON files
python scripts/merge.py file1.json file2.json --output merged.json

# Filter data
python scripts/filter.py data.json --key "age" --op ">" --value 18

# Transform structure
python scripts/transform.py data.json --template transform.json
```

---

## Level 3: Reference Guides

- [transformations.md](./reference/transformations.md) - Use when: Need complex data transformations
- [filters.md](./reference/filters.md) - Use when: Filtering with complex conditions

---

## Common Use Cases

### Case 1: Merge API Responses
```bash
python scripts/merge.py \
  response1.json response2.json response3.json \
  --output combined.json \
  --strategy deep
```

### Case 2: Filter Large Dataset
```bash
python scripts/filter.py users.json \
  --key "status" --op "==" --value "active" \
  --key "age" --op ">=" --value 18 \
  --output active_adults.json
```

### Case 3: Transform Structure
```bash
python scripts/transform.py legacy_data.json \
  --template new_schema.json \
  --output migrated_data.json
```

---

## Best Practices

- Validate JSON before processing
- Backup original files
- Use appropriate merge strategy
- Test transformations on small sample
- Document custom transformations

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Invalid JSON | Use `jq .` to validate |
| Merge conflicts | Use --strategy deep or shallow |
| Large files | Process in streaming mode |
| Transform errors | Check template syntax |
```

---

## Summary

Các examples trên cover:

1. **Simple skill** - Tất cả trong SKILL.md
2. **Read-only skill** - Với `allowed-tools` restriction
3. **Script-based skill** - Sử dụng external scripts
4. **Multi-file skill** - Progressive disclosure với reference files
5. **Template skill** - Generate từ assets/templates
6. **Data processing skill** - Complex workflows với scripts và references

**Key Patterns**:
- Level 1/2/3 structure cho progressive disclosure
- Clear trigger words trong description
- Concrete examples với real commands
- Reference files cho advanced topics
- Scripts với proper documentation
- Assets cho templates và boilerplate

Chọn pattern phù hợp với complexity và requirements của skill bạn đang tạo.
