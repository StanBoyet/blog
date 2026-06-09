---
layout: snippet
title: "Personal and work git on one machine"
subtitle: "Some needed hygiene for working in a professional environment without the overhead"
author: stan
tags: [ git, github ]
excerpt: "One GitHub account, two emails, and a conditional include so git picks the right identity based on the folder a repo lives in."
---

Add both emails to a single GitHub account and verify them (**Settings → Emails**) — GitHub attributes a commit to your profile by matching the author email. Then let git pick the identity by folder: anything under `~/work/` is professional, everything else is personal. Clone by intent and you never touch `user.email` again.

### `~/.gitconfig`

```ini
[include]
  path = ~/.gitconfig-perso

[includeIf "gitdir:~/work/"]
  path = ~/.gitconfig-pro
```

### `~/.gitconfig-perso`

```ini
[user]
  name = Your Name
  email = you@personal-email.com
  signingkey = BA07F563XXXXXXXX
```

### `~/.gitconfig-pro`

```ini
[user]
  name = Your Name
  email = you@company.com
  signingkey = 522A0BC5XXXXXXXX
```

The trailing slash on `gitdir:~/work/` matches the folder and everything under it; the override must come _after_ the default include to win. Check it:

```bash
$ cd ~/perso/blog && git config user.email
you@personal-email.com

$ cd ~/work/acme-api && git config user.email
you@company.com
```
