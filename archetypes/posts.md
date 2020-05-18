---
date: '{{ now.Format "Monday, January 2, 2006"}}'
title: '{{ .File.TranslationBaseName | replaceRE "-" " " | title }}'
draft: true
---