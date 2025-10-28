---
title: "Document 11"
description: "Fuck You"
date: 2025-10-20T10:00:00Z
lastmod: 2025-10-27T11:39:00Z
tags: ["tutorial", "doc", "fck"]
draft: false
---

## Info
The search output format isn't being generated. Let me fix the `hugo.toml` configuration:

### Step 1: Update `hugo.toml` completely

```toml
baseURL = "http://localhost:8083"
languageCode = "en-us"
title = "My Documentation"
theme = "PaperMod"

# Performance settings
minify = false
enableGitInfo = true
ignoreCache = true

# Force rebuild
buildDrafts = true
buildFuture = true
buildExpired = true

# Output formats
[outputs]
home = ["HTML", "RSS", "JSON"]
section = ["HTML"]
page = ["HTML"]

[outputFormats.JSON]
baseName = "index"
mediaType = "application/json"
isPlainText = true

# Taxonomies for tag filtering
[taxonomies]
tag = "tags"
category = "categories"

[markup.highlight]
style = "monokai"
lineNos = false

[markup.goldmark.renderer]
unsafe = true

[params]
description = "Fast, beautiful documentation site"
showBreadcrumbs = false
showPostNavLinks = true
ShowCodeCopyButtons = true
ShowToc = true
TocOpen = true
defaultTheme = "auto"
hideFooter = false

  [params.homeInfoParams]
    Title = "My Documentation"
    Content = "A beautiful, fast, and minimal documentation site built with Hugo"

  [[params.socialIcons]]
    name = "github"
    url = "https://github.com"

  [[params.socialIcons]]
    name = "twitter"
    url = "https://twitter.com"

  [[params.socialIcons]]
    name = "rss"
    url = "index.xml"
```

### Step 2: Create the search page with output format

Create `~/my-docs/content/search/_index.md`:

```markdown
---
title: "Search"
description: "Full text search"
outputs:
  - html
  - json
---
```

### Step 3: Create the JSON layout for search page

Create `~/my-docs/layouts/search/index.json`:

```json
{{- $pages := .Site.RegularPages -}}
[
{{- range $i, $page := $pages }}
  {{- if and $page.Title $page.Plain }}
  {
    "id": {{ $i }},
    "title": {{ $page.Title | jsonify }},
    "content": {{ (($page.Plain | truncate 2000) | replace "\n" " ") | jsonify }},
    "description": {{ ($page.Description | default "") | jsonify }},
    "date": {{ ($page.Date.Format "2006-01-02") | jsonify }},
    "url": {{ $page.Permalink | jsonify }},
    "tags": {{ ($page.Params.tags | default (slice)) | jsonify }},
    "section": {{ $page.Section | jsonify }}
  }{{- if ne $i (sub (len $pages) 1) }},{{- end }}
  {{- end }}
{{- end }}
]
```

### Step 4: Create the search HTML layout

Create `~/my-docs/layouts/search/index.html`:

