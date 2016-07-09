layout: post
title: "随机权重分布的Python实现"
date: 2015-05-15 16:58:00
tags:
- Python
categories:
- 算法
---

原文：<http://www.electricmonk.nl/log/2009/12/23/weighted-random-distribution/>

Randomly selecting elements from a set of items is easy. Just generate a random number between 0 and the length of the set minus one, and use that as an index in the set (if it is an array) to get a random entry. The chance that an entry is picked is the same for each entry in the set. This is called even distribution or uniform distribution.

But what if we do not want each entry to appear as much as every other? Suppose we're creating a question-answer game, and we want the questions the user got wrong previously to appear more often than the question he or she got right? This is called a Weighted Random Distribution, or sometimes Weighted Random Choice, and there are multiple methods of implementing such as random picker.

This article explains these various methods of implementing Weighted Random Distribution along with their pros and cons. We use Python as our language of choice, because it has an easy to read syntax, and provides many useful tools which would take many more lines of code in most other languages. Along the way all Python "tricks" will be explained.

分为几种实现：

### Expanding

    #!/usr/bin/python

    import random

    weights = {
            'A': 2,
            'B': 4,
            'C': 3,
            'D': 1
    }

    dist = []
    for x in weights.keys():
            dist += weights[x] * x

    results = {}
    for i in range(100000):
            wRndChoice = random.choice(dist)
            results[wRndChoice] = results.get(wRndChoice, 0) + 1

    print results

### In-Place(Unsorted)

    #!/usr/bin/python

    import random

    weights = {
            'A': 2,
            'B': 4,
            'C': 3,
            'D': 1
    }

    wTotal = sum(weights.values())

    results = {}
    for i in range(100000):
            wRndNr = random.randint(0, wTotal - 1)
            s = 0
            for w in weights.items():
                    s += w[1]
                    if s > wRndNr:
                            break;
            results[w[0]] = results.get(w[0], 0) + 1

    print results

### In-Place(Sorted)

    results = {}
    for i in range(100000):
            wRndNr = random.randint(0, wTotal - 1)
            s = wTotal
            for i in xrange(len(sWeights) - 1, -1, -1):
                    wRndChoice = sWeights[i]
                    s -= wRndChoice[1]
                    if s <= wRndNr:
                            break
            results[wRndChoice[0]] = results.get(wRndChoice[0], 0) + 1
    print results

### Pre-calculation

    wTotal = 0
    sWeights = []
    for w in weights.items():
        wTotal += w[1]
        sWeights.append( (wTotal, w[0]) )
        results = {}
    for i in range(100000):
        wRndNr = random.randint(0, wTotal - 1)
        wRndChoice = sWeights[bisect.bisect(sWeights, (wRndNr, None))]

        results[wRndChoice[1]] = results.get(wRndChoice[1], 0) + 1
