---
title: "Document 9"
description: "Fuck You"
date: 2025-10-20T10:00:00Z
lastmod: 2025-10-27T11:39:00Z
tags: ["tutorial", "doc", "fck"]
draft: false
---

# Info
The error is because in the tag loop, the context changes to `page.WeightedPages` and `.Site` is no longer available. Let me fix the `index.html`:

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
  <h2 style="margin-top: 0;">📁 Browse by Tags</h2>
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
      <a href="{{ $.Site.BaseURL }}tags/{{ $name | urlize }}/" 
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
    <h2>📚 All Documents (Latest Modified First)</h2>
  </header>
  
  <div class="post-content">
    <ul style="list-style: none; padding: 0;">
      {{- range (sort .Site.RegularPages "Lastmod" "desc") }}
      <li style="margin-bottom: 16px; padding: 16px; background: rgba(0,0,0,0.02); border-radius: 6px; border-left: 4px solid #667eea;">
        <a href="{{ .Permalink }}" style="text-decoration: none; font-weight: 600; font-size: 1.1rem;">
          {{ .Title }}
        </a>
        {{- if .Description }}
        <div style="color: #666; margin: 6px 0; font-size: 0.95rem;">
          {{ .Description }}
        </div>
        {{- end }}
        <div style="margin-top: 8px;">
          <small style="color: #888;">
            📅 Modified: <time datetime="{{ .Lastmod.Format "2006-01-02T15:04:05Z07:00" }}">{{ .Lastmod.Format "2006-01-02 15:04" }}</time>
            {{- if .Params.tags }}
            | 🏷️ 
            {{- range .Params.tags }}
              <a href="{{ $.Site.BaseURL }}tags/{{ . | urlize }}/" style="text-decoration: none;">
                <span style="background: #667eea; color: white; padding: 2px 8px; border-radius: 3px; margin: 0 4px; font-size: 0.85rem;">{{ . }}</span>
              </a>
            {{- end }}
            {{- end }}
          </small>
        </div>
      </li>
      {{- end }}
    </ul>
  </div>
</article>

{{- end }}
```

**Key fix:** Changed `{{ .Site.BaseURL }}` to `{{ $.Site.BaseURL }}` in the tag loop (line 27). The `$` prefix preserves the outer scope context.

Now run:

```bash
cd ~/my-docs
rm -rf resources/_gen/
hugo server --disableFastRender --ignoreCache --noHTTPCache --baseURL="http://localhost:8083" --port=8083
```

This should work now! The `$` is crucial in Hugo templates when you need to access the global context from within a nested loop.