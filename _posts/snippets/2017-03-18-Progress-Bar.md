---
layout: blog
title: Progress Bar
category: Snippets
---

- toc
{:toc}

# Python

## Minimal

```python
'''
ProgressBar of python
'''

from __future__ import division

import sys
import subprocess
import time

class ProgressBar:
    def __init__(self, total):
        self.count = 0
        self.total = total

    def move(self, message=None):
        self.count += 1
        self.width = min(int(subprocess.check_output(['tput', 'cols'])) - 10, 50)
        sys.stdout.write(' ' * (self.width + 9) + '\r')
        sys.stdout.flush()
        if message:
            print message
        progress = self.width * self.count // self.total
        sys.stdout.write('#' * progress + '-' * (self.width - progress) + ' {0:.2f}%'.format(self.count * 100 / self.total) + '\r')
        if progress == self.width:
            sys.stdout.write(' ' * (self.width + 9) + '\r')
        sys.stdout.flush()

    def reset(self):
        self.count = 0

if __name__ == '__main__':
    # demo
    TOTAL = 45
    bar = ProgressBar(TOTAL)
    for i in range(TOTAL):
        # bar.move('message: ' + '{}'.format(i))
        bar.move()
        time.sleep(0.1)
```

## [progressbar2](https://pypi.org/project/progressbar2/)

## [tqdm](https://github.com/tqdm/tqdm)
