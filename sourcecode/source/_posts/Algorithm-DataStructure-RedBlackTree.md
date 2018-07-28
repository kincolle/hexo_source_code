---
title: Red Black Tree
date: 2018-05-02 10:34:32
tags:
categories:
- Algorithm
- DataStructure
- Red Black Tree
---
## 1. Introduction of Red Black Tree
I copy an introduction from [wiki](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree):


>A red–black tree is a kind of self-balancing binary search tree in computer science. Each node of the binary tree has an extra bit, and that bit is often interpreted as the color (red or black) of the node. These color bits are used to ensure the tree remains approximately balanced during insertions and deletions.
>
>Balance is preserved by painting each node of the tree with one of two colors in a way that satisfies certain properties, which collectively constrain how unbalanced the tree can become in the worst case. When the tree is modified, the new tree is subsequently rearranged and repainted to restore the coloring properties. The properties are designed in such a way that this rearranging and recoloring can be performed efficiently.
>
>The balancing of the tree is not perfect, but it is good enough to allow it to guarantee searching in O(log n) time, where n is the total number of elements in the tree. The insertion and deletion operations, along with the tree rearrangement and recoloring, are also performed in O(log n) time.
>
>Tracking the color of each node requires only 1 bit of information per node because there are only two colors. The tree does not contain any other data specific to its being a red–black tree so its memory footprint is almost identical to a classic (uncolored) binary search tree. In many cases, the additional bit of information can be stored at no additional memory cost.

And in fact I have introduced Red Black Tree in [here](https://kincolle.github.io/2018/03/11/Collection-TreeMap/). You can see the detail of Red Black Tree by click [here](https://kincolle.github.io/2018/03/11/Collection-TreeMap/)  