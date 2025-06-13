---
title: "Nahamcon 2025 Free_Flags"
date: 2025-05-24T15:34:30-04:00
categories:
  - Blog
tags:
  - CTF
  - Nahamcon2025
---

```markdown
WOW!! Look at all these free flags!!  
  
But... wait a second... only one of them is right??  
  
**NOTE, bruteforcing flag submissions is still not permitted.**
I will put a "max attempts" limit on this challenge at 1:00 PM 
Pacific to stop participants from automating submissions.
There is only one correct flag, you can find a needle in 
a haystack if you really know what you are looking for.  
  
**Download the file(s) below.**
```

This challenge involved filtering the 3000 entries given in the text file to find the real flag.
From the rules of the CTF almost all the flags unless specified by the CTF follow the format of
**`flag\{[0-9a-f]{32}\}`**. That means a `flag{}` wrapper with a 32-character lowercase hex string inside.
Using the python code below to parse the free_flags.txt to find the right flag.

```python
import re

# Regex to match flag{32 hex characters}
pattern = re.compile(r'^flag\{[0-9a-f]{32}\}$')

with open('free_flags.txt', 'r') as infile:
    flags = infile.read().splitlines()

# Filter flags matching the pattern
valid_flags = [flag for flag in flags if pattern.match(flag)]

print(f"Found {len(valid_flags)} valid flags matching the pattern.")

with open('valid_flags.txt', 'w') as outfile:
    for flag in valid_flags:
        outfile.write(flag + '\n')
```

This gives us
**`flag{ae6b6fb0686ec594652afe9eb6088167}`**
