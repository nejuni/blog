--- 
title: "curl"
description: "readme"
date: 2025-10-28T14:03:52Z
lastmod: 2025-10-28T14:03:52Z
tags: ["Download", "Testing"]
draft: false
---

# 🌐 cURL - Complete Guide for Termux (2025)

## Table of Contents
- [About cURL](#about-curl)
- [Basic Usage](#basic-usage)
- [Essential Daily Commands](#essential-daily-commands)
- [HTTP Methods](#http-methods)
- [API Development](#api-development)
- [Advanced Features](#advanced-features)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)

---

## About cURL

**cURL** (Client URL) is a command-line tool for transferring data with URLs. It supports numerous protocols and is the industry standard for API testing and data transfer.

- **Official Website**: https://curl.se/
- **Version**: 8.5.0+ (latest as of 2025)
- **License**: MIT-like (curl license)
- **Maintainer**: Daniel Stenberg & community

### Supported Protocols
HTTP, HTTPS, FTP, FTPS, SCP, SFTP, TFTP, DICT, TELNET, LDAP, LDAPS, MQTT, FILE, IMAP, IMAPS, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, GOPHER, GOPHERS

### Key Features
- Support for 25+ protocols
- HTTP/2 and HTTP/3 support
- SSL/TLS with multiple backends
- Cookie support
- Proxy support (HTTP, HTTPS, SOCKS4, SOCKS5)
- Form submission (multipart/form-data)
- File upload/download
- Authentication (Basic, Digest, NTLM, Negotiate)
- Resume capability
- Rate limiting
- IPv6 support

### cURL vs Alternatives

| Feature | cURL | wget | aria2 |
|---------|------|------|-------|
| API testing | ✅ Best | ❌ | ❌ |
| POST/PUT/DELETE | ✅ | ❌ | ❌ |
| Multiple protocols | ✅ 25+ | ⚠️ 3 | ⚠️ 5 |
| Recursive download | ❌ | ✅ | ❌ |
| Multiple connections | ❌ | ❌ | ✅ |
| JSON handling | ✅ | ❌ | ❌ |
| Headers control | ✅ Best | ⚠️ Limited | ⚠️ Limited |

---

## Basic Usage

### Simple Download
```bash
# Download a file
curl https://example.com/file.zip -o file.zip

# Download with automatic filename
curl -O https://example.com/file.zip

# Download and show progress
curl -# https://example.com/file.zip -o file.zip

# -o: output to file
# -O: save with remote filename
# -#: progress bar
```

### Check curl version
```bash
curl --version
```

---

## Essential Daily Commands

### 1. **Simple GET Request**
```bash
# Basic GET request (display in terminal)
curl https://example.com

# Save output to file
curl https://example.com -o page.html

# Save with remote filename
curl -O https://example.com/file.zip

# Silent mode (no progress)
curl -s https://example.com

# -s: silent mode
# -o: output file
# -O: use remote filename
```

**Example:**
```bash
# Get JSON data from API
curl https://api.github.com/users/torvalds
```

---

### 2. **Download Files**
```bash
# Download with progress bar
curl -# -O https://example.com/file.zip

# Download to specific location
curl https://example.com/file.zip -o /sdcard/Download/file.zip

# Download multiple files
curl -O https://example.com/file1.zip -O https://example.com/file2.zip

# Download with custom filename
curl https://example.com/data -o mydata.json
```

**Example:**
```bash
curl -# -o /sdcard/Download/ubuntu.iso https://releases.ubuntu.com/22.04/ubuntu-22.04-desktop-amd64.iso
```

---

### 3. **Resume Interrupted Downloads**
```bash
# Resume download
curl -C - -O https://example.com/largefile.iso

# Resume with specific byte offset
curl -C 1024000 -O https://example.com/largefile.iso

# -C -: automatically detect where to resume
# -C <offset>: resume from specific byte
```

**Example:**
```bash
# Download interrupted, resume it
curl -C - -# -o movie.mp4 https://example.com/movie.mp4
```

---

### 4. **POST Request with Data**
```bash
# POST with form data
curl -X POST -d "name=John&email=john@example.com" https://example.com/api/users

# POST with JSON data
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"name":"John","email":"john@example.com"}' \
     https://example.com/api/users

# POST from file
curl -X POST -d @data.json -H "Content-Type: application/json" https://example.com/api

# -X: HTTP method
# -d: data to send
# -H: custom header
# @file: read data from file
```

**Example:**
```bash
# Create new user via API
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"username":"john","password":"secret123"}' \
     https://api.example.com/register
```

---

### 5. **Custom Headers**
```bash
# Add single header
curl -H "Authorization: Bearer TOKEN123" https://api.example.com/data

# Add multiple headers
curl -H "Authorization: Bearer TOKEN123" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     https://api.example.com/data

# User-Agent
curl -A "Mozilla/5.0" https://example.com

# Referer
curl -e "https://google.com" https://example.com

# -H: add header
# -A: user agent
# -e: referer
```

**Example:**
```bash
# Access protected API endpoint
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
     -H "Accept: application/json" \
     https://api.example.com/protected/data
```

---

### 6. **Authentication**
```bash
# Basic Authentication
curl -u username:password https://example.com/protected

# Bearer Token
curl -H "Authorization: Bearer TOKEN" https://api.example.com

# Digest Authentication
curl --digest -u username:password https://example.com

# NTLM Authentication
curl --ntlm -u username:password https://example.com

# OAuth 2.0
curl -H "Authorization: Bearer ACCESS_TOKEN" https://api.example.com

# -u: username:password
```

**Example:**
```bash
# Login to API
curl -u "john:secret123" https://api.example.com/login

# Or prompt for password (more secure)
curl -u john https://api.example.com/login
# Enter host password for user 'john': [type password]
```

---

### 7. **Follow Redirects**
```bash
# Follow redirects
curl -L https://short.link/abc

# Follow with max redirects
curl -L --max-redirs 5 https://example.com

# Show redirect chain
curl -L -v https://short.link/abc

# -L: follow redirects
# --max-redirs: maximum number of redirects
```

**Example:**
```bash
# Download from shortened URL
curl -L -O https://bit.ly/example-file
```

---

### 8. **View Response Headers**
```bash
# Show headers only
curl -I https://example.com

# Show headers with body
curl -i https://example.com

# Verbose output (everything)
curl -v https://example.com

# Very verbose (with timing)
curl -v --trace-time https://example.com

# -I: HEAD request (headers only)
# -i: include headers in output
# -v: verbose mode
```

**Example:**
```bash
# Check if website is up
curl -I https://example.com

# Output:
# HTTP/2 200
# content-type: text/html
# ...
```

---

### 9. **Upload Files**
```bash
# Upload file (POST)
curl -F "file=@document.pdf" https://example.com/upload

# Upload with additional fields
curl -F "file=@photo.jpg" -F "description=My Photo" https://example.com/upload

# Upload multiple files
curl -F "file1=@doc1.pdf" -F "file2=@doc2.pdf" https://example.com/upload

# PUT method upload
curl -T file.txt https://example.com/upload

# -F: multipart form data
# -T: transfer file (PUT)
# @: read from file
```

**Example:**
```bash
# Upload image to API
curl -X POST \
     -H "Authorization: Bearer TOKEN" \
     -F "image=@/sdcard/Pictures/photo.jpg" \
     -F "caption=Beautiful sunset" \
     https://api.example.com/photos
```

---

### 10. **Cookies**
```bash
# Save cookies to file
curl -c cookies.txt https://example.com/login

# Load cookies from file
curl -b cookies.txt https://example.com/dashboard

# Both save and load
curl -b cookies.txt -c cookies.txt https://example.com

# Send specific cookie
curl -b "session=abc123" https://example.com

# -c: write cookies to file
# -b: read cookies from file/string
```

**Example:**
```bash
# Login and save session
curl -c cookies.txt \
     -d "username=john&password=secret" \
     https://example.com/login

# Access protected page
curl -b cookies.txt https://example.com/dashboard
```

---

### 11. **Limit Speed**
```bash
# Limit download speed to 1MB/s
curl --limit-rate 1M https://example.com/file.zip -o file.zip

# Limit to 500KB/s
curl --limit-rate 500K -O https://example.com/file.zip

# Limit to 100KB/s (mobile data saving)
curl --limit-rate 100K -# -O https://example.com/largefile.iso
```

**Example:**
```bash
# Download in background with speed limit
curl --limit-rate 500K -# -o movie.mp4 https://example.com/movie.mp4 &
```

---

### 12. **Timeout Settings**
```bash
# Connection timeout (30 seconds)
curl --connect-timeout 30 https://example.com

# Max time for entire operation
curl --max-time 300 https://example.com/largefile.zip -o file.zip

# Combined
curl --connect-timeout 30 --max-time 600 -O https://example.com/file.zip

# --connect-timeout: max time for connection
# --max-time: max time for entire operation
```

**Example:**
```bash
# Download with timeout (10 minutes max)
curl --connect-timeout 30 \
     --max-time 600 \
     -# -O https://example.com/largefile.iso
```

---

### 13. **Proxy Usage**
```bash
# HTTP Proxy
curl -x http://proxy.example.com:8080 https://example.com

# SOCKS5 Proxy
curl -x socks5://127.0.0.1:1080 https://example.com

# Proxy with authentication
curl -x http://proxy.example.com:8080 -U proxyuser:proxypass https://example.com

# No proxy for specific domains
curl --noproxy localhost,127.0.0.1 https://example.com

# -x: proxy server
# -U: proxy authentication
```

**Example:**
```bash
curl -x socks5://127.0.0.1:1080 \
     -H "User-Agent: Mozilla/5.0" \
     https://example.com
```

---

### 14. **Retry Options**
```bash
# Retry on failure (5 times)
curl --retry 5 https://example.com/file.zip -o file.zip

# Retry with delay
curl --retry 5 --retry-delay 10 https://example.com

# Retry on specific HTTP codes
curl --retry 5 --retry-all-errors https://example.com

# Retry max time
curl --retry 5 --retry-max-time 60 https://example.com

# --retry: number of retries
# --retry-delay: wait between retries
# --retry-all-errors: retry on any error
```

**Example:**
```bash
# Download from unreliable server
curl --retry 10 \
     --retry-delay 5 \
     --retry-all-errors \
     -# -C - -O https://unstable.example.com/file.zip
```

---

### 15. **JSON Pretty Print**
```bash
# Get JSON and format it
curl -s https://api.example.com/data | jq

# Get specific field
curl -s https://api.example.com/users/1 | jq '.name'

# Pretty print without jq (using python)
curl -s https://api.example.com/data | python -m json.tool

# Save formatted JSON
curl -s https://api.example.com/data | jq > data.json
```

**Example:**
```bash
# Install jq first
pkg install jq

# Get and format GitHub user data
curl -s https://api.github.com/users/torvalds | jq
```

---

## HTTP Methods

### GET Request
```bash
# Simple GET
curl https://api.example.com/users

# GET with query parameters
curl "https://api.example.com/users?page=1&limit=10"

# GET with headers
curl -H "Accept: application/json" https://api.example.com/users
```

---

### POST Request
```bash
# POST form data
curl -X POST -d "name=John&age=30" https://api.example.com/users

# POST JSON
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"name":"John","age":30}' \
     https://api.example.com/users

# POST from file
curl -X POST -d @user.json -H "Content-Type: application/json" https://api.example.com/users

# POST with multipart form
curl -X POST -F "name=John" -F "file=@photo.jpg" https://api.example.com/users
```

---

### PUT Request
```bash
# PUT (update resource)
curl -X PUT \
     -H "Content-Type: application/json" \
     -d '{"name":"John Updated","age":31}' \
     https://api.example.com/users/1

# PUT from file
curl -X PUT -d @updated-user.json -H "Content-Type: application/json" https://api.example.com/users/1
```

---

### PATCH Request
```bash
# PATCH (partial update)
curl -X PATCH \
     -H "Content-Type: application/json" \
     -d '{"age":32}' \
     https://api.example.com/users/1
```

---

### DELETE Request
```bash
# DELETE resource
curl -X DELETE https://api.example.com/users/1

# DELETE with authentication
curl -X DELETE \
     -H "Authorization: Bearer TOKEN" \
     https://api.example.com/users/1
```

---

### HEAD Request
```bash
# HEAD (get headers only)
curl -I https://example.com

# Check if resource exists
curl -I https://example.com/file.zip
```

---

### OPTIONS Request
```bash
# OPTIONS (check supported methods)
curl -X OPTIONS -i https://api.example.com/users
```

---

## API Development

### Testing REST API

**1. Create Resource (POST)**
```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -d '{
       "title": "New Post",
       "body": "This is the content",
       "userId": 1
     }' \
     https://jsonplaceholder.typicode.com/posts
```

**2. Read Resource (GET)**
```bash
# Get all
curl https://jsonplaceholder.typicode.com/posts

# Get specific
curl https://jsonplaceholder.typicode.com/posts/1

# With query parameters
curl "https://jsonplaceholder.typicode.com/posts?userId=1"
```

**3. Update Resource (PUT)**
```bash
curl -X PUT \
     -H "Content-Type: application/json" \
     -d '{
       "id": 1,
       "title": "Updated Post",
       "body": "Updated content",
       "userId": 1
     }' \
     https://jsonplaceholder.typicode.com/posts/1
```

**4. Partial Update (PATCH)**
```bash
curl -X PATCH \
     -H "Content-Type: application/json" \
     -d '{"title": "Patched Title"}' \
     https://jsonplaceholder.typicode.com/posts/1
```

**5. Delete Resource (DELETE)**
```bash
curl -X DELETE https://jsonplaceholder.typicode.com/posts/1
```

---

### Working with JSON

**Send JSON**
```bash
# Inline JSON
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"key":"value"}' \
     https://api.example.com/endpoint

# From file
curl -X POST \
     -H "Content-Type: application/json" \
     -d @data.json \
     https://api.example.com/endpoint

# From heredoc
curl -X POST \
     -H "Content-Type: application/json" \
     -d @- https://api.example.com/endpoint << EOF
{
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
EOF
```

**Parse JSON Response**
```bash
# Install jq
pkg install jq

# Get specific field
curl -s https://api.github.com/users/torvalds | jq '.name'
# Output: "Linus Torvalds"

# Get multiple fields
curl -s https://api.github.com/users/torvalds | jq '{name, location, followers}'

# Filter array
curl -s https://api.github.com/users/torvalds/repos | jq '.[].name'

# Conditional filtering
curl -s https://api.github.com/users/torvalds/repos | jq '.[] | select(.stargazers_count > 1000) | .name'
```

---

### API Authentication Examples

**Bearer Token**
```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5..." \
     https://api.example.com/protected
```

**API Key in Header**
```bash
curl -H "X-API-Key: your-api-key-here" \
     https://api.example.com/data
```

**API Key in Query**
```bash
curl "https://api.example.com/data?api_key=your-api-key-here"
```

**OAuth 2.0**
```bash
# Get access token
TOKEN=$(curl -X POST \
     -d "grant_type=client_credentials&client_id=YOUR_ID&client_secret=YOUR_SECRET" \
     https://oauth.example.com/token | jq -r '.access_token')

# Use token
curl -H "Authorization: Bearer $TOKEN" \
     https://api.example.com/data
```

**JWT Authentication**
```bash
# Login and get JWT
JWT=$(curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"username":"user","password":"pass"}' \
     https://api.example.com/login | jq -r '.token')

# Use JWT
curl -H "Authorization: Bearer $JWT" \
     https://api.example.com/protected
```

---

### GraphQL Queries

```bash
# Simple GraphQL query
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{
       "query": "{ user(id: 1) { name email } }"
     }' \
     https://api.example.com/graphql

# With variables
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{
       "query": "query GetUser($id: ID!) { user(id: $id) { name email } }",
       "variables": {"id": "1"}
     }' \
     https://api.example.com/graphql

# Mutation
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{
       "query": "mutation { createUser(name: \"John\", email: \"john@example.com\") { id name } }"
     }' \
     https://api.example.com/graphql
```

---

## Advanced Features

### Parallel Downloads
```bash
# Download multiple files in parallel
curl -Z \
     -O https://example.com/file1.zip \
     -O https://example.com/file2.zip \
     -O https://example.com/file3.zip

# Or using background jobs
curl -O https://example.com/file1.zip &
curl -O https://example.com/file2.zip &
curl -O https://example.com/file3.zip &
wait
```

---

### Download URL List
```bash
# From file (one URL per line)
xargs -n 1 curl -O < urls.txt

# With progress
cat urls.txt | while read url; do
    echo "Downloading: $url"
    curl -# -O "$url"
done

# Parallel download from list
cat urls.txt | xargs -P 4 -n 1 curl -O
# -P 4: run 4 parallel processes
```

---

### FTP Operations

**Download from FTP**
```bash
# Anonymous FTP
curl -O ftp://ftp.example.com/file.zip

# With authentication
curl -u username:password -O ftp://ftp.example.com/file.zip

# List directory
curl ftp://ftp.example.com/pub/

# Download entire directory
curl -u username:password ftp://ftp.example.com/dir/ -o -
```

**Upload to FTP**
```bash
# Upload file
curl -T file.txt ftp://ftp.example.com/

# Upload with authentication
curl -u username:password -T file.txt ftp://ftp.example.com/upload/

# Upload multiple files
curl -u username:password -T "{file1.txt,file2.txt}" ftp://ftp.example.com/upload/
```

**FTP Commands**
```bash
# Create directory
curl -u username:password ftp://ftp.example.com -Q "MKD newdir"

# Delete file
curl -u username:password ftp://ftp.example.com -Q "DELE oldfile.txt"

# Rename file
curl -u username:password ftp://ftp.example.com -Q "RNFR old.txt" -Q "RNTO new.txt"
```

---

### SMTP (Send Email)
```bash
# Send email
curl --url 'smtps://smtp.gmail.com:465' \
     --ssl-reqd \
     --mail-from 'sender@example.com' \
     --mail-rcpt 'recipient@example.com' \
     --user 'sender@example.com:password' \
     -T mail.txt

# mail.txt content:
From: "Sender" <sender@example.com>
To: "Recipient" <recipient@example.com>
Subject: Test Email

This is the email body.
```

---

### DICT Protocol (Dictionary)
```bash
# Define word
curl dict://dict.org/d:hello

# Search for word
curl dict://dict.org/m:hello

# List databases
curl dict://dict.org/show:db
```

---

### Performance Testing

**Measure Timing**
```bash
# Show timing information
curl -w "\n\nTotal time: %{time_total}s\n" -o /dev/null -s https://example.com

# Detailed timing
curl -w @- -o /dev/null -s https://example.com << 'EOF'
    time_namelookup:  %{time_namelookup}s\n
       time_connect:  %{time_connect}s\n
    time_appconnect:  %{time_appconnect}s\n
   time_pretransfer:  %{time_pretransfer}s\n
      time_redirect:  %{time_redirect}s\n
 time_starttransfer:  %{time_starttransfer}s\n
                    ----------\n
         time_total:  %{time_total}s\n
EOF

# HTTP status code
curl -w "%{http_code}\n" -o /dev/null -s https://example.com

# -w: write out format
```

**Load Testing**
```bash
# Simple load test (10 requests)
for i in {1..10}; do
    curl -w "Request $i: %{time_total}s\n" -o /dev/null -s https://example.com
done

# Parallel load test
seq 1 100 | xargs -P 10 -I {} curl -w "{}: %{time_total}s\n" -o /dev/null -s https://example.com
```

---

### HTTP/2 and HTTP/3

**HTTP/2**
```bash
# Use HTTP/2
curl --http2 https://example.com

# HTTP/2 prior knowledge
curl --http2-prior-knowledge http://example.com

# Check if server supports HTTP/2
curl -I --http2 -s -o /dev/null -w "%{http_version}\n" https://example.com
```

**HTTP/3**
```bash
# Use HTTP/3 (requires curl 7.66+)
curl --http3 https://example.com

# Check HTTP/3 support
curl --http3-only https://cloudflare-quic.com
```

---

### SSL/TLS Options

```bash
# Ignore SSL certificate errors (dangerous!)
curl -k https://self-signed.example.com

# Specify SSL version
curl --tlsv1.2 https://example.com
curl --tlsv1.3 https://example.com

# Use specific cipher
curl --ciphers ECDHE-RSA-AES128-GCM-SHA256 https://example.com

# Client certificate
curl --cert client.pem --key client-key.pem https://example.com

# CA certificate
curl --cacert ca-bundle.crt https://example.com

# Show SSL/TLS info
curl -v https://example.com 2>&1 | grep -i ssl
```

---

### IPv4/IPv6

```bash
# Force IPv4
curl -4 https://example.com

# Force IPv6
curl -6 https://example.com

# Resolve to specific IP
curl --resolve example.com:443:93.184.216.34 https://example.com
```

---

## Configuration

### Using Config File

**Create config file:** `~/.curlrc`

```bash
# Create config
nano ~/.curlrc
```

**Sample Configuration:**
```conf
# Progress bar by default
progress-bar

# Follow redirects
location

# Keep alive
keepalive-time = 60

# User agent
user-agent = "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"

# Retry settings
retry = 3
retry-delay = 5
retry-max-time = 60

# Timeouts
connect-timeout = 30
max-time = 300

# Speed limit (0 = unlimited)
limit-rate = 0

# Show errors
show-error

# Create directories if needed
create-dirs

# Compressed transfer
compressed

# Keep cookies
cookie-jar = ~/.curl-cookies
```

**Override config:**
```bash
# Disable config file
curl --disable https://example.com

# Use custom config
curl -K custom-config.txt https://example.com
```

---

### Environment Variables

```bash
# HTTP Proxy
export http_proxy=http://proxy.example.com:8080
export https_proxy=http://proxy.example.com:8080

# SOCKS Proxy
export ALL_PROXY=socks5://127.0.0.1:1080

# No proxy for certain domains
export no_proxy=localhost,127.0.0.1,.local

# Certificate bundle
export CURL_CA_BUNDLE=/path/to/ca-bundle.crt

# Default flags
alias curl='curl -L --retry 3'
```

---

## Practical Examples

### Example 1: Download GitHub Release
```bash
# Get latest release info
RELEASE=$(curl -s https://api.github.com/repos/user/repo/releases/latest)

# Extract download URL
URL=$(echo $RELEASE | jq -r '.assets[0].browser_download_url')

# Download
curl -L -# -O "$URL"
```

**Complete script:**
```bash
#!/data/data/com.termux/files/usr/bin/bash

REPO="user/repo"
API="https://api.github.com/repos/$REPO/releases/latest"

echo "Fetching latest release..."
DOWNLOAD_URL=$(curl -s "$API" | jq -r '.assets[0].browser_download_url')

if [ -z "$DOWNLOAD_URL" ] || [ "$DOWNLOAD_URL" == "null" ]; then
    echo "Error: Could not find download URL"
    exit 1
fi

echo "Downloading: $DOWNLOAD_URL"
curl -L -# -O "$DOWNLOAD_URL"

echo "Download complete!"
```

---

### Example 2: Weather API
```bash
# Get weather (wttr.in)
curl wttr.in/London

# One-line weather
curl "wttr.in/London?format=%l:+%C+%t"

# Save as PNG
curl "wttr.in/London.png" -o weather.png
```

---

### Example 3: QR Code Generator
```bash
# Generate QR code for URL
curl "https://api.qrserver.com/v1/create-qr-code/?size=300x300&data=https://example.com" -o qr.png

# Or using qrencode locally
pkg install qrencode
echo "https://example.com" | qrencode -o qr.png
```

---

### Example 4: Shorten URL
```bash
# Using is.gd
shorten_url() {
    curl -s "https://is.gd/create.php?format=simple&url=$1"
}

# Usage
shorten_url "https://very-long-url.com/path/to/page"
```

---

### Example 5: Check Website Status
```bash
#!/data/data/com.termux/files/usr/bin/bash

URL="$1"

if [ -z "$URL" ]; then
    echo "Usage: $0 <url>"
    exit 1
fi

HTTP_CODE=$(curl -o /dev/null -s -w "%{http_code}" -L "$URL")
RESPONSE_TIME=$(curl -o /dev/null -s -w "%{time_total}" -L "$URL")

case $HTTP_CODE in
    200) STATUS="✓ Online" ;;
    3*) STATUS="⟳ Redirect" ;;
    4*) STATUS="✗ Client Error" ;;
    5*) STATUS="✗ Server Error" ;;
    000) STATUS="✗ Connection Failed" ;;
    *) STATUS="? Unknown" ;;
esac

echo "URL: $URL"
echo "Status: $HTTP_CODE - $STATUS"
echo "Response Time: ${RESPONSE_TIME}s"
```

**Usage:**
```bash
chmod +x check-status.sh
./check-status.sh https://example.com
```

---

### Example 6: Download Instagram Photo
```bash
# Get Instagram photo URL
curl -s "https://www.instagram.com/p/POST_ID/?__a=1&__d=dis" | \
    jq -r '.items[0].image_versions2.candidates[0].url' | \
    xargs curl -o photo.jpg
```

---

### Example 7: JSON API Client
```bash
#!/data/data/com.termux/files/usr/bin/bash

API_URL="https://jsonplaceholder.typicode.com"
TOKEN=""  # Add your token if needed

# Function to make API call
api_call() {
    local method="$1"
    local endpoint="$2"
    local data="$3"
    
    local headers=(-H "Content-Type: application/json")
    
    if [ -n "$TOKEN" ]; then
        headers+=(-H "Authorization: Bearer $TOKEN")
    fi
    
    if [ -n "$data" ]; then
        curl -s -X "$method" "${headers[@]}" -d "$data" "$API_URL$endpoint" | jq
    else
        curl -s -X "$method" "${headers[@]}" "$API_URL$endpoint" | jq
    fi
}

# Examples
echo "=== GET all posts ==="
api_call GET "/posts"

echo -e "\n=== GET single post ==="
api_call GET "/posts/1"

echo -e "\n=== CREATE post ==="
api_call POST "/posts" '{"title":"New Post","body":"Content","userId":1}'

echo -e "\n=== UPDATE post ==="
api_call PUT "/posts/1" '{"id":1,"title":"Updated","body":"New content","userId":1}'

echo -e "\n=== DELETE post ==="
api_call DELETE "/posts/1"
```

---

### Example 8: File Upload Progress
```bash
# Upload with progress bar
curl -# -F "file=@/sdcard/video.mp4" https://example.com/upload

# Upload multiple files with progress
for file in /sdcard/Pictures/*.jpg; do
    echo "Uploading: $(basename $file)"
    curl -# -F "file=@$file" https://example.com/upload
    echo ""
done
```

---

### Example 9: Monitor API Endpoint
```bash
#!/data/data/com.termux/files/usr/bin/bash

# Monitor API endpoint every 30 seconds
ENDPOINT="https://api.example.com/health"
INTERVAL=30

echo "Monitoring: $ENDPOINT"
echo "Press Ctrl+C to stop"
echo ""

while true; do
    TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')
    HTTP_CODE=$(curl -o /dev/null -s -w "%{http_code}" "$ENDPOINT")
    RESPONSE_TIME=$(curl -o /dev/null -s -w "%{time_total}" "$ENDPOINT")
    
    if [ "$HTTP_CODE" -eq 200 ]; then
        echo "[$TIMESTAMP] ✓ Status: $HTTP_CODE | Response: ${RESPONSE_TIME}s"
    else
        echo "[$TIMESTAMP] ✗ Status: $HTTP_CODE | Response: ${RESPONSE_TIME}s"
        termux-notification --title "API Down" --content "$ENDPOINT returned $HTTP_CODE"
    fi
    
    sleep $INTERVAL
done
```

---

### Example 10: Bulk Image Download
```bash
#!/data/data/com.termux/files/usr/bin/bash

# Download images 1-50
for i in {1..50}; do
    printf -v num "%03d" $i
    echo "Downloading image $num..."
    curl -# -o "image-$num.jpg" "https://example.com/images/$i.jpg"
done

echo "Download complete!"
```

---

## Important Options Reference

| Option | Description | Example |
|--------|-------------|---------|
| `-o FILE` | Save to file | `-o file.zip` |
| `-O` | Save with remote name | `-O` |
| `-C -` | Resume download | `-C -` |
| `-#` | Progress bar | `-#` |
| `-s` | Silent mode | `-s` |
| `-v` | Verbose mode | `-v` |
| `-i` | Include headers | `-i` |
| `-I` | HEAD request | `-I` |
| `-X METHOD` | HTTP method | `-X POST` |
| `-d DATA` | Data to send | `-d "key=val"` |
| `-H HEADER` | Custom header | `-H "Auth: token"` |
| `-A AGENT` | User agent | `-A "Mozilla/5.0"` |
| `-u USER:PASS` | Authentication | `-u john:secret` |
| `-b COOKIE` | Cookie | `-b "session=abc"` |
| `-c FILE` | Save cookies | `-c cookies.txt` |
| `-L` | Follow redirects | `-L` |
| `-F` | Multipart form | `-F "file=@pic.jpg"` |
| `-T FILE` | Upload file (PUT) | `-T file.txt` |
| `-x PROXY` | Use proxy | `-x proxy.com:8080` |
| `-k` | Insecure SSL | `-k` |
| `--limit-rate` | Speed limit | `--limit-rate 1M` |
| `--retry N` | Retry count | `--retry 5` |
| `--max-time SEC` | Timeout | `--max-time 300` |
| `-w FORMAT` | Output format | `-w "%{http_code}"` |

---

## Format Variables (for -w)

```bash
# Commonly used format variables

# HTTP
%{http_code}              # HTTP status code
%{http_version}           # HTTP version

# Timing
%{time_total}             # Total time
%{time_namelookup}        # DNS lookup time
%{time_connect}           # Connection time
%{time_appconnect}        # SSL handshake time
%{time_pretransfer}       # Time to start transfer
%{time_starttransfer}     # Time to first byte
%{time_redirect}          # Redirect time

# Size
%{size_download}          # Downloaded bytes
%{size_upload}            # Uploaded bytes
%{size_header}            # Header size
%{size_request}           # Request size

# Speed
%{speed_download}         # Download speed (bytes/sec)
%{speed_upload}           # Upload speed (bytes/sec)

# URLs
%{url_effective}          # Final URL after redirects
%{redirect_url}           # Redirect URL

# SSL
%{ssl_verify_result}      # SSL verification result

# Other
%{num_redirects}          # Number of redirects
%{num_connects}           # Number of connections
%{content_type}           # Content-Type header
%{filename_effective}     # Filename that would be saved
```

**Example usage:**
```bash
# Complete timing report
curl -w "\n\
DNS lookup:        %{time_namelookup}s\n\
Connect:           %{time_connect}s\n\
SSL handshake:     %{time_appconnect}s\n\
Pre-transfer:      %{time_pretransfer}s\n\
Start transfer:    %{time_starttransfer}s\n\
Total:             %{time_total}s\n\
HTTP code:         %{http_code}\n\
Downloaded:        %{size_download} bytes\n\
Speed:             %{speed_download} bytes/sec\n" \
-o /dev/null -s https://example.com
```

---

## Troubleshooting

### Issue 1: "SSL certificate problem"
**Solution:**
```bash
# Quick fix (insecure - use cautiously)
curl -k https://example.com

# Better: update CA certificates
pkg update ca-certificates

# Or specify CA bundle
curl --cacert /path/to/ca-bundle.crt https://example.com
```

---

### Issue 2: "Could not resolve host"
**Solution:**
```bash
# Check DNS
curl -v https://example.com 2>&1 | grep -i "could not resolve"

# Use specific DNS
curl --dns-servers 8.8.8.8,8.8.4.4 https://example.com

# Check /etc/resolv.conf
cat /etc/resolv.conf
```

---

### Issue 3: "Connection timed out"
**Solution:**
```bash
# Increase timeout
curl --connect-timeout 60 --max-time 300 https://example.com

# Try with retry
curl --retry 5 --retry-delay 10 https://example.com
```

---

### Issue 4: "403 Forbidden" or "406 Not Acceptable"
**Solution:**
```bash
# Server blocking curl user agent
curl -A "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" https://example.com

# Add referer
curl -e "https://google.com" -A "Mozilla/5.0" https://example.com
```

---

### Issue 5: "curl: (7) Failed to connect to proxy"
**Solution:**
```bash
# Check proxy settings
echo $http_proxy
echo $https_proxy

# Unset proxy
unset http_proxy https_proxy

# Or use --noproxy
curl --noproxy '*' https://example.com
```

---

### Issue 6: Downloaded file is corrupt or incomplete
**Solution:**
```bash
# Use resume
curl -C - -O https://example.com/file.zip

# Check integrity with checksum
curl -O https://example.com/file.zip
curl -O https://example.com/file.zip.sha256
sha256sum -c file.zip.sha256
```

---

### Issue 7: "Empty reply from server"
**Solution:**
```bash
# Server might have closed connection
curl --http1.1 https://example.com  # Try HTTP/1.1
curl --http2 https://example.com    # Or HTTP/2

# Increase keep-alive
curl --keepalive-time 120 https://example.com
```

---

### Issue 8: POST request not working
**Solution:**
```bash
# Ensure correct Content-Type
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"key":"value"}' \
     https://api.example.com

# Debug request
curl -v -X POST \
     -H "Content-Type: application/json" \
     -d '{"key":"value"}' \
     https://api.example.com
```

---

## Best Practices

### 1. **Always Use HTTPS When Possible**
```bash
# Good
curl https://example.com

# Bad (unless specifically needed)
curl http://example.com
```

---

### 2. **Use Resume for Large Files**
```bash
curl -C - -# -O https://example.com/largefile.iso
```

---

### 3. **Add Retry Logic**
```bash
curl --retry 5 --retry-delay 10 --retry-all-errors URL
```

---

### 4. **Set Reasonable Timeouts**
```bash
curl --connect-timeout 30 --max-time 300 URL
```

---

### 5. **Use Config File for Common Settings**
```bash
# Edit ~/.curlrc once
echo "retry = 3" >> ~/.curlrc
echo "retry-delay = 5" >> ~/.curlrc
```

---

### 6. **Silent JSON Parsing**
```bash
# Good - clean output
curl -s https://api.example.com/data | jq

# Bad - shows progress in JSON
curl https://api.example.com/data | jq
```

---

### 7. **Proper Error Handling in Scripts**
```bash
#!/data/data/com.termux/files/usr/bin/bash

response=$(curl -s -w "\n%{http_code}" https://api.example.com/data)
http_code=$(echo "$response" | tail -n1)
body=$(echo "$response" | sed '$d')

if [ "$http_code" -eq 200 ]; then
    echo "Success!"
    echo "$body" | jq
else
    echo "Error: HTTP $http_code"
    exit 1
fi
```

---

### 8. **Rate Limiting**
```bash
# Be nice to servers - add delays
for url in $(cat urls.txt); do
    curl -O "$url"
    sleep 2
done
```

---

### 9. **Secure Credential Handling**
```bash
# Bad - password visible in history
curl -u user:password https://example.com

# Good - prompt for password
curl -u user https://example.com

# Better - use .netrc file
echo "machine example.com login user password secret" >> ~/.netrc
chmod 600 ~/.netrc
curl -n https://example.com
```

---

### 10. **Verify SSL Certificates**
```bash
# Default (secure)
curl https://example.com

# Only skip verification if absolutely necessary
curl -k https://self-signed.local  # Document why you're doing this
```

---

## Useful Scripts

### Script 1: API Testing Framework
```bash
#!/data/data/com.termux/files/usr/bin/bash
# api-test.sh

BASE_URL="https://jsonplaceholder.typicode.com"
PASSED=0
FAILED=0

# Color codes
RED='\033[0;31m'
GREEN='\033[0;32m'
NC='\033[0m' # No Color

test_endpoint() {
    local method="$1"
    local endpoint="$2"
    local expected_code="$3"
    local data="$4"
    
    echo -n "Testing $method $endpoint ... "
    
    if [ -n "$data" ]; then
        http_code=$(curl -s -o /dev/null -w "%{http_code}" -X "$method" -H "Content-Type: application/json" -d "$data" "$BASE_URL$endpoint")
    else
        http_code=$(curl -s -o /dev/null -w "%{http_code}" -X "$method" "$BASE_URL$endpoint")
    fi
    
    if [ "$http_code" -eq "$expected_code" ]; then
        echo -e "${GREEN}PASS${NC} ($http_code)"
        PASSED=$((PASSED + 1))
    else
        echo -e "${RED}FAIL${NC} (expected $expected_code, got $http_code)"
        FAILED=$((FAILED + 1))
    fi
}

echo "=== API Test Suite ==="
echo ""

test_endpoint "GET" "/posts" 200
test_endpoint "GET" "/posts/1" 200
test_endpoint "GET" "/posts/999" 404
test_endpoint "POST" "/posts" 201 '{"title":"Test","body":"Content","userId":1}'
test_endpoint "PUT" "/posts/1" 200 '{"id":1,"title":"Updated","body":"Content","userId":1}'
test_endpoint "DELETE" "/posts/1" 200

echo ""
echo "=== Results ==="
echo -e "${GREEN}Passed: $PASSED${NC}"
echo -e "${RED}Failed: $FAILED${NC}"
echo "Total: $((PASSED + FAILED))"
```

---

### Script 2: Bulk Image Downloader
```bash
#!/data/data/com.termux/files/usr/bin/bash
# bulk-download.sh

if [ $# -lt 3 ]; then
    echo "Usage: $0 <base-url> <start> <end> [extension]"
    echo "Example: $0 https://example.com/img 1 100 jpg"
    exit 1
fi

BASE_URL="$1"
START="$2"
END="$3"
EXT="${4:-jpg}"
FAILED=0

mkdir -p downloads
cd downloads

for i in $(seq $START $END); do
    # Zero-pad number
    NUM=$(printf "%04d" $i)
    URL="${BASE_URL}${i}.${EXT}"
    
    echo -n "Downloading $NUM ... "
    
    if curl -s -f -o "${NUM}.${EXT}" "$URL"; then
        echo "✓"
    else
        echo "✗"
        FAILED=$((FAILED + 1))
    fi
    
    sleep 1
done

echo ""
echo "Downloaded: $((END - START + 1 - FAILED))"
echo "Failed: $FAILED"
```

**Usage:**
```bash
chmod +x bulk-download.sh
./bulk-download.sh "https://example.com/photo-" 1 50 jpg
```

---

### Script 3: Website Uptime Monitor
```bash
#!/data/data/com.termux/files/usr/bin/bash
# uptime-monitor.sh

URL="$1"
CHECK_INTERVAL=60  # seconds
LOG_FILE="uptime-$(date +%Y%m%d).log"

if [ -z "$URL" ]; then
    echo "Usage: $0 <url>"
    exit 1
fi

echo "Monitoring: $URL"
echo "Log file: $LOG_FILE"
echo "Press Ctrl+C to stop"
echo ""

while true; do
    TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')
    
    # Make request and capture timing
    RESPONSE=$(curl -s -w "\n%{http_code}\n%{time_total}" -o /dev/null "$URL")
    HTTP_CODE=$(echo "$RESPONSE" | sed -n '1p')
    TIME_TOTAL=$(echo "$RESPONSE" | sed -n '2p')
    
    if [ "$HTTP_CODE" -eq 200 ]; then
        STATUS="UP"
        SYMBOL="✓"
        LOG_LINE="$TIMESTAMP | $STATUS | $HTTP_CODE | ${TIME_TOTAL}s"
        echo -e "\033[0;32m$SYMBOL\033[0m $LOG_LINE"
    else
        STATUS="DOWN"
        SYMBOL="✗"
        LOG_LINE="$TIMESTAMP | $STATUS | $HTTP_CODE | ${TIME_TOTAL}s"
        echo -e "\033[0;31m$SYMBOL\033[0m $LOG_LINE"
        
        # Send notification
        termux-notification \
            --title "Website Down" \
            --content "$URL returned $HTTP_CODE"
    fi
    
    echo "$LOG_LINE" >> "$LOG_FILE"
    
    sleep $CHECK_INTERVAL
done
```

**Usage:**
```bash
chmod +x uptime-monitor.sh
./uptime-monitor.sh https://example.com
```

---

### Script 4: cURL Wrapper with Logging
```bash
#!/data/data/com.termux/files/usr/bin/bash
# smart-curl.sh - Enhanced curl with automatic logging

LOG_DIR="$HOME/.curl-logs"
mkdir -p "$LOG_DIR"

TIMESTAMP=$(date +%Y%m%d_%H%M%S)
LOG_FILE="$LOG_DIR/curl_${TIMESTAMP}.log"

# Log request details
echo "=== cURL Request ===" >> "$LOG_FILE"
echo "Date: $(date)" >> "$LOG_FILE"
echo "Command: curl $@" >> "$LOG_FILE"
echo "" >> "$LOG_FILE"

# Execute curl with timing
echo "=== Response ===" >> "$LOG_FILE"
/data/data/com.termux/files/usr/bin/curl \
    -w "\n\n=== Timing ===\nHTTP Code: %{http_code}\nTotal Time: %{time_total}s\nDownload Speed: %{speed_download} bytes/s\n" \
    "$@" 2>&1 | tee -a "$LOG_FILE"

echo ""
echo "Log saved to: $LOG_FILE"
```

**Usage:**
```bash
chmod +x smart-curl.sh
alias curl='~/smart-curl.sh'

# Now all curl commands are logged
curl https://api.github.com/users/torvalds
```

---

### Script 5: JSON API Client Generator
```bash
#!/data/data/com.termux/files/usr/bin/bash
# api-client.sh - Generate API client functions

generate_client() {
    local base_url="$1"
    local output_file="${2:-api-client.sh}"
    
    cat > "$output_file" << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
# Auto-generated API client

BASE_URL="$BASE_URL_PLACEHOLDER"
TOKEN=""

# Set token
set_token() {
    TOKEN="$1"
}

# Make API request
api_request() {
    local method="$1"
    local endpoint="$2"
    local data="$3"
    
    local args=(
        -s
        -X "$method"
        -H "Content-Type: application/json"
    )
    
    if [ -n "$TOKEN" ]; then
        args+=(-H "Authorization: Bearer $TOKEN")
    fi
    
    if [ -n "$data" ]; then
        args+=(-d "$data")
    fi
    
    curl "${args[@]}" "$BASE_URL$endpoint" | jq
}

# Convenience functions
api_get() {
    api_request GET "$1"
}

api_post() {
    api_request POST "$1" "$2"
}

api_put() {
    api_request PUT "$1" "$2"
}

api_delete() {
    api_request DELETE "$1"
}

# Export functions
export -f set_token api_request api_get api_post api_put api_delete
EOF

    sed -i "s|\$BASE_URL_PLACEHOLDER|$base_url|g" "$output_file"
    chmod +x "$output_file"
    
    echo "API client generated: $output_file"
}

# Usage
if [ $# -lt 1 ]; then
    echo "Usage: $0 <base-url> [output-file]"
    echo "Example: $0 https://api.example.com/v1 my-api.sh"
    exit 1
fi

generate_client "$1" "$2"
```

**Usage:**
```bash
chmod +x api-client.sh
./api-client.sh "https://jsonplaceholder.typicode.com" client.sh

# Use generated client
source client.sh
api_get "/posts/1"
api_post "/posts" '{"title":"New Post","userId":1}'
```

---

## Integration with Other Tools

### With jq (JSON Processing)
```bash
# Install jq
pkg install jq

# Get specific field
curl -s https://api.github.com/users/torvalds | jq '.name'

# Filter array
curl -s https://api.github.com/repos/torvalds/linux/commits | jq '.[].commit.message'

# Complex query
curl -s https://api.github.com/users/torvalds/repos | \
    jq '[.[] | {name, stars: .stargazers_count}] | sort_by(.stars) | reverse | .[0:5]'
```

---

### With xmllint (XML Processing)
```bash
# Install libxml2
pkg install libxml2

# Parse XML response
curl -s https://example.com/feed.xml | xmllint --format -

# Extract specific elements
curl -s https://example.com/feed.xml | xmllint --xpath "//item/title/text()" -
```

---

### With yt-dlp (Video Downloads)
```bash
# Use curl as downloader
yt-dlp --downloader curl --downloader-args "curl:-C - --retry 5" VIDEO_URL
```

---

### With Termux:API
```bash
# Download and notify
curl -# -O https://example.com/file.zip && \
    termux-notification --title "Download Complete" --content "file.zip downloaded"

# Copy API response to clipboard
curl -s https://api.example.com/data | jq | termux-clipboard-set

# Share downloaded file
curl -s -o /tmp/file.pdf https://example.com/doc.pdf && \
    termux-open /tmp/file.pdf
```

---

## Comparison Table

| Task | curl | wget | aria2 |
|------|------|------|-------|
| Simple download | ✅ `curl -O URL` | ✅ `wget URL` | ✅ `aria2c URL` |
| Resume download | ✅ `curl -C -` | ✅ `wget -c` | ✅ `aria2c -c` |
| API POST/PUT | ✅ Best | ❌ | ❌ |
| Headers control | ✅ Best | ⚠️ Limited | ⚠️ Limited |
| Mirror website | ❌ | ✅ Best | ❌ |
| Multi-connection | ❌ | ❌ | ✅ Best |
| FTP upload | ✅ | ❌ | ✅ |
| Cookie handling | ✅ | ✅ | ❌ |
| Protocol support | ✅ 25+ | ⚠️ 3 | ⚠️ 5 |
| Progress bar | ✅ | ✅ | ✅ |
| Background mode | ⚠️ Manual | ✅ `-b` | ✅ `-D` |
| Parallel downloads | ⚠️ Manual | ❌ | ✅ Auto |

---

## Quick Reference Card

```bash
# DOWNLOAD
curl -O URL                       # Save with remote name
curl -o file.zip URL              # Save with custom name
curl -# -O URL                    # Progress bar
curl -C - -O URL                  # Resume download

# HTTP METHODS
curl URL                          # GET
curl -X POST -d "data" URL        # POST
curl -X PUT -d "data" URL         # PUT
curl -X DELETE URL                # DELETE

# HEADERS
curl -H "Key: Value" URL          # Add header
curl -A "Agent" URL               # User agent
curl -e "Referer" URL             # Referer
curl -i URL                       # Show headers
curl -I URL                       # Headers only

# AUTHENTICATION
curl -u user:pass URL             # Basic auth
curl -H "Authorization: Bearer T" # Bearer token

# DATA
curl -d "key=val" URL             # Form data
curl -d @file.json URL            # From file
curl -F "file=@pic.jpg" URL       # File upload

# OPTIONS
curl -L URL                       # Follow redirects
curl -k URL                       # Insecure SSL
curl -v URL                       # Verbose
curl -s URL                       # Silent
curl -x proxy:8080 URL            # Use proxy
curl --retry 5 URL                # Retry 5 times
curl --limit-rate 1M URL          # Limit speed

# OUTPUT FORMAT
curl -w "%{http_code}" URL        # HTTP code
curl -w "%{time_total}" URL       # Total time

# COOKIES
curl -b cookies.txt URL           # Load cookies
curl -c cookies.txt URL           # Save cookies
```

---

## Sources & Citations

This guide was compiled from:

1. **Official curl Documentation**
   - https://curl.se/docs/
   - Complete manual and reference

2. **Everything curl Book**
   - https://everything.curl.dev/
   - Comprehensive guide by Daniel Stenberg

3. **curl GitHub Repository**
   - https://github.com/curl/curl
   - Latest features and changelog

4. **curl Man Pages**
   - https://curl.se/docs/manpage.html
   - Detailed option reference

5. **curl HTTP Scripting Guide**
   - https://curl.se/docs/httpscripting.html
   - Best practices and examples

6. **Termux Wiki**
   - https://wiki.termux.com/
   - Termux-specific usage

7. **Community Resources**
   - Stack Overflow curl tag
   - Real-world examples and use cases from 2024-2025

---

**Last Updated**: 2025  
**cURL Version**: 8.5.0+  
**Tested on**: Termux (Android)  
**Status**: Production Ready

---

This comprehensive guide covers everything you need to master curl in Termux as of 2025. Save this markdown file for quick reference! 🚀