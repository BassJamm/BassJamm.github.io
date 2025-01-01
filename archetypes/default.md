---
draft: false
date: '{{ .Date }}'
lastmod: '{{ .Date }}' # Optional; will be updated by GitInfo if enabled
title: '{{ replace .File.ContentBaseName "-" " " | title }}'
description: "Description"
summary: "Summary"
author: "BassJamm"
tags: ["new"]
categories: ["How To", "TLDR", "CheatSheet"]
showToc: true
hidemeta: false
canonicalURL: "https://canonical.url/to/page"
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/BassJamm/BassJamm.github.io/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
