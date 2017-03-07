---
layout: blog
title: Sentence BLEU
category: Snippets
---

- toc
{:toc}

```python
#!/usr/bin/env python3

import argparse
import contextlib
import os

from nltk.translate.bleu_score import sentence_bleu, SmoothingFunction

def main(args):
    with open(args.hyp, 'r') as fhyp, open(args.ref, 'r') as fref:
        for hyp, ref in zip(fhyp, fref):
            with open(os.devnull, "w") as f, contextlib.redirect_stderr(f):
                bleu = sentence_bleu(
                    [ref.strip().split()],
                    hyp.split(),
                    smoothing_function=SmoothingFunction().method7
                )
                print(bleu)

def parse_args():
    parser = argparse.ArgumentParser()
    parser.add_argument('hyp')
    parser.add_argument('ref')
    args = parser.parse_args()
    return args

if __name__ == '__main__':
    main(parse_args())
```
