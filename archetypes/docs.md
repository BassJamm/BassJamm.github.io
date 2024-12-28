---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
last_modified: {{ .Date }}
draft: false
description: ""
edit_with_github_link: "https://github.com/your-repo/edit/main/content/docs/{{ .Name }}.md"
---

Last Update:
<time datetime="{{ .Page.Lastmod.Format "Mon Jan 10 17:13:38 2020 -0700" }}" class="text-muted">
  {{ $.Page.Lastmod.Format "January 02, 2006" }}
</time>
