---
title: "My First Doc"
date: 2024-12-27T19:29:34Z
last_modified: 2024-12-27T19:29:34Z
draft: false
description: "First post testing with a tempalte"
edit_with_github_link: "https://github.com/your-repo/edit/main/content/docs/my-first-doc.md"
---

## Testing things out

Test

## Git info

{{ with .GitInfo }}
    {{ .CommitDate.Format "2006-01-02" }}
    {{ .AbbreviatedHash }} → aab9ec0b3
{{ end }}
