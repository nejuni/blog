--- 
title: "jq"
description: "readme"
date: 2025-10-28T14:10:52Z
lastmod: 2025-10-28T14:15:52Z
tags: ["log"]
draft: false
---

# jq - Command-line JSON Processor Guide

**Version**: jq 1.7+ (Latest as of 2025)  
**Official Repository**: https://github.com/jqlang/jq  
**Official Documentation**: https://jqlang.github.io/jq/

---

## Table of Contents
1. [Introduction](#introduction)
2. [Basic Syntax](#basic-syntax)
3. [Essential Daily Commands](#essential-daily-commands)
4. [Filtering and Selecting](#filtering-and-selecting)
5. [Transforming Data](#transforming-data)
6. [Working with Arrays](#working-with-arrays)
7. [Working with Objects](#working-with-objects)
8. [Advanced Operations](#advanced-operations)
9. [Practical Real-World Examples](#practical-real-world-examples)
10. [Common Patterns in Termux](#common-patterns-in-termux)

---

## Introduction

**jq** is a lightweight and flexible command-line JSON processor. It's like `sed` for JSON data - you can use it to slice, filter, map, and transform structured data.

### Why Use jq?
- Parse and pretty-print JSON
- Extract specific fields from API responses
- Transform JSON structures
- Filter data based on conditions
- Combine multiple JSON files
- Process JSON in shell scripts

---

## Basic Syntax

```bash
jq [options] '<filter>' [file...]
```

### Common Options
- `.` - Identity filter (outputs input unchanged)
- `-r` - Raw output (without quotes)
- `-c` - Compact output (single line)
- `-n` - No input (useful with `--arg`)
- `-s` - Slurp (read entire input into array)
- `-e` - Exit with status code based on output
- `-M` - Monochrome (no colors)
- `-C` - Colorize output (default for terminals)

---

## Essential Daily Commands

### 1. Pretty Print JSON
```bash
# Basic pretty printing
echo '{"name":"John","age":30}' | jq '.'

# Pretty print from file
jq '.' data.json

# Pretty print from API
curl -s https://api.github.com/users/octocat | jq '.'
```

### 2. Extract Single Field
```bash
# Get a specific field
echo '{"name":"John","age":30}' | jq '.name'
# Output: "John"

# Raw output (no quotes)
echo '{"name":"John","age":30}' | jq -r '.name'
# Output: John
```

### 3. Extract Multiple Fields
```bash
# Multiple fields as array
echo '{"name":"John","age":30,"city":"NYC"}' | jq '.name, .age'
# Output:
# "John"
# 30

# Create new object with selected fields
echo '{"name":"John","age":30,"city":"NYC"}' | jq '{name, age}'
# Output: {"name":"John","age":30}
```

### 4. Extract Nested Fields
```bash
# Access nested data
echo '{"user":{"name":"John","details":{"age":30}}}' | jq '.user.details.age'
# Output: 30

# Alternative notation
echo '{"user":{"name":"John"}}' | jq '.["user"]["name"]'
```

### 5. Work with Arrays
```bash
# Get first element
echo '[1,2,3,4,5]' | jq '.[0]'
# Output: 1

# Get last element
echo '[1,2,3,4,5]' | jq '.[-1]'
# Output: 5

# Get range (slice)
echo '[1,2,3,4,5]' | jq '.[1:3]'
# Output: [2,3]

# Get array length
echo '[1,2,3,4,5]' | jq 'length'
# Output: 5
```

---

## Filtering and Selecting

### 6. Filter Array Elements
```bash
# Select elements matching condition
echo '[{"name":"John","age":30},{"name":"Jane","age":25}]' | jq '.[] | select(.age > 26)'
# Output: {"name":"John","age":30}

# Multiple conditions (AND)
echo '[{"name":"John","age":30,"city":"NYC"}]' | jq '.[] | select(.age > 25 and .city == "NYC")'

# Multiple conditions (OR)
echo '[{"name":"John","age":30}]' | jq '.[] | select(.age > 25 or .name == "Jane")'
```

### 7. Check if Key Exists
```bash
# Check if key exists
echo '{"name":"John","age":30}' | jq 'has("name")'
# Output: true

# Select objects with specific key
echo '[{"name":"John"},{"age":30}]' | jq '.[] | select(has("name"))'
# Output: {"name":"John"}
```

### 8. Filter by Type
```bash
# Select only strings
echo '[1,"hello",true,"world"]' | jq '.[] | select(type == "string")'
# Output:
# "hello"
# "world"

# Available types: null, boolean, number, string, array, object
```

---

## Transforming Data

### 9. Map Over Arrays
```bash
# Apply transformation to each element
echo '[1,2,3,4,5]' | jq 'map(. * 2)'
# Output: [2,4,6,8,10]

# Extract field from array of objects
echo '[{"name":"John","age":30},{"name":"Jane","age":25}]' | jq 'map(.name)'
# Output: ["John","Jane"]

# Alternative using .[]
echo '[{"name":"John"},{"name":"Jane"}]' | jq '[.[] | .name]'
```

### 10. Create New Objects
```bash
# Transform object structure
echo '{"first":"John","last":"Doe","years":30}' | jq '{name: (.first + " " + .last), age: .years}'
# Output: {"name":"John Doe","age":30}

# Add new fields
echo '{"name":"John"}' | jq '. + {age: 30, city: "NYC"}'
# Output: {"name":"John","age":30,"city":"NYC"}
```

### 11. Rename Keys
```bash
# Rename object keys
echo '{"oldName":"John"}' | jq '{newName: .oldName}'
# Output: {"newName":"John"}

# Rename multiple keys
echo '{"first":"John","last":"Doe"}' | jq '{firstName: .first, lastName: .last}'
```

### 12. Delete Keys
```bash
# Remove specific key
echo '{"name":"John","age":30,"city":"NYC"}' | jq 'del(.age)'
# Output: {"name":"John","city":"NYC"}

# Remove multiple keys
echo '{"name":"John","age":30,"city":"NYC"}' | jq 'del(.age, .city)'
# Output: {"name":"John"}
```

---

## Working with Arrays

### 13. Sort Arrays
```bash
# Sort numbers
echo '[3,1,4,1,5,9,2,6]' | jq 'sort'
# Output: [1,1,2,3,4,5,6,9]

# Sort objects by field
echo '[{"name":"John","age":30},{"name":"Jane","age":25}]' | jq 'sort_by(.age)'
# Output: [{"name":"Jane","age":25},{"name":"John","age":30}]

# Reverse sort
echo '[3,1,4,1,5]' | jq 'sort | reverse'
```

### 14. Unique Values
```bash
# Get unique values
echo '[1,2,2,3,3,3,4]' | jq 'unique'
# Output: [1,2,3,4]

# Unique by field
echo '[{"name":"John","age":30},{"name":"Jane","age":30}]' | jq 'unique_by(.age)'
```

### 15. Group By
```bash
# Group array elements by field
echo '[{"type":"A","val":1},{"type":"B","val":2},{"type":"A","val":3}]' | jq 'group_by(.type)'
# Output: [[{"type":"A","val":1},{"type":"A","val":3}],[{"type":"B","val":2}]]
```

### 16. Flatten Arrays
```bash
# Flatten one level
echo '[[1,2],[3,4],[5]]' | jq 'flatten'
# Output: [1,2,3,4,5]

# Flatten specific depth
echo '[[[1,2]],[[3,4]]]' | jq 'flatten(1)'
# Output: [[1,2],[3,4]]
```

### 17. Array Operations
```bash
# Add element to array
echo '[1,2,3]' | jq '. + [4]'
# Output: [1,2,3,4]

# Concatenate arrays
echo '[[1,2],[3,4]]' | jq 'add'
# Output: [1,2,3,4]

# Sum array
echo '[1,2,3,4,5]' | jq 'add'
# Output: 15

# Min/Max
echo '[3,1,4,1,5,9]' | jq 'min'
# Output: 1
echo '[3,1,4,1,5,9]' | jq 'max'
# Output: 9
```

---

## Working with Objects

### 18. Get All Keys
```bash
# Extract all keys
echo '{"name":"John","age":30,"city":"NYC"}' | jq 'keys'
# Output: ["age","city","name"]

# Get keys unsorted
echo '{"name":"John","age":30,"city":"NYC"}' | jq 'keys_unsorted'
```

### 19. Get All Values
```bash
# Extract all values
echo '{"name":"John","age":30,"city":"NYC"}' | jq '.[]'
# Output:
# "John"
# 30
# "NYC"

# As array
echo '{"name":"John","age":30}' | jq '[.[]]'
# Output: ["John",30]
```

### 20. Merge Objects
```bash
# Merge two objects
echo '{"a":1}' | jq '. + {"b":2}'
# Output: {"a":1,"b":2}

# Recursive merge
echo '{"a":{"b":1}}' | jq '. * {"a":{"c":2}}'
# Output: {"a":{"b":1,"c":2}}
```

### 21. Convert Object to Array
```bash
# Object to key-value pairs
echo '{"name":"John","age":30}' | jq 'to_entries'
# Output: [{"key":"name","value":"John"},{"key":"age","value":30}]

# Array back to object
echo '[{"key":"name","value":"John"}]' | jq 'from_entries'
# Output: {"name":"John"}
```

---

## Advanced Operations

### 22. Conditional Logic
```bash
# If-then-else
echo '{"age":30}' | jq 'if .age >= 18 then "adult" else "minor" end'
# Output: "adult"

# Ternary-like operator
echo '{"age":30}' | jq '.age >= 18 | if . then "adult" else "minor" end'
```

### 23. String Operations
```bash
# String concatenation
echo '{"first":"John","last":"Doe"}' | jq '.first + " " + .last'
# Output: "John Doe"

# String interpolation
echo '{"name":"John"}' | jq '"Hello, \(.name)!"'
# Output: "Hello, John!"

# Convert to lowercase/uppercase
echo '{"name":"JOHN"}' | jq '.name | ascii_downcase'
# Output: "john"
echo '{"name":"john"}' | jq '.name | ascii_upcase'
# Output: "JOHN"

# Split string
echo '{"data":"a,b,c"}' | jq '.data | split(",")'
# Output: ["a","b","c"]

# Join array
echo '["a","b","c"]' | jq 'join(",")'
# Output: "a,b,c"

# Test string pattern
echo '{"email":"test@example.com"}' | jq '.email | test("@")'
# Output: true
```

### 24. Mathematical Operations
```bash
# Basic math
echo '{"a":10,"b":3}' | jq '.a + .b'
# Output: 13

echo '{"a":10,"b":3}' | jq '.a - .b'
# Output: 7

echo '{"a":10,"b":3}' | jq '.a * .b'
# Output: 30

echo '{"a":10,"b":3}' | jq '.a / .b'
# Output: 3.3333333333333335

# Modulo
echo '{"a":10,"b":3}' | jq '.a % .b'
# Output: 1

# Round/Floor/Ceil
echo '3.7' | jq 'floor'
# Output: 3
echo '3.2' | jq 'ceil'
# Output: 4
echo '3.7' | jq 'round'
# Output: 4
```

### 25. Date/Time Operations
```bash
# Current timestamp
echo 'null' | jq 'now'
# Output: 1730116320.123456 (Unix timestamp)

# Convert timestamp to ISO8601
echo '1730116320' | jq 'todate'
# Output: "2025-10-28T12:32:00Z"

# Convert ISO8601 to timestamp
echo '"2025-10-28T12:32:00Z"' | jq 'fromdate'
# Output: 1730116320

# Format date
echo '1730116320' | jq 'todate | strftime("%Y-%m-%d")'
```

### 26. Variables
```bash
# Define and use variable
echo '{"x":10,"y":20}' | jq '.x as $a | .y as $b | $a + $b'
# Output: 30

# Pass variable from command line
echo '{"name":"World"}' | jq --arg greeting "Hello" '$greeting + ", " + .name'
# Output: "Hello, World"

# Pass JSON variable
echo '{}' | jq --argjson num 42 '{value: $num}'
# Output: {"value":42}
```

### 27. Recursion
```bash
# Recursive descent
echo '{"a":{"b":{"c":"value"}}}' | jq '.. | select(type == "string")'
# Output: "value"

# Recursive walk
echo '{"a":1,"b":{"c":2}}' | jq 'walk(if type == "number" then . * 2 else . end)'
# Output: {"a":2,"b":{"c":4}}
```

### 28. Error Handling
```bash
# Try-catch
echo '{"num":"not a number"}' | jq 'try .num + 1 catch "error"'
# Output: "error"

# Alternative operator (use default if null)
echo '{"name":null}' | jq '.name // "Unknown"'
# Output: "Unknown"

# Optional field access
echo '{}' | jq '.missing?'
# Output: null (no error)
```

### 29. Reduce
```bash
# Sum with reduce
echo '[1,2,3,4,5]' | jq 'reduce .[] as $item (0; . + $item)'
# Output: 15

# Build object with reduce
echo '[{"name":"John","age":30},{"name":"Jane","age":25}]' | jq 'reduce .[] as $item ({}; .[$item.name] = $item.age)'
# Output: {"John":30,"Jane":25}
```

### 30. Comments
```bash
# jq supports comments in filters
echo '{"a":1}' | jq '
  # This is a comment
  .a + 1  # Add one to a
'
# Output: 2
```

---

## Practical Real-World Examples

### Example 1: Parse API Response
```bash
# Get user information from GitHub API
curl -s https://api.github.com/users/octocat | jq '{
  username: .login,
  name: .name,
  followers: .followers,
  repos: .public_repos,
  url: .html_url
}'
```

### Example 2: Process Package.json
```bash
# Extract dependencies from package.json
cat package.json | jq '.dependencies | keys'

# Get scripts
cat package.json | jq '.scripts'

# Check if dependency exists
cat package.json | jq 'has("dependencies") and (.dependencies | has("express"))'
```

### Example 3: Filter Log Files (JSON format)
```bash
# Find errors in JSON logs
cat app.log | jq 'select(.level == "error")'

# Get errors from last hour (assuming timestamp field)
cat app.log | jq --arg time "$(date -d '1 hour ago' +%s)" 'select(.timestamp > ($time | tonumber))'

# Count errors by type
cat app.log | jq 'select(.level == "error") | .type' | sort | uniq -c
```

### Example 4: Transform API Response for CSV
```bash
# Convert JSON array to CSV
echo '[{"name":"John","age":30},{"name":"Jane","age":25}]' | jq -r '.[] | [.name, .age] | @csv'
# Output:
# "John",30
# "Jane",25

# With headers
echo '[{"name":"John","age":30}]' | jq -r '["name","age"], (.[] | [.name, .age]) | @csv'
```

### Example 5: Combine Multiple JSON Files
```bash
# Slurp multiple files into array
jq -s '.' file1.json file2.json file3.json

# Merge objects from multiple files
jq -s 'add' file1.json file2.json

# Combine arrays from multiple files
jq -s 'flatten' file1.json file2.json
```

### Example 6: Extract Data from Docker Inspect
```bash
# Get container IP addresses
docker inspect $(docker ps -q) | jq -r '.[] | .NetworkSettings.IPAddress'

# Get container names and statuses
docker inspect $(docker ps -aq) | jq -r '.[] | {name: .Name, status: .State.Status}'

# Get port mappings
docker inspect container_name | jq '.[0].NetworkSettings.Ports'
```

### Example 7: Parse Kubernetes JSON
```bash
# Get pod names in namespace
kubectl get pods -n default -o json | jq -r '.items[].metadata.name'

# Get pods not in Running state
kubectl get pods -o json | jq -r '.items[] | select(.status.phase != "Running") | .metadata.name'

# Get container images used
kubectl get pods -o json | jq -r '.items[].spec.containers[].image' | sort | uniq
```

### Example 8: Format AWS CLI Output
```bash
# List EC2 instances with specific fields
aws ec2 describe-instances | jq '.Reservations[].Instances[] | {
  id: .InstanceId,
  type: .InstanceType,
  state: .State.Name,
  ip: .PublicIpAddress
}'

# Get S3 bucket sizes
aws s3api list-buckets | jq -r '.Buckets[].Name'
```

### Example 9: Create JSON from Shell Variables
```bash
# Build JSON object from variables in Termux
NAME="John"
AGE=30
CITY="NYC"

jq -n \
  --arg name "$NAME" \
  --arg age "$AGE" \
  --arg city "$CITY" \
  '{name: $name, age: ($age | tonumber), city: $city}'
# Output: {"name":"John","age":30,"city":"NYC"}
```

### Example 10: Monitor System Information (Termux)
```bash
# Parse termux-battery-status
termux-battery-status | jq '{
  percentage: .percentage,
  status: .status,
  temperature: .temperature
}'

# Parse termux-sensor data
termux-sensor -s "Accelerometer" -n 1 | jq '.sensors[0].values'

# Get WiFi info
termux-wifi-connectioninfo | jq '{
  ssid: .ssid,
  ip: .ip,
  speed: .link_speed_mbps
}'
```

---

## Common Patterns in Termux

### Pattern 1: Process API Data and Save
```bash
# Fetch, process, and save data
curl -s "https://api.example.com/data" | \
  jq '[.items[] | {id, name, value}]' > processed_data.json
```

### Pattern 2: Pretty Print and Page Through Large JSON
```bash
# Use less for pagination
cat large_file.json | jq '.' | less -R
```

### Pattern 3: Stream Processing Large Files
```bash
# Process streaming JSON (one object per line)
cat stream.ndjson | jq -c 'select(.status == "active")'

# Use --stream for very large files (SAX-like parsing)
jq --stream 'select(length == 2 and .[0][0] == "name") | .[1]' huge.json
```

### Pattern 4: Interactive JSON Exploration
```bash
# Use jq with pipe chains to explore data step by step
cat data.json | jq 'keys'  # See what keys exist
cat data.json | jq '.items | length'  # Check array length
cat data.json | jq '.items[0]'  # Examine first item
cat data.json | jq '.items[0] | keys'  # See item structure
```

### Pattern 5: Validate JSON
```bash
# Check if file is valid JSON
jq empty file.json && echo "Valid JSON" || echo "Invalid JSON"

# Check and format in one command
jq '.' file.json > formatted.json 2>/dev/null && echo "Success" || echo "Failed"
```

### Pattern 6: Create Test Data
```bash
# Generate array of test objects
jq -n '[range(10) | {id: ., name: "user\(.)", active: (. % 2 == 0)}]'

# Output:
# [
#   {"id":0,"name":"user0","active":true},
#   {"id":1,"name":"user1","active":false},
#   ...
# ]
```

### Pattern 7: Quick JSON Queries in Scripts
```bash
#!/data/data/com.termux/files/usr/bin/bash

# Get configuration value from JSON config file
CONFIG_FILE="config.json"
DB_HOST=$(jq -r '.database.host' "$CONFIG_FILE")
DB_PORT=$(jq -r '.database.port' "$CONFIG_FILE")

echo "Connecting to $DB_HOST:$DB_PORT"
```

### Pattern 8: Transform and POST JSON
```bash
# Transform local data and POST to API
cat local_data.json | \
  jq '{items: [.[] | {name, value: (.value * 100)}]}' | \
  curl -X POST -H "Content-Type: application/json" \
    -d @- https://api.example.com/upload
```

### Pattern 9: Diff Two JSON Files
```bash
# Compare two JSON files after sorting
diff <(jq -S '.' file1.json) <(jq -S '.' file2.json)

# Show only changed fields
jq -s '.[0] * .[1]' file1.json file2.json
```

### Pattern 10: Extract Specific Data Pattern
```bash
# Find all email addresses in complex JSON
jq -r '.. | select(type == "string") | select(test("@"))' data.json

# Find all URLs
jq -r '.. | select(type == "string") | select(test("^https?://"))' data.json
```

---

## Performance Tips

### 1. Use Raw Output When Possible
```bash
# Faster for large datasets
jq -r '.[]' data.json
```

### 2. Compile Filters for Reuse
```bash
# Save filter to file
echo '.items[] | select(.active) | {id, name}' > filter.jq

# Use it
jq -f filter.jq data.json
```

### 3. Streaming for Large Files
```bash
# For multi-gigabyte files
jq --stream 'select(.[0][0] == "items") | .[1]' huge.json
```

### 4. Limit Early
```bash
# Apply filters early to reduce data
jq '.items[] | select(.active)' | jq 'limit(10; .)'
```

---

## Debugging Tips

### 1. Use debug Filter
```bash
# Print intermediate values
echo '{"a":{"b":1}}' | jq '.a | debug | .b'
```

### 2. Break Complex Queries
```bash
# Build query step by step
echo '{"items":[{"val":1},{"val":2}]}' | jq '.items'
echo '{"items":[{"val":1},{"val":2}]}' | jq '.items[]'
echo '{"items":[{"val":1},{"val":2}]}' | jq '.items[] | .val'
```

### 3. Check Types
```bash
# Verify data types
echo '{"data":"123"}' | jq '.data | type'
# Output: "string"
```

---

## Quick Reference Card

| Task | Command |
|------|---------|
| Pretty print | `jq '.'` |
| Extract field | `jq '.field'` |
| Raw output | `jq -r '.field'` |
| Array element | `jq '.[0]'` |
| Array slice | `jq '.[2:5]'` |
| All elements | `jq '.[]'` |
| Filter | `jq '.[] \| select(.age > 25)'` |
| Map | `jq 'map(.field)'` |
| Sort | `jq 'sort_by(.field)'` |
| Group | `jq 'group_by(.field)'` |
| Unique | `jq 'unique'` |
| Keys | `jq 'keys'` |
| Length | `jq 'length'` |
| Add field | `jq '. + {new: "value"}'` |
| Delete field | `jq 'del(.field)'` |
| Merge | `jq -s 'add'` |
| First | `jq 'first'` |
| Last | `jq 'last'` |
| Min/Max | `jq 'min'` / `jq 'max'` |
| Sum | `jq 'add'` |
| Contains | `jq 'contains("text")'` |

---

## Resources

- **Official Manual**: https://jqlang.github.io/jq/manual/
- **GitHub Repository**: https://github.com/jqlang/jq
- **jq Play (Online)**: https://jqplay.org/
- **Tutorial**: https://stedolan.github.io/jq/tutorial/

---

## Version Information

This guide is based on **jq 1.7+** (latest stable as of 2025).

Check your version in Termux:
```bash
jq --version
```

Update jq in Termux:
```bash
pkg update && pkg upgrade jq
```

---

**Last Updated**: October 2025  
**Created for**: Termux environment on Android
```

---

## Detailed Explanation and Usage Guide

### How to Use This Guide in Termux

**jq** is already installed in your Termux, so you can start using it immediately. Here's how to effectively work with this guide:

#### 1. **Basic Usage Pattern**
Every jq command follows this structure:
```bash
echo 'JSON_DATA' | jq 'FILTER'
```
or
```bash
jq 'FILTER' filename.json
```

#### 2. **Understanding Filters**
Filters are the core of jq. Think of them as instructions telling jq what to do with your JSON:
- `.` = "show me everything"
- `.name` = "show me the name field"
- `.[]` = "show me each element"
- `select()` = "filter based on condition"

#### 3. **Practical Learning Path**

**Week 1 - Basics:**
- Start with pretty printing: `jq '.'`
- Extract single fields: `jq '.fieldname'`
- Work with arrays: `jq '.[0]'`, `jq '.[]'`

**Week 2 - Filtering:**
- Use `select()` to filter data
- Learn `map()` for transformations
- Practice with real API responses

**Week 3 - Advanced:**
- Combine filters with pipes `|`
- Create new JSON structures
- Use variables and functions

#### 4. **Real-World Practice Examples for Termux**

**Example A: Monitor Your Phone Battery**
```bash
# Get battery percentage (Termux API required)
termux-battery-status | jq '.percentage'

# Create alert if battery low
BATTERY=$(termux-battery-status | jq '.percentage')
if [ $BATTERY -lt 20 ]; then
  termux-notification --title "Low Battery" --content "Battery at ${BATTERY}%"
fi
```

**Example B: Process Downloaded Data**
```bash
# Download JSON data
curl -s "https://api.github.com/users/torvalds" -o user.json

# Extract interesting information
jq '{
  name: .name,
  followers: .followers,
  repos: .public_repos,
  joined: .created_at
}' user.json
```

**Example C: Configuration Management**
```bash
# Create a config file
cat > myconfig.json <<EOF
{
  "api": {
    "key": "abc123",
    "endpoint": "https://api.example.com"
  },
  "settings": {
    "theme": "dark",
    "notifications": true
  }
}
EOF

# Read config in script
API_KEY=$(jq -r '.api.key' myconfig.json)
THEME=$(jq -r '.settings.theme' myconfig.json)

echo "Using API key: $API_KEY"
echo "Theme: $THEME"
```

**Example D: Log Analysis**
```bash
# Create sample log file (NDJSON - newline delimited JSON)
cat > app.log <<EOF
{"timestamp":"2025-10-28T10:00:00","level":"info","message":"App started"}
{"timestamp":"2025-10-28T10:15:00","level":"error","message":"Connection failed"}
{"timestamp":"2025-10-28T10:30:00","level":"info","message":"App recovered"}
{"timestamp":"2025-10-28T11:00:00","level":"error","message":"Timeout occurred"}
EOF

# Count errors
cat app.log | jq -s '[.[] | select(.level == "error")] | length'

# Get all error messages
cat app.log | jq -r 'select(.level == "error") | .message'

# Create error report
cat app.log | jq -s '[.[] | select(.level == "error")] | {
  total_errors: length,
  messages: [.[] | .message]
}'
```

**Example E: Working with APIs in Scripts**
```bash
#!/data/data/com.termux/files/usr/bin/bash
# weather.sh - Simple weather script

CITY="London"
API_KEY="your_api_key"
URL="https://api.openweathermap.org/data/2.5/weather?q=${CITY}&appid=${API_KEY}"

# Fetch and parse weather data
curl -s "$URL" | jq '{
  city: .name,
  temperature: (.main.temp - 273.15 | round),
  condition: .weather[0].main,
  humidity: .main.humidity
}' | jq -r '"Weather in \(.city): \(.temperature)°C, \(.condition), Humidity: \(.humidity)%"'
```

#### 5. **Common Mistakes and Solutions**

**Mistake 1: Forgetting quotes**
```bash
# Wrong
echo '{"name":"John"}' | jq .name

# Right
echo '{"name":"John"}' | jq '.name'
```

**Mistake 2: Not using -r for raw output**
```bash
# With quotes (default)
echo '{"name":"John"}' | jq '.name'
# Output: "John"

# Without quotes (raw)
echo '{"name":"John"}' | jq -r '.name'
# Output: John
```

**Mistake 3: Wrong array syntax**
```bash
# Wrong - trying to get array element
echo '{"items":[1,2,3]}' | jq '.items[1]'  # This works

# Wrong - for object array
echo '[{"id":1},{"id":2}]' | jq '.id'  # Error!

# Right
echo '[{"id":1},{"id":2}]' | jq '.[].id'  # Returns both IDs
echo '[{"id":1},{"id":2}]' | jq '.[0].id'  # Returns first ID
```

#### 6. **Building Complex Queries Step by Step**

When you have complex JSON, build your query incrementally:

```bash
# Sample complex JSON
cat > data.json <<EOF
{
  "users": [
    {"id": 1, "name": "John", "address": {"city": "NYC", "zip": "10001"}},
    {"id": 2, "name": "Jane", "address": {"city": "LA", "zip": "90001"}}
  ]
}
EOF

# Step 1: Access users array
jq '.users' data.json

# Step 2: Get each user
jq '.users[]' data.json

# Step 3: Get each user's name
jq '.users[].name' data.json

# Step 4: Get name and city
jq '.users[] | {name, city: .address.city}' data.json

# Step 5: Filter by city
jq '.users[] | select(.address.city == "NYC") | {name, city: .address.city}' data.json
```

#### 7. **Combining with Other Termux Tools**

**With grep:**
```bash
# Find JSON files containing specific value
jq -r '.name' *.json | grep "John"
```

**With awk:**
```bash
# Process jq output with awk
jq -r '.items[] | "\(.id),\(.value)"' data.json | awk -F, '{sum+=$2} END {print sum}'
```

**With curl:**
```bash
# Chain API calls
USER_ID=$(curl -s https://api.example.com/users | jq -r '.[0].id')
curl -s "https://api.example.com/users/$USER_ID/details" | jq '.'
```

#### 8. **Creating Helper Functions**

Add these to your `~/.bashrc`:

```bash
# Pretty print JSON from clipboard
alias jpretty='termux-clipboard-get | jq .'

# Quick JSON validation
jvalidate() {
    if jq empty "$1" 2>/dev/null; then
        echo "✓ Valid JSON"
    else
        echo "✗ Invalid JSON"
    fi
}

# Extract field helper
jget() {
    jq -r ".$1" "$2"
}
# Usage: jget name data.json

# Count array elements
jcount() {
    jq 'length' "$1"
}
```

---

## Sources and References

This guide was compiled from the following authoritative sources:

1. **Official jq GitHub Repository**: https://github.com/jqlang/jq
2. **Official jq Manual**: https://jqlang.github.io/jq/manual/
3. **jq Wiki and Documentation**: Latest updates as of October 2025

The guide includes commands and features available in **jq 1.7+**, which is the current stable version as of 2025.

---

I've created a comprehensive markdown file covering jq with:

1. **30+ essential commands** with practical examples
2. **10 real-world scenarios** specific to Termux usage
3. **10 common patterns** for daily workflows
4. **Complete explanation** of how to use each feature
5. **Progressive learning path** from basic to advanced
6. **Debugging tips and common mistakes**
7. **Integration with other Termux tools**
8. **Ready-to-use helper functions**

The guide is updated with the latest jq features as of 2025 and is specifically tailored for Termux environment on Android. You can save this as a `.md` file and refer to it anytime!

Would you like me to explain any specific section in more detail or add more examples for particular use cases?