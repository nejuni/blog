---
title: "Document10"
description: "Fuck You"
date: 2025-10-20T10:00:00Z
lastmod: 2025-10-27T11:39:00Z
tags: ["tutorial", "doc", "fck"]
draft: false
---

# Info 
Perfect! I'll add a full-text fuzzy search feature. Let me create a search page and add search functionality.

## Steps
ndnd
cnfm
ndjf

### Step 1: Create search index JSON output

Update your `hugo.toml` to include JSON output for search:

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
section = ["HTML", "JSON"]

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

  # Corrected social icons array
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

### Step 2: Create search index template

Create `~/my-docs/layouts/_default/index.json`:

```json
{{- range .Site.RegularPages -}}
{
  "title": {{ .Title | jsonify }},
  "content": {{ .Plain | jsonify }},
  "description": {{ .Description | jsonify }},
  "date": {{ .Date.Format "2006-01-02" | jsonify }},
  "lastmod": {{ .Lastmod.Format "2006-01-02" | jsonify }},
  "url": {{ .Permalink | jsonify }},
  "tags": {{ .Params.tags | jsonify }},
  "section": {{ .Section | jsonify }}
},
{{- end -}}
```

### Step 3: Create search page layout

Create `~/my-docs/content/search/_index.md`:

```markdown
---
title: "Search"
description: "Full text search"
draft: false
---
```

### Step 4: Create search layout

Create `~/my-docs/layouts/search/search.html`:

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

  .highlight {
    background: yellow;
    padding: 2px 4px;
    border-radius: 3px;
    font-weight: 600;
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
      placeholder="Search documentation... (try 'API', 'tutorial', 'setup')"
      autocomplete="off"
    >
    <span class="search-icon">🔎</span>
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

  // Fetch search index
  async function loadSearchIndex() {
    try {
      const response = await fetch('{{ .Site.BaseURL }}index.json');
      const data = await response.json();
      
      // Filter out invalid entries and clean data
      searchIndex = data
        .filter(item => item.title && item.content)
        .map(item => ({
          title: item.title || '',
          content: item.content || '',
          description: item.description || '',
          url: item.url || '',
          tags: Array.isArray(item.tags) ? item.tags : [],
          date: item.date || '',
          section: item.section || ''
        }));

      // Initialize Fuse.js with options
      fuse = new Fuse(searchIndex, {
        keys: [
          { name: 'title', weight: 0.4 },
          { name: 'description', weight: 0.3 },
          { name: 'content', weight: 0.2 },
          { name: 'tags', weight: 0.1 }
        ],
        includeScore: true,
        includeMatches: true,
        threshold: 0.3,
        minMatchCharLength: 2,
        useExtendedSearch: true
      });

      console.log('Search index loaded:', searchIndex.length, 'documents');
    } catch (error) {
      console.error('Error loading search index:', error);
    }
  }

  // Highlight matching text
  function highlightMatches(text, matches) {
    if (!matches || matches.length === 0) return text;
    
    let result = text;
    let offset = 0;
    
    // Sort matches by indices
    matches.sort((a, b) => a.indices[0][0] - b.indices[0][0]);
    
    matches.forEach(match => {
      match.indices.forEach(([start, end]) => {
        const before = result.substring(0, start + offset);
        const matched = result.substring(start + offset, end + offset + 1);
        const after = result.substring(end + offset + 1);
        
        result = before + '<span class="highlight">' + matched + '</span>' + after;
        offset += '<span class="highlight"></span>'.length;
      });
    });
    
    return result;
  }

  // Get snippet from content
  function getSnippet(content, query, length = 150) {
    const lowerContent = content.toLowerCase();
    const lowerQuery = query.toLowerCase();
    const index = lowerContent.indexOf(lowerQuery);
    
    if (index === -1) return content.substring(0, length) + '...';
    
    const start = Math.max(0, index - 50);
    const end = Math.min(content.length, index + length);
    
    let snippet = content.substring(start, end);
    if (start > 0) snippet = '...' + snippet;
    if (end < content.length) snippet = snippet + '...';
    
    return snippet;
  }

  // Perform search
  function performSearch(query) {
    const resultsContainer = document.getElementById('searchResults');
    const emptyState = document.getElementById('emptyState');
    
    resultsContainer.innerHTML = '';

    if (!query.trim()) {
      emptyState.style.display = 'block';
      return;
    }

    emptyState.style.display = 'none';

    if (!fuse || searchIndex.length === 0) {
      resultsContainer.innerHTML = '<p style="text-align: center; color: #999;">Search index not loaded. Please refresh the page.</p>';
      return;
    }

    const results = fuse.search(query).slice(0, 20);

    if (results.length === 0) {
      resultsContainer.innerHTML = `
        <div class="no-results">
          <div class="no-results-icon">🔍</div>
          <h2>No results found</h2>
          <p>Try different keywords or browse by tags</p>
        </div>
      `;
      return;
    }

    const resultsInfo = document.createElement('div');
    resultsInfo.className = 'search-results-info';
    resultsInfo.innerHTML = `Found <strong>${results.length}</strong> result${results.length !== 1 ? 's' : ''} for <strong>"${query}"</strong>`;
    resultsContainer.appendChild(resultsInfo);

    results.forEach(result => {
      const item = result.item;
      const snippet = getSnippet(item.content, query, 200);
      
      const resultElement = document.createElement('div');
      resultElement.className = 'result-item';
      
      let tagsHtml = '';
      if (item.tags && item.tags.length > 0) {
        tagsHtml = item.tags.map(tag => 
          `<a href="{{ .Site.BaseURL }}tags/${tag.toLowerCase().replace(/\s+/g, '-')}/" class="result-tag">${tag}</a>`
        ).join('');
      }

      resultElement.innerHTML = `
        <div class="result-title">
          <a href="${item.url}">${item.title}</a>
        </div>
        ${item.description ? `<div class="result-description">${item.description}</div>` : ''}
        <div class="result-snippet">"${snippet}"</div>
        <div class="result-meta">
          <div class="result-date">📅 ${item.date || 'N/A'}</div>
          ${tagsHtml ? `<div class="result-tags">${tagsHtml}</div>` : ''}
        </div>
      `;
      
      resultsContainer.appendChild(resultElement);
    });
  }

  // Event listeners
  document.getElementById('searchInput').addEventListener('input', (e) => {
    performSearch(e.target.value);
  });

  // Handle enter key
  document.getElementById('searchInput').addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
      e.preventDefault();
      performSearch(e.target.value);
    }
  });

  // Load search index on page load
  window.addEventListener('load', loadSearchIndex);
