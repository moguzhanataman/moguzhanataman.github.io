---
layout: post
title: Run Elixir/Phoenix iex REPL with Kamal
---

Add this to your `deploy.yml`

```yml
aliases:
  iex: app exec --interactive --reuse "./bin/YOUR_APP_NAME remote"
```

run

```bash
kamal iex
```
