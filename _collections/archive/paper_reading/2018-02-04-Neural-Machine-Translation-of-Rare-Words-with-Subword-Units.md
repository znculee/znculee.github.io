---
layout: post
title: Neural Machine Translation of Rare Words with Subword Units
category: Paper_Reading
comments: false
---

paper link: [https://arxiv.org/pdf/1508.07909.pdf](https://arxiv.org/pdf/1508.07909.pdf)

- toc
{:toc}

# Summary

This paper introduce a variant of [Byte Pair Encoding (BPE)](http://collaboration.cmc.ec.gc.ca/science/rpn/biblio/ddj/Website/articles/CUJ/1994/9402/gage/gage.htm) to encode words as sequences of subword units, making the NMT model capable of open-vocabulary translation.

# Byte Pair Encoding

## Algorithm

### Learn BPE

- Initialize the symbol vocabulary with the character vocabulary, and represent each word as a sequence of characters, plus a special end-of-word symbol `</w>`.
- Iteratively count all symbol pairs and replace each occurrence of the most frequent pair (`A`, `B`) with a new symbol `AB`.
- Each merge operation produces a new symbol which represents a character n-gram.
- Frequent n-grams (or whole words) are eventually merged into a single symbol.
- The final symbol vocabulary size is equal to the size of the initial vocabulary (character vocabulary), plus the number of the merge operations, which is the only hyper-parameter of the algorithm.
- Do not consider pairs that cross word boundaries.
- Each word is weighted by its frequency.

### Apply BPE

- Split words into sequences of characters.
- Apply the learned operations to merge the characters into larger, known symbols.

### Joint BPE

- It improves consistency between the source and the target segmentation, avoiding the same name being segmentated differently in the two languages.
- To increase the consistency between English and Russian segmentation despite the differing alphabets, the authors transliterate the Russian vocabulary into Latin characters with ISO-9 to learn the joint BPE encoding, then transliterate the BPE merge operations back into Cyrillic to apply them to Russian training text.

## Demo

```python
import re

def process_raw_words(words, endtag='-'):
    vocabs = {}
    for word, count in words.items():
        word = re.sub(r'([a-zA-Z])', r' \1', word)
        word += ' ' + endtag
        vocabs[word] = count
    return vocabs

def get_symbol_pairs(vocabs):
    pairs = dict()
    for word, freq in vocabs.items():
        symbols = word.split()
        for i in range(len(symbols) - 1):
            p = (symbols[i], symbols[i + 1])
            pairs[p] = pairs.get(p, 0) + freq
    return pairs

def merge_symbols(symbol_pair, vocabs):
    vocabs_new = {}
    raw = ' '.join(symbol_pair)
    merged = ''.join(symbol_pair)
    bigram =  re.escape(raw)
    p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
    for word, count in vocabs.items():
        word_new = p.sub(merged, word)
        vocabs_new[word_new] = count
    return vocabs_new

raw_words = {'low':5, 'lower':2, 'newest':6, 'widest':3, 'eailer':3}
vocabs = process_raw_words(raw_words)

num_merges = 19
print(vocabs)
for i in range(num_merges):
    pairs = get_symbol_pairs(vocabs)
    symbol_pair = max(pairs, key=pairs.get)
    vocabs = merge_symbols(symbol_pair, vocabs)
    print(vocabs)
```

## View as Optimization

The open-vocabulary can be understand as a vocabulary, whose words can fully construct the whole dataset and none of them is rare.

$$
\ell = \frac{\text{entropy of vocabulary distribution}}{\text{vocabulary size}}
$$

BPE can be viewed as a greed search to maximize the vocabulary size. The best number of merge operations may have the lowest loss $\ell$.
