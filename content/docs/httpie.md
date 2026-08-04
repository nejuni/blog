--- 
title: "httpie"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:13:52Z
tags: ["Download", "Testing"]
draft: false
---

# HTTPie CLI - Complete Guide for Termux (2025)

**HTTPie** is a modern, user-friendly command-line HTTP client for making API requests and testing web services. This guide covers the most useful commands for daily use in Termux.

**Official Resources:**
- Website: https://httpie.io
- GitHub: https://github.com/httpie/cli
- Documentation: https://httpie.io/docs/cli

---

## Table of Contents
1. [Basic Syntax](#basic-syntax)
2. [HTTP Methods](#http-methods)
3. [Request Items](#request-items)
4. [Headers & Authentication](#headers--authentication)
5. [Request Body](#request-body)
6. [File Operations](#file-operations)
7. [Sessions](#sessions)
8. [Output Options](#output-options)
9. [Advanced Features](#advanced-features)
10. [Common Use Cases](#common-use-cases)

---

## Basic Syntax

```bash
http [flags] [METHOD] URL [REQUEST_ITEM ...]
```

**Structure:**
- `METHOD`: GET, POST, PUT, DELETE, PATCH, etc.
- `URL`: The endpoint to hit
- `REQUEST_ITEM`: Headers, data, query params, etc.

---

## HTTP Methods

### GET Request (Default)
```bash
# Simple GET
http GET httpbin.org/get

# GET is default, so you can omit it
http httpbin.org/get

# GET with query parameters
http GET httpbin.org/get?name==John&age==25

# Alternative query param syntax
http GET httpbin.org/get name==John age==25
```

### POST Request
```bash
# POST with JSON data
http POST httpbin.org/post name=John email=john@example.com

# POST with explicit JSON
http POST httpbin.org/post name="John Doe" age:=30 active:=true
```

### PUT Request
```bash
# Update resource
http PUT httpbin.org/put id:=1 name="Updated Name"
```

### DELETE Request
```bash
# Delete resource
http DELETE httpbin.org/delete

# Delete with authorization
http DELETE api.example.com/users/123 Authorization:"Bearer token123"
```

### PATCH Request
```bash
# Partial update
http PATCH httpbin.org/patch name="New Name"
```

### HEAD Request
```bash
# Get headers only
http HEAD httpbin.org/get
```

---

## Request Items

HTTPie uses special syntax for different request items:

| Syntax | Type | Example |
|--------|------|---------|
| `==` | Query parameter | `name==John` → `?name=John` |
| `=` | JSON string field | `name=John` → `{"name": "John"}` |
| `:=` | JSON non-string field | `age:=25` → `{"age": 25}` |
| `@` | File field | `file@~/doc.pdf` |
| `:` | Header | `User-Agent:Custom` |
| `=@` | Data from file | `data=@file.json` |
| `:=@` | JSON from file | `config:=@config.json` |

---

## Headers & Authentication

### Custom Headers
```bash
# Single header
http httpbin.org/headers User-Agent:MyApp/1.0

# Multiple headers
http httpbin.org/headers \
    User-Agent:MyApp/1.0 \
    Accept:application/json \
    X-Custom-Header:value
```

### Authentication

**Basic Auth:**
```bash
# Format: http -a username:password
http -a john:secret123 httpbin.org/basic-auth/john/secret123

# Prompt for password (more secure)
http -a john httpbin.org/basic-auth/john/secret123
```

**Bearer Token:**
```bash
http httpbin.org/bearer Authorization:"Bearer eyJhbGc..."
```

**Digest Auth:**
```bash
http --auth-type=digest -a username:password httpbin.org/digest-auth/auth/username/password
```

---

## Request Body

### JSON (Default)
```bash
# Simple JSON
http POST httpbin.org/post name=John age:=30

# Nested JSON
http POST httpbin.org/post \
    user[name]=John \
    user[email]=john@example.com \
    user[age]:=30

# Boolean and null
http POST httpbin.org/post \
    active:=true \
    inactive:=false \
    data:=null
```

### Form Data
```bash
# URL-encoded form (--form or -f)
http --form POST httpbin.org/post name=John email=john@example.com

# Multipart form
http --multipart POST httpbin.org/post name=John file@~/photo.jpg
```

### Raw Body
```bash
# Send raw text
echo 'Hello World' | http POST httpbin.org/post

# Send from file
http POST httpbin.org/post < data.json
```

---

## File Operations

### Upload Files
```bash
# Single file upload
http --form POST httpbin.org/post file@~/document.pdf

# Multiple files
http --multipart POST httpbin.org/post \
    file1@~/doc1.pdf \
    file2@~/doc2.pdf \
    description="My files"

# File with custom filename
http --form POST httpbin.org/post file@~/doc.pdf;filename=custom.pdf
```

### Download Files
```bash
# Download and save (preserves filename)
http --download httpbin.org/image/png

# Download with custom filename
http --output photo.png httpbin.org/image/png

# Resume download
http --download --continue httpbin.org/large-file.zip
```

### Read from File
```bash
# JSON from file
http POST httpbin.org/post < data.json

# Use file content as field value
http POST httpbin.org/post data=@file.txt

# JSON field from file
http POST httpbin.org/post config:=@config.json
```

---

## Sessions

Sessions persist cookies, headers, and auth across requests.

### Named Sessions
```bash
# Create/use session
http --session=mysession httpbin.org/cookies/set?token=abc123

# Reuse session (cookies are automatically included)
http --session=mysession httpbin.org/cookies

# Different session per host
http --session=github-session api.github.com/user

# Read-only session (don't update)
http --session-read-only=mysession httpbin.org/get
```

### Session Location
Sessions are stored in:
```
~/.config/httpie/sessions/<host>/<session-name>.json
```

---

## Output Options

### Control Output
```bash
# Print request and response headers + body (default)
http httpbin.org/get

# Print only response body
http --body httpbin.org/get

# Print only response headers
http --headers httpbin.org/get

# Print request and response headers only
http --verbose httpbin.org/get

# Print everything including SSL connection info
http --verbose --all httpbin.org/get

# Quiet mode (success/failure only)
http --quiet httpbin.org/get
```

### Custom Print Options (-p flag)
```bash
# H: request headers, B: request body
# h: response headers, b: response body

# Print request headers and response body
http -p Hb httpbin.org/get

# Print only request and response headers
http -p Hh httpbin.org/get

# Print everything
http -p HhBb httpbin.org/post name=John
```

### Pretty Print & Colors
```bash
# Format and colorize (default)
http httpbin.org/get

# No colors
http --pretty=none httpbin.org/get

# Different color schemes
http --style=monokai httpbin.org/get
http --style=fruity httpbin.org/get

# Available styles: auto, solarized, monokai, fruity, etc.
```

### Output Formatting
```bash
# JSON sorting
http --sorted httpbin.org/json

# Unsorted output
http --unsorted httpbin.org/json
```

---

## Advanced Features

### Follow Redirects
```bash
# Follow redirects (up to 30 by default)
http --follow httpbin.org/redirect/3

# Limit redirect depth
http --follow --max-redirects=5 httpbin.org/redirect/10
```

### Timeout
```bash
# Set timeout (in seconds)
http --timeout=5 httpbin.org/delay/10

# Connection timeout only
http --timeout=2.5 httpbin.org/get
```

### Proxy
```bash
# HTTP proxy
http --proxy=http:http://proxy.example.com:8080 httpbin.org/get

# HTTPS proxy
http --proxy=https:https://proxy.example.com:8080 https://httpbin.org/get

# Use system proxy settings
http httpbin.org/get
```

### SSL/TLS Options
```bash
# Ignore SSL verification (dangerous!)
http --verify=no https://self-signed.example.com

# Custom CA certificate
http --verify=/path/to/ca-cert.pem https://example.com

# Client certificate
http --cert=/path/to/client.pem https://example.com

# Client cert with separate key
http --cert=/path/to/client.crt --cert-key=/path/to/client.key https://example.com
```

### Custom Method
```bash
# Custom HTTP method
http OPTIONS httpbin.org/get

# Really custom method
http BREW httpbin.org/get
```

---

## Common Use Cases

### 1. **Testing REST APIs**
```bash
# Create user
http POST api.example.com/users \
    name="John Doe" \
    email="john@example.com" \
    age:=30

# Get user
http GET api.example.com/users/123

# Update user
http PUT api.example.com/users/123 \
    name="John Updated"

# Delete user
http DELETE api.example.com/users/123
```

### 2. **GitHub API**
```bash
# Get your profile (with token)
http https://api.github.com/user \
    Authorization:"Bearer ghp_yourtoken"

# Create a repository
http POST https://api.github.com/user/repos \
    Authorization:"Bearer ghp_yourtoken" \
    name="my-new-repo" \
    private:=true

# List repositories
http https://api.github.com/users/username/repos
```

### 3. **Working with JSON APIs**
```bash
# POST complex JSON
http POST api.example.com/data \
    user[name]="John" \
    user[email]="john@example.com" \
    user[preferences][theme]="dark" \
    user[preferences][notifications]:=true \
    tags:='["developer", "python"]'

# Pretty print response
http GET api.example.com/data | jq .
```

### 4. **File Upload/Download**
```bash
# Upload image
http --form POST api.example.com/upload \
    image@~/photo.jpg \
    title="My Photo" \
    Authorization:"Bearer token123"

# Download file with progress
http --download https://example.com/file.zip

# Resume interrupted download
http --download --continue https://example.com/largefile.zip
```

### 5. **Testing Authentication**
```bash
# Test basic auth
http -a username:password api.example.com/protected

# Test OAuth token
http api.example.com/me \
    Authorization:"Bearer access_token_here"

# Test with cookies
http --session=auth api.example.com/login username=john password=secret
http --session=auth api.example.com/dashboard
```

### 6. **Debugging Requests**
```bash
# See full request and response
http --verbose POST httpbin.org/post name=John

# See timing information
http --print=HhBb --verbose httpbin.org/get

# Test CORS
http OPTIONS https://api.example.com \
    Origin:https://myapp.com \
    Access-Control-Request-Method:POST
```

### 7. **Working with Query Parameters**
```bash
# Search with filters
http GET api.example.com/products \
    category==electronics \
    price_min==100 \
    price_max==500 \
    sort==price \
    order==asc

# Pagination
http GET api.example.com/users \
    page==2 \
    limit==50
```

### 8. **GraphQL Queries**
```bash
# GraphQL query
http POST https://api.example.com/graphql \
    query='{ user(id: "123") { name email } }'

# GraphQL with variables
http POST https://api.example.com/graphql \
    query='query GetUser($id: ID!) { user(id: $id) { name } }' \
    variables:='{"id": "123"}'
```

### 9. **Webhooks Testing**
```bash
# Send webhook payload
http POST https://webhook.site/your-unique-url \
    event="user.created" \
    timestamp:=$(date +%s) \
    data[user_id]:=123 \
    data[name]="John Doe"
```

### 10. **Health Checks**
```bash
# Quick health check
http --check-status https://api.example.com/health

# Get only status code
http --print= https://api.example.com/ping

# Check multiple endpoints
for endpoint in /api/v1 /api/v2 /health; do
    echo "Testing $endpoint"
    http --check-status https://example.com$endpoint
done
```

---

## Quick Reference

### Most Used Flags
```bash
-j, --json          Force JSON content type
-f, --form          URL-encoded form data
-m, --multipart     Multipart form data
-v, --verbose       Print request headers
-h, --headers       Print response headers only
-b, --body          Print response body only
-d, --download      Download mode
-o, --output FILE   Save to file
-a, --auth USER:PASS  Authentication
-A, --auth-type     Auth type (basic, digest, bearer)
--session=NAME      Named session
--follow            Follow redirects
--timeout=SECONDS   Request timeout
--verify=no         Skip SSL verification
--proxy=PROTOCOL:URL  Use proxy
--print=WHAT        Custom print (-p HhBb)
--style=STYLE       Color scheme
--sorted            Sort JSON keys
--check-status      Exit with error on 4xx/5xx
```

### Exit Codes
- `0`: Success
- `1`: Generic error
- `2`: Request timeout
- `3`: Connection error
- `4`: Request/response processing error
- `5`: HTTP 3xx (redirect) with `--check-status`
- `6`: HTTP 4xx (client error)
- `7`: HTTP 5xx (server error)

---

## Tips for Termux

1. **Storage Access:**
```bash
# If accessing files in shared storage
termux-setup-storage
http --form POST example.com file@~/storage/downloads/file.pdf
```

2. **Clipboard Integration:**
```bash
# Copy response to clipboard
http httpbin.org/json | termux-clipboard-set

# POST from clipboard
termux-clipboard-get | http POST httpbin.org/post
```

3. **Aliases (add to ~/.bashrc):**
```bash
alias get='http GET'
alias post='http POST'
alias put='http PUT'
alias delete='http DELETE'
alias api='http --session=api'
```

4. **Environment Variables:**
```bash
# Set default HTTPie config
export HTTPIE_CONFIG_DIR=~/.config/httpie

# Default options
export HTTPIE_DEFAULT_OPTIONS='--timeout=30 --follow'
```

---

## Configuration File

Create `~/.config/httpie/config.json`:

```json
{
    "default_options": [
        "--timeout=30",
        "--style=monokai"
    ],
    "implicit_content_type": "json"
}
```

---

## Troubleshooting

**SSL Certificate Errors:**
```bash
# Update CA certificates in Termux
pkg update
pkg install ca-certificates

# Or bypass (not recommended)
http --verify=no https://example.com
```

**Python/HTTPie Issues:**
```bash
# Reinstall HTTPie
pip install --upgrade httpie
```

**Permission Denied:**
```bash
# Check file permissions
chmod +r file.txt
http POST example.com data=@file.txt
```

---

## Resources

- **Official Docs:** https://httpie.io/docs/cli
- **GitHub:** https://github.com/httpie/cli
- **Issues:** https://github.com/httpie/cli/issues
- **Community:** https://httpie.io/discord

---

**Version Info (as of 2025):**
- HTTPie CLI: Latest stable version supports HTTP/2, improved sessions, and enhanced formatting
- Maintained by HTTPie, Inc.
- Active development with regular updates

**Source:** Official HTTPie documentation at httpie.io/docs/cli
```

---

# Explanation & Examples

## How HTTPie Works

HTTPie uses an intuitive syntax that's more human-friendly than curl:

### 1. **Request Items Syntax**

The magic of HTTPie is in how it interprets different operators:

**Query Parameters (`==`):**
```bash
http GET example.com/search q==python lang==en
# Becomes: example.com/search?q=python&lang=en
```

**JSON Fields (`=` and `:=`):**
```bash
http POST example.com name=John age:=30 active:=true
# Sends: {"name": "John", "age": 30, "active": true}
```

The difference:
- `=` treats the value as a string
- `:=` treats the value as raw JSON (numbers, booleans, objects)

### 2. **Headers (`:`):**
```bash
http example.com User-Agent:MyApp X-API-Key:secret123
# Adds these as HTTP headers
```

### 3. **Automatic JSON**

HTTPie automatically assumes JSON for POST/PUT/PATCH:
```bash
http POST api.example.com name=John
# Content-Type: application/json is automatic
# Body: {"name": "John"}
```

For forms, use `--form`:
```bash
http --form POST api.example.com name=John
# Content-Type: application/x-www-form-urlencoded
# Body: name=John
```

## Real-World Examples

### Example 1: Building a TODO App API Test

```bash
# 1. Create a todo
http POST https://jsonplaceholder.typicode.com/todos \
    userId:=1 \
    title="Learn HTTPie" \
    completed:=false

# Response:
# {
#   "userId": 1,
#   "id": 201,
#   "title": "Learn HTTPie",
#   "completed": false
# }

# 2. Get all todos
http GET https://jsonplaceholder.typicode.com/todos

# 3. Get specific todo
http GET https://jsonplaceholder.typicode.com/todos/1

# 4. Update todo
http PUT https://jsonplaceholder.typicode.com/todos/1 \
    userId:=1 \
    title="Learn HTTPie - Completed!" \
    completed:=true

# 5. Delete todo
http DELETE https://jsonplaceholder.typicode.com/todos/1
```

### Example 2: GitHub API Workflow

```bash
# Set your token as variable (in Termux)
TOKEN="ghp_your_github_token_here"

# 1. Get your profile
http https://api.github.com/user \
    Authorization:"Bearer $TOKEN"

# 2. List your repositories
http https://api.github.com/user/repos \
    Authorization:"Bearer $TOKEN" \
    sort==updated \
    per_page==5

# 3. Create a new repository
http POST https://api.github.com/user/repos \
    Authorization:"Bearer $TOKEN" \
    name="test-repo" \
    description="Created with HTTPie" \
    private:=false

# 4. Create an issue
http POST https://api.github.com/repos/username/repo/issues \
    Authorization:"Bearer $TOKEN" \
    title="Test Issue" \
    body="This issue was created using HTTPie"
```

### Example 3: Weather API

```bash
# OpenWeatherMap API
API_KEY="your_api_key"

# Get current weather
http GET "api.openweathermap.org/data/2.5/weather" \
    q=="London" \
    appid==$API_KEY \
    units==metric

# Get forecast
http GET "api.openweathermap.org/data/2.5/forecast" \
    q=="London" \
    appid==$API_KEY \
    units==metric \
    cnt==5
```

### Example 4: File Upload with Progress

```bash
# Upload profile picture
http --form POST api.example.com/profile/avatar \
    Authorization:"Bearer token123" \
    avatar@~/storage/dcim/photo.jpg \
    description="My new avatar"

# Upload multiple files
http --multipart POST api.example.com/gallery \
    photos[]=@~/photo1.jpg \
    photos[]=@~/photo2.jpg \
    photos[]=@~/photo3.jpg \
    album="Summer 2025"
```

### Example 5: Session Management

```bash
# 1. Login and save session
http --session=myapp POST api.example.com/login \
    username=john \
    password=secret123

# 2. Make authenticated requests (session auto-includes cookies)
http --session=myapp GET api.example.com/dashboard

# 3. Update profile
http --session=myapp PUT api.example.com/profile \
    name="John Updated" \
    email="newemail@example.com"

# 4. Logout
http --session=myapp POST api.example.com/logout
```

## Why HTTPie is Better than Curl for API Testing

**Curl:**
```bash
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer token123" \
  -d '{"name":"John","age":30,"active":true}'
```

**HTTPie:**
```bash
http POST api.example.com/users \
    Authorization:"Bearer token123" \
    name=John \
    age:=30 \
    active:=true
```

HTTPie:
- Auto-formats JSON
- Colorized output
- Automatic Content-Type
- More readable syntax
- Built-in JSON parsing

## Citations

1. HTTPie Official Documentation: https://httpie.io/docs/cli
2. HTTPie GitHub Repository: https://github.com/httpie/cli
3. HTTPie CLI Usage Guide: https://httpie.io/docs/cli/usage
4. HTTPie Examples: https://httpie.io/docs/cli/examples

All information has been compiled from official HTTPie sources and is current as of October 2025.