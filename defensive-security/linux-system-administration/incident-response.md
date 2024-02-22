---
title: incident-response
description: 
published: true
date: 2024-02-22T15:49:23.394Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:34.669Z
---

Processes listening:
```bash
ss -nlp | grep -E 'LISTEN.*(([0-9]+\.){3}[0-9]+|\*|):[0-9]+' | grep -e 'pid=[0-9]*'
```

