layout: post
title: "List and Search all keys Ubuntu Unity Gsettings"
date: 2016-06-25 11:15:28
tags:
- Gsettings
categories:
- 方案
---

Listing all window manager key bindings in sorted order:

```
gsettings list-recursively org.gnome.desktop.wm.keybindings | sort
```

Filtering the the key binding for <Control><Alt>s:

```
gsettings list-recursively org.gnome.desktop.wm.keybindings | grep "<Control><Alt>s"
```
