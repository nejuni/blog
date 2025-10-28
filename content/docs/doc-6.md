---
title: "Document 6"
description: "Fuck You"
date: 2025-10-20T10:00:00Z
lastmod: 2025-10-27T11:39:00Z
tags: ["tutorial", "doc", "fck"]
draft: false
---

# Info

The user wants to:
1. Serve Markdown (.md) files on localhost:8081 or private LAN using Hugo in Termux
2. Make the .md files look like GitHub README style
3. Create a static and fast server
4. Make files searchable by tags only (to reduce load)
5. Learn how to assign tags to different .md files
6. They want detailed steps for Termux on Android (aarch64 architecture)
7. They're not tech-savvy, so I need to explain everything clearly

I need to provide:
- Step-by-step installation of Hugo in Termux
- How to set up a Hugo site
- How to make it look like GitHub README
- How to add tags to markdown files
- How to create tag-based search
- How to serve it on localhost:8081

I'll help you set up a static website to serve your Markdown files with a GitHub-like appearance using Hugo on Termux. Let me break this down into simple steps.

## What You're Building

You'll create a local website that:
- Shows your Markdown files like GitHub READMEs
- Runs on your Android device at `localhost:8081`
- Can be accessed by other devices on your network
- Uses tags for organizing and searching files
- Runs fast because it's static (pre-built HTML files)

## Step 1: Install Termux and Update It

1. Install Termux from F-Droid (not Google Play - the Play Store version is outdated)
   - Download F-Droid: https://f-droid.org
   - Search for "Termux" and install it

2. Open Termux and run these commands one by one:
```bash
pkg update -y
pkg upgrade -y
```

## Step 2: Install Required Packages

Run these commands to install Hugo and Git:
```bash
pkg install hugo git -y
```

## Step 3: Create Your Hugo Site

1. Create a new Hugo site:
```bash
cd ~
hugo new site my-markdown-server
cd my-markdown-server
```

2. Install a GitHub-style theme (we'll use "github-style"):
```bash
git init
git submodule add https://github.com/MeiK2333/github-style themes/github-style
```

## Step 4: Configure Your Site

1. Edit the configuration file:
```bash
nano config.toml
```

2. Replace everything in the file with:
```toml
baseURL = "http://localhost:8081/"
languageCode = "en-us"
title = "My Markdown Files"
theme = "github-style"

[params]
  author = "Your Name"
  description = "My Markdown Collection"
  
[taxonomies]
  tag = "tags"

[server]
  [[server.headers]]
    for = "/**"
    [server.headers.values]
      Access-Control-Allow-Origin = "*"
```

3. Save the file (press `Ctrl+X`, then `Y`, then `Enter`)

## Step 5: Create Your First Markdown File with Tags

1. Create a content folder:
```bash
mkdir -p content/posts
```

2. Create your first Markdown file:
```bash
nano content/posts/my-first-file.md
```

3. Add this content (this shows how to add tags):
```markdown
---
title: "My First Document"
date: 2025-01-20
tags: ["tutorial", "example", "getting-started"]
---

# Welcome to My First Document

This is a sample Markdown file. It will look like a GitHub README!

## Features

- Bullet points work
- **Bold text** works
- *Italic text* works
- `Code snippets` work

### Code blocks work too:

```python
print("Hello, World!")
```
```

4. Save the file (`Ctrl+X`, `Y`, `Enter`)

## Step 6: Customize for GitHub Look

1. Create a custom CSS file:
```bash
mkdir -p static/css
nano static/css/custom.css
```

2. Add this CSS for better GitHub styling:
```css
body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
    line-height: 1.6;
}

.markdown-body {
    box-sizing: border-box;
    min-width: 200px;
    max-width: 980px;
    margin: 0 auto;
    padding: 45px;
}

pre {
    background-color: #f6f8fa;
    border-radius: 6px;
    padding: 16px;
    overflow: auto;
}

code {
    background-color: rgba(175, 184, 193, 0.2);
    padding: 0.2em 0.4em;
    border-radius: 3px;
}
```

3. Save the file

## Step 7: Create a Simple Tag Search Page

1. Create a layout for tags:
```bash
mkdir -p layouts/_default
nano layouts/_default/list.html
```

2. Add this simple HTML:
```html
<!DOCTYPE html>
<html>
<head>
    <title>{{ .Title }}</title>
    <link rel="stylesheet" href="/css/custom.css">
    <style>
        .tag-list { padding: 20px; }
        .tag { 
            display: inline-block; 
            background: #0969da; 
            color: white; 
            padding: 5px 10px; 
            margin: 5px;
            border-radius: 20px;
            text-decoration: none;
        }
        .post-list { list-style: none; padding: 20px; }
        .post-item { margin: 10px 0; padding: 10px; border: 1px solid #e1e4e8; border-radius: 6px; }
    </style>
</head>
<body>
    <div class="markdown-body">
        <h1>{{ .Title }}</h1>
        
        {{ if eq .Kind "taxonomy" }}
            <h2>All Tags:</h2>
            <div class="tag-list">
                {{ range .Site.Taxonomies.tags }}
                    <a href="/tags/{{ .Page.Title | urlize }}/" class="tag">{{ .Page.Title }}</a>
                {{ end }}
            </div>
        {{ end }}
        
        <h2>Posts:</h2>
        <ul class="post-list">
            {{ range .Pages }}
                <li class="post-item">
                    <a href="{{ .Permalink }}">{{ .Title }}</a>
                    <br>
                    Tags: 
                    {{ range .Params.tags }}
                        <a href="/tags/{{ . | urlize }}/" class="tag">{{ . }}</a>
                    {{ end }}
                </li>
            {{ end }}
        </ul>
    </div>
</body>
</html>
```

3. Save the file

## Step 8: Start Your Server

1. Run Hugo server on port 8081:
```bash
hugo server -D --bind 0.0.0.0 -p 8081 --baseURL http://localhost:8081
```

2. To access from other devices on your network:
   - Find your IP address: `ip addr | grep 192`
   - Other devices can access: `http://YOUR-IP:8081`

## How to Add More Markdown Files

For each new Markdown file:

1. Create a new file:
```bash
nano content/posts/your-filename.md
```

2. Always start with this format (front matter):
```markdown
---
title: "Your Document Title"
date: 2025-01-20
tags: ["tag1", "tag2", "tag3"]
---

Your markdown content here...
```

## Understanding Tags

- Tags help organize your files by topic
- Each file can have multiple tags
- Click any tag to see all files with that tag
- This reduces server load compared to full-text search

## Tips for Managing Your Server

1. **To stop the server**: Press `Ctrl+C`

2. **To run in background**:
```bash
nohup hugo server -D --bind 0.0.0.0 -p 8081 &
```

3. **To auto-start when Termux opens**, add to `.bashrc`:
```bash
echo 'cd ~/my-markdown-server && hugo server -D --bind 0.0.0.0 -p 8081 &' >> ~/.bashrc
```

## Troubleshooting

If you get errors:
- Make sure you're in the right directory: `cd ~/my-markdown-server`
- Check if port 8081 is free: `killall hugo` then try again
- For permission errors: `termux-setup-storage` and grant storage permission

Your Markdown files will now be served with a GitHub-like appearance, searchable by tags!