---
title: "Document 8"
description: "Fuck You"
date: 2025-10-20T10:00:00Z
lastmod: 2025-10-27T11:39:00Z
tags: ["tutorial", "doc", "fck"]
draft: false
---

# Add Tag Cloud & Sort by Last Modified

## Step 1: Update `hugo.toml`

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
ShowToc = true
TocOpen = true

  [params.homeInfoParams]
  Title = "Welcome to Docs"
  Content = "Click tags below or browse all documentation"

  [params.socialIcons]
  # Add social links if needed
```

## Step 2: Create Custom Homepage Template

Create a new file: `layouts/index.html`

```html
{{- define "main" }}

<article class="post-single">
  <header class="post-header">
    <h1 class="post-title">
      {{ .Site.Params.homeInfoParams.Title }}
    </h1>
  </header>
  <section class="post-content">
    <p>{{ .Site.Params.homeInfoParams.Content }}</p>
  </section>
</article>

<!-- Tag Cloud Section -->
<div class="tag-cloud-container" style="margin: 40px 0; padding: 20px; background: rgba(0,0,0,0.02); border-radius: 8px;">
  <h2 style="margin-top: 0;">Browse by Tags</h2>
  <div class="tag-cloud" style="display: flex; flex-wrap: wrap; gap: 8px; align-items: center;">
    {{- range $name, $taxonomy := .Site.Taxonomies.tags }}
      {{- $count := len $taxonomy.Pages }}
      {{- $size := 0.9 }}
      {{- if gt $count 5 }}
        {{- $size = 1.2 }}
      {{- end }}
      {{- if gt $count 10 }}
        {{- $size = 1.4 }}
      {{- end }}
      <a href="{{ .Site.BaseURL }}tags/{{ $name | urlize }}/" 
         class="tag-item"
         style="
           font-size: {{ $size }}rem;
           padding: 8px 12px;
           background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
           color: white;
           border-radius: 20px;
           text-decoration: none;
           transition: all 0.3s ease;
           display: inline-block;
           font-weight: 500;
         "
         onmouseover="this.style.transform='scale(1.1)'; this.style.boxShadow='0 4px 12px rgba(0,0,0,0.15)';"
         onmouseout="this.style.transform='scale(1)'; this.style.boxShadow='none';">
        {{ $name }} <span style="margin-left: 4px; opacity: 0.8;">({{ $count }})</span>
      </a>
    {{- end }}
  </div>
</div>

<!-- All Documents Sorted by Last Modified -->
<article class="post-single">
  <header class="post-header">
    <h2>All Documents (Latest Modified First)</h2>
  </header>
  
  <div class="post-content">
    <ul style="list-style: none; padding: 0;">
      {{- range (sort .Site.RegularPages "Lastmod" "desc") }}
      <li style="margin-bottom: 12px; padding: 12px; background: rgba(0,0,0,0.02); border-radius: 6px; border-left: 3px solid #667eea;">
        <a href="{{ .Permalink }}" style="text-decoration: none; font-weight: 600;">
          {{ .Title }}
        </a>
        <br>
        <small style="color: #666;">
          📅 Modified: <time>{{ .Lastmod.Format "2006-01-02 15:04" }}</time>
          | 🏷️ 
          {{- range .Params.tags }}
            <a href="{{ .Site.BaseURL }}tags/{{ . | urlize }}/" style="text-decoration: none;">
              <span style="background: #eee; padding: 2px 6px; border-radius: 3px; margin: 0 2px;">{{ . }}</span>
            </a>
          {{- end }}
        </small>
      </li>
      {{- end }}
    </ul>
  </div>
</article>

{{- end }}
```

## Step 3: Fix Lastmod in Your MD Files

Update your `add-files-simple.sh` to include `lastmod`:

```bash
#!/bin/bash
# save as: add-files-simple.sh

TAGS=("setup" "tutorial" "api" "advanced" "troubleshooting" "performance" "security")

for i in {1..100}; do
  TAG_INDEX=$((i % ${#TAGS[@]}))
  TAGS_STR="${TAGS[$TAG_INDEX]}"
  
  filename="content/docs/doc-$i.md"
  
  # Create staggered dates (newest first)
  HOURS=$((100 - i))
  DATE="2025-10-27T$((10 + (HOURS / 60) % 14)):$((HOURS % 60)):00Z"
  
  cat > "$filename" << EOF
---
title: "Document $i"
description: "Documentation file number $i"
date: 2025-10-20T10:00:00Z
lastmod: $DATE
tags: ["$TAGS_STR", "doc"]
draft: false
---

# Document $i

This is content for document $i - Modified at $DATE

\`\`\`bash
# Example code
echo "Document $i"
\`\`\`

## Section 1

Content here...

## Section 2

More content...
EOF
  
  echo "Created: $filename with lastmod: $DATE"
done
```

Run it:

```bash
chmod +x add-files-simple.sh
./add-files-simple.sh
```

## Step 4: Update Existing Files with Lastmod

If you already have files, add `lastmod` to each frontmatter:

```bash
#!/bin/bash
# Quick fix: Add lastmod to all files

for file in content/docs/doc-*.md; do
  # Extract doc number
  NUM=$(basename "$file" .md | sed 's/doc-//')
  HOURS=$((100 - NUM))
  DATE="2025-10-27T$((10 + (HOURS / 60) % 14)):$((HOURS % 60)):00Z"
  
  # Insert lastmod after date line
  sed -i "/^date:/a lastmod: $DATE" "$file"
done

echo "Updated all files with lastmod"
```

```bash
chmod +x update-lastmod.sh
./update-lastmod.sh
```

## Step 5: Test it

```bash
# Clear cache
rm -rf resources/ public/

# Restart server
hugo server -p 8083 -D --bind 0.0.0.0
```

Visit: `http://localhost:8083`

## What You'll See

✅ **Homepage shows:**
- Welcome message
- **Tag Cloud** - all tags with counts, larger if more posts
- **All Documents** - sorted by last modified (newest first)
- Each doc shows: title, modified date, and tags

✅ **Interactive:**
- Click any tag → filters to only that tag's posts
- Hover tags on homepage → scale animation
- Mobile responsive

✅ **Tag Pages:**
- `/tags/setup/` → all setup posts
- `/tags/api/` → all api posts

## Advanced: Sort Tag Pages by Lastmod Too

Create: `layouts/taxonomy/tags.html`

```html
{{- define "main" }}

<header class="page-header">
  <h1>Posts tagged with "{{ .Title }}"</h1>
</header>

<div class="post-content">
  <ul style="list-style: none; padding: 0;">
    {{- range (sort .Pages "Lastmod" "desc") }}
    <li style="margin-bottom: 12px; padding: 12px; background: rgba(0,0,0,0.02); border-radius: 6px;">
      <a href="{{ .Permalink }}" style="font-weight: 600;">{{ .Title }}</a>
      <br>
      <small style="color: #666;">
        📅 Modified: <time>{{ .Lastmod.Format "2006-01-02 15:04" }}</time>
      </small>
    </li>
    {{- end }}
  </ul>
</div>

{{- end }}
```

Now everything sorts by last modified! 🚀