---
layout: blog
title: text2speech
category: Snippets
---

- toc
{:toc}
# espeak

```python
#!/usr/bin/env python

'''
brew install espeak (OS X)
sudo apt-get install espeak (Ubuntu)
'''

import os

while True:
    str = raw_input()
    if str == '':
        break
    else:
        os.system('espeak ' + '"' + str + '"')
```

# [vosk](https://alphacephei.com/vosk/)

Vosk is a speech recognition toolkit.
