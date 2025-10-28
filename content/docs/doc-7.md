---
title: "Document 7"
description: "Fuck You"
date: 2025-10-20T10:00:00Z
lastmod: 2025-10-27T11:39:00Z
tags: ["tutorial", "doc", "fck"]
draft: false
---

# Hugo Website Setup for Termux (2025)

I'll guide you through creating a fast, optimized Hugo site in Termux that serves 100+ MD files with tag filtering.

## Prerequisites & Installation

```bash
# Update Termux
pkg update && pkg upgrade -y

# Install Hugo (latest stable)
pkg install hugo -y

# Verify installation
hugo version
```

## Step 1: Create Hugo Project Structure

```bash
# Create new site
hugo new site my-docs
cd my-docs

# Initialize git (optional but recommended)
git init
```

## Step 2: Add a Modern, Fast Theme

Use **PaperMod** - it's lightweight, fast, and perfect for documentation:

```bash
# Add theme as submodule (or direct clone)
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

## Step 3: Configure Hugo (`hugo.toml`)

```toml
baseURL = "http://localhost:8083"
languageCode = "en-us"
title = "My Documentation"
theme = "PaperMod"

# Performance settings
minify = true
enableGitInfo = true

# Output formats
[outputs]
home = ["HTML", "RSS", "JSON"]

# Taxonomies for tag filtering
[taxonomies]
tag = "tags"
category = "categories"

[markup.highlight]
style = "monokai"
lineNos = false

[params]
description = "Fast documentation site"
showBreadcrumbs = true
showPostNavLinks = true
ShowCodeCopyButtons = true

  [params.homeInfoParams]
  Title = "Welcome"
  Content = "Browse documentation by tags"

  [params.socialIcons]
  # Add social links if needed
```

## Step 4: Create Content Structure

```bash
# Create content directory
mkdir -p content/docs

# Example: Create first markdown file with tags
cat > content/docs/guide-setup.md << 'EOF'
---
title: "Setup Guide"
description: "How to set up the environment"
date: 2025-10-27
tags: ["setup", "tutorial", "beginner"]
categories: ["guides"]
draft: false
---

# Setup Guide

## Installation Steps

Your markdown content here...

```bash
echo "Code blocks are easy to copy"
```

More content...
EOF
```

## Step 5: Directory Structure Overview

```
my-docs/
├── config.toml          # Main configuration
├── content/
│   └── docs/
│       ├── guide-setup.md
│       ├── advanced-topic.md
│       └── api-reference.md
├── layouts/             # Custom templates (optional)
├── static/              # Static files
├── themes/
│   └── PaperMod/
└── public/              # Built site (generated)
```

## Step 6: Batch Add 100 MD Files with Tags

Create a script to generate sample files:

```bash
#!/bin/bash
# save as: add-files.sh

TAGS=("setup" "tutorial" "api" "advanced" "troubleshooting" "performance" "security")

for i in {1..100}; do
  # Rotate tags
  TAG_INDEX=$((i % ${#TAGS[@]}))
  TAGS_STR="${TAGS[$TAG_INDEX]}"
  
  filename="content/docs/doc-$i.md"
  
  cat > "$filename" << EOF
---
title: "Document $i"
description: "Documentation file number $i"
date: 2025-10-27T0$((i % 24)):$((i % 60)):00Z
tags: ["$TAGS_STR", "doc"]
draft: false
---

# Document $i

This is content for document $i.

\`\`\`bash
# Example code
echo "Document $i"
\`\`\`

## Section 1

Content here...

## Section 2

More content...
EOF
  
  echo "Created: $filename"
done
```

Run it:

```bash
chmod +x add-files.sh
./add-files.sh
```

## Step 7: Run on Localhost:8083

```bash
# Development server with live reload
hugo server -p 8083 -D --bind 0.0.0.0

# For LAN access (replace 192.168.x.x with your Termux IP)
# hugo server -p 8083 --bind 192.168.1.x --baseURL "http://192.168.1.x:8083"
```

Find your Termux IP:

```bash
hostname -I
# or
ifconfig
```

## Step 8: Access Website

- **Localhost**: `http://localhost:8083`
- **LAN**: `http://192.168.1.x:8083` (use your actual IP)
- **Tags page** (automatic): `/tags/`
- **Filter by tag**: `/tags/setup/` or `/tags/api/`

## Overall Workflow

```
User visits site
       ↓
Homepage shows all docs (sorted by date)
       ↓
Click on any tag → filters posts
       ↓
Search/browse by category
       ↓
Click post → renders beautifully with copy-able code blocks
```

## Adding New Files Going Forward

**Quick method (one file):**

```bash
hugo new docs/my-new-topic.md
```

Edit the generated file:

```bash
nano content/docs/my-new-topic.md
```

**Add frontmatter:**

```markdown
---
title: "My Topic"
tags: ["setup", "tutorial"]
draft: false
---

Your content...
```

**Batch method (multiple files with tags):**

```bash
# Create a CSV then convert to MD files
# Or use the script above and modify it
```

## Performance Tips for 100+ Files

```bash
# Build static site for production
hugo -d public

# Serve static files (even faster)
cd public
python -m http.server 8083
```

## Troubleshooting in Termux

```bash
# If permission denied
termux-setup-storage

# If Hugo not found
which hugo

# Clear cache and rebuild
rm -rf public/
hugo

# Check for dependency issues
hugo doctor
```

## Key Features Already Included

✅ **Fast** - Hugo generates static HTML instantly  
✅ **Tag Filtering** - Built-in taxonomy system  
✅ **Code Copy Buttons** - PaperMod theme includes this  
✅ **Responsive** - Mobile-friendly by default  
✅ **No Dependencies** - Just Hugo + theme  
✅ **GitHub-like rendering** - Markdown renders professionally  

## Next Steps

1. Install Hugo in Termux
2. Create site and add theme
3. Run `add-files.sh` to populate 100 docs
4. Start server on port 8083
5. Access from LAN
6. Add new files with tags as needed

This setup uses **zero npm dependencies** and works perfectly in Termux!