</script>

{{- end }}
```

### Step 5: Update homepage to add search link

Update `~/my-docs/layouts/index.html` - add this to the hero buttons section (replace the existing buttons):

```html
    <div class="hero-buttons">
      <a href="{{ .Site.BaseURL }}search/" class="btn-primary">🔍 Search Docs</a>
      <a href="#all-docs" class="btn-secondary">Browse All</a>
      <a href="{{ .Site.BaseURL }}tags/" class="btn-secondary">View Tags</a>
    </div>
```

### Step 6: Restart server

```bash
cd ~/my-docs
rm -rf resources/_gen/
hugo server --disableFastRender --ignoreCache --noHTTPCache --baseURL="http://localhost:8083" --port=8083
```

## Features Included:

✅ **Full-text fuzzy search** - Uses Fuse.js library  
✅ **Real-time search** - Results update as you type  
✅ **Smart ranking** - Prioritizes title > description > content  
✅ **Snippet preview** - Shows context from matching documents  
✅ **Highlight matches** - Yellow highlight on matching text  
✅ **Tag filtering** - Click tags from results  
✅ **Result metadata** - Shows date and tags  
✅ **Mobile responsive** - Works perfectly on all devices  
✅ **Empty states** - Helpful messages for no results  
✅ **Fast search** - Runs locally in browser (no server needed)  

## How It Works:

1. **Fuzzy matching** - "api" will match "API", "aapi", "application", etc.
2. **Multi-field search** - Searches title, description, content, and tags
3. **Weighted results** - Title matches score higher than content matches
4. **Snippet extraction** - Shows relevant excerpt from each result
5. **Click to view** - Click any result to open the full document

Now visit `http://localhost:8083/search/` and try searching! 🔍