```html
{{- define "main" }}

<style>
  .search-container {
    max-width: 900px;
    margin: 0 auto;
    padding: 40px 20px;
  }

  .search-header {
    text-align: center;
    margin-bottom: 40px;
  }

  .search-header h1 {
    font-size: 2.5rem;
    margin: 0 0 15px 0;
    font-weight: 700;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .search-header p {
    color: #666;
    font-size: 1rem;
  }

  .search-box-wrapper {
    position: relative;
    margin-bottom: 40px;
  }

  .search-input {
    width: 100%;
    padding: 16px 20px;
    font-size: 1rem;
    border: 2px solid #ddd;
    border-radius: 12px;
    outline: none;
    transition: all 0.3s ease;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  }

  .search-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
  }

  .search-input::placeholder {
    color: #999;
  }

  .search-icon {
    position: absolute;
    right: 16px;
    top: 50%;
    transform: translateY(-50%);
    color: #999;
    font-size: 1.2rem;
  }

  .search-results {
    margin-top: 40px;
  }

  .search-results-info {
    font-size: 0.95rem;
    color: #666;
    margin-bottom: 20px;
  }

  .search-results-info strong {
    color: #667eea;
    font-weight: 600;
  }

  .result-item {
    padding: 20px;
    background: rgba(0, 0, 0, 0.02);
    border-radius: 12px;
    border-left: 4px solid #667eea;
    margin-bottom: 16px;
    transition: all 0.3s ease;
    cursor: pointer;
  }

  .result-item:hover {
    transform: translateX(5px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
  }

  .result-title {
    font-size: 1.15rem;
    font-weight: 600;
    margin: 0 0 8px 0;
  }

  .result-title a {
    text-decoration: none;
    color: #333;
  }

  .result-title a:hover {
    color: #667eea;
  }

  .result-description {
    color: #666;
    font-size: 0.95rem;
    margin-bottom: 10px;
    line-height: 1.5;
  }

  .result-snippet {
    background: #fff;
    padding: 10px;
    border-radius: 6px;
    font-size: 0.9rem;
    color: #555;
    margin-bottom: 10px;
    border-left: 3px solid #667eea;
    font-style: italic;
  }

  .result-meta {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 10px;
    font-size: 0.85rem;
    color: #888;
  }

  .result-date {
    display: flex;
    align-items: center;
    gap: 5px;
  }

  .result-tags {
    display: flex;
    gap: 6px;
    flex-wrap: wrap;
  }

  .result-tag {
    background: #667eea;
    color: white;
    padding: 2px 8px;
    border-radius: 12px;
    text-decoration: none;
    font-size: 0.8rem;
  }

  .result-tag:hover {
    background: #764ba2;
  }

  .empty-state {
    text-align: center;
    padding: 60px 20px;
    color: #999;
  }

  .empty-state-icon {
    font-size: 3rem;
    margin-bottom: 20px;
  }

  .no-results {
    text-align: center;
    padding: 60px 20px;
  }

  .no-results-icon {
    font-size: 3rem;
    margin-bottom: 20px;
  }

  .no-results h2 {
    color: #666;
    margin: 20px 0 10px 0;
  }

  .no-results p {
    color: #999;
  }

  .loading {
    text-align: center;
    padding: 40px 20px;
    color: #999;
  }

  .loading-spinner {
    display: inline-block;
    width: 30px;
    height: 30px;
    border: 3px solid #ddd;
    border-top: 3px solid #667eea;
    border-radius: 50%;
    animation: spin 1s linear infinite;
    margin-bottom: 15px;
  }

  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }

  @media (max-width: 768px) {
    .search-header h1 {
      font-size: 2rem;
    }

    .search-container {
      padding: 20px;
    }

    .result-meta {
      flex-direction: column;
      align-items: flex-start;
    }
  }
</style>

<div class="search-container">
  <div class="search-header">
    <h1>🔍 Search</h1>
    <p>Full text search with fuzzy matching</p>
  </div>

  <div class="search-box-wrapper">
    <input 
      type="text" 
      id="searchInput" 
      class="search-input" 
      placeholder="Search documentation... (try 'bash', 'tutorial', 'guide')"
      autocomplete="off"
    >
    <span class="search-icon">🔎</span>
  </div>

  <div id="loadingState" class="loading" style="display: none;">
    <div class="loading-spinner"></div>
    <p>Loading search index...</p>
  </div>

  <div id="searchResults" class="search-results"></div>

  <div id="emptyState" class="empty-state">
    <div class="empty-state-icon">📚</div>
    <p>Enter a search term to begin</p>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/fuse.js@7.0.0/dist/fuse.min.js"></script>
<script>
  let searchIndex = [];
  let fuse = null;
  const BASE_URL = '{{ .Site.BaseURL }}';

  async function loadSearchIndex() {
    const loadingState = document.getElementById('loadingState');
    const emptyState = document.getElementById('emptyState');
    
    loadingState.style.display = 'block';
    
    try {
      const indexUrl = BASE_URL + 'search/index.json';
      console.log('Loading from:', indexUrl);
      
      const response = await fetch(indexUrl);
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      
      const data = await response.json();
      console.log('Data loaded:', data);
      
      searchIndex = Array.isArray(data) ? data : [];

      searchIndex = searchIndex
        .filter(item => item && item.title && item.content)
        .map(item => ({
          id: item.id || Math.random(),
          title: String(item.title || ''),
          content: String(item.content || ''),
          description: String(item.description || ''),
          url: String(item.url || ''),
          tags: Array.isArray(item.tags) ? item.tags.filter(t => t) : [],
          date: String(item.date || ''),
          section: String(item.section || '')
        }));

      console.log('✓ Index ready:', searchIndex.length, 'documents');

      if (searchIndex.length === 0) {
        loadingState.innerHTML = `
          <div class="empty-state-icon">⚠️</div>
          <p>No documents found. Add markdown files to content/docs/</p>
        `;
        return;
      }

      fuse = new Fuse(searchIndex, {
        keys: [
          { name: 'title', weight: 0.4 },
          { name: 'description', weight: 0.3 },
          { name: 'content', weight: 0.2 },
          { name: 'tags', weight: 0.1 }
        ],
        includeScore: true,
        threshold: 0.3,
        minMatchCharLength: 1
      });

      loadingState.style.display = 'none';
      console.log('✓ Fuse.js ready');
    } catch (error) {
      console.error('Error:', error);
      loadingState.innerHTML = `
        <div class="empty-state-icon">❌</div>
        <p>Error: ${error.message}</p>
      `;
    }
  }

  function getSnippet(content, query, length = 150) {
    if (!content) return '';
    const idx = content.toLowerCase().indexOf(query.toLowerCase());
    if (idx === -1) return content.substring(0, length) + '...';
    const start = Math.max(0, idx - 50);
    const end = Math.min(content.length, idx + length);
    let snippet = content.substring(start, end);
    if (start > 0) snippet = '...' + snippet;
    if (end < content.length) snippet = snippet + '...';
    return snippet;
  }

  function performSearch(query) {
    const resultsContainer = document.getElementById('searchResults');
    const emptyState = document.getElementById('emptyState');
    
    resultsContainer.innerHTML = '';

    if (!query.trim()) {
      emptyState.style.display = 'block';
      return;
    }

    emptyState.style.display = 'none';

    if (!fuse) {
      resultsContainer.innerHTML = '<p style="text-align: center; color: #999;">Search not ready yet...</p>';
      return;
    }

    const results = fuse.search(query).slice(0, 20);

    if (results.length === 0) {
      resultsContainer.innerHTML = `
        <div class="no-results">
          <div class="no-results-icon">🔍</div>
          <h2>No results found</h2>
          <p>Try different keywords</p>
        </div>
      `;
      return;
    }

    const info = document.createElement('div');
    info.className = 'search-results-info';
    info.innerHTML = `Found <strong>${results.length}</strong> result${results.length !== 1 ? 's' : ''} for <strong>"${query}"</strong>`;
    resultsContainer.appendChild(info);

    results.forEach(result => {
      const item = result.item;
      const snippet = getSnippet(item.content, query);
      
      const el = document.createElement('div');
      el.className = 'result-item';
      
      let tagsHtml = '';
      if (item.tags && item.tags.length > 0) {
        tagsHtml = item.tags.map(tag => 
          `<a href="${BASE_URL}tags/${tag.toLowerCase().replace(/\s+/g, '-')}/" class="result-tag">${tag}</a>`
        ).join('');
      }

      el.innerHTML = `
        <div class="result-title">
          <a href="${item.url}">${item.title}</a>
        </div>
        ${item.description ? `<div class="result-description">${item.description}</div>` : ''}
        ${snippet ? `<div class="result-snippet">"${snippet}"</div>` : ''}
        <div class="result-meta">
          <div class="result-date">📅 ${item.date}</div>
          ${tagsHtml ? `<div class="result-tags">${tagsHtml}</div>` : ''}
        </div>
      `;
      
      resultsContainer.appendChild(el);
    });
  }

  document.getElementById('searchInput').addEventListener('input', (e) => {
    performSearch(e.target.value);
  });

  loadSearchIndex();
</script>

{{- end }}
```

### Step 5: Clean rebuild

```bash
cd ~/my-docs
rm -rf public/ resources/_gen/
hugo
```

### Step 6: Check the output

```bash
ls -la ~/my-docs/public/search/
cat ~/my-docs/public/search/index.json | python3 -m json.tool | head -50
```

### Step 7: Start server

```bash
cd ~/my-docs
hugo server --disableFastRender --ignoreCache --noHTTPCache --baseURL="http://localhost:8083" --port=8083
```

Now test:
- `http://localhost:8083/search/index.json` - Should show JSON array
- `http://localhost:8083/search/` - Should search work

The key changes:
✅ Added explicit `outputs` in `/search/_index.md`  
✅ Created separate `search/index.json` layout  
✅ Fixed `hugo.toml` to define JSON format properly  
✅ Updated search script to use correct path

Let me know what you see! 🔍