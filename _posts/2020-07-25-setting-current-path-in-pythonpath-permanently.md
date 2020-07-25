---
layout: post
title:  "Setting current path to `PYTHONPATH` permanently"
date:   2020-07-25 13:41:11 +0530
categories: python
---

A neat trick to always include the current working/running directory in `PYTHONPATH`.

### Windows

1. Go to the folder where your `python.exe` is present. In my case, `C:\Program Files\Python38`

2. Rename your `python.exe` to something like `python3.exe`. I'm renaming it to `python38.exe` because it's v3.8

3. Create a file called `python.bat` in that same folder and put the following:

```
%echo off
set PYTHONPATH=%CD%;%PYTHONPATH%
python38 %*
```

That's it. Now whenever you're running `python` command, the current directory will be included in `PYTHONPATH` by default.
