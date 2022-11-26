---
layout: default
title: Indexing
nav_order:  4
---

# Creating an index

Hashed sequences can be indexed for efficient similarity search. RapTCR provides
several different kinds of these indexes, spanning from exact (slower) and
approximate (but faster). 

All implemented indexes follow the same pattern:

1. Initialize the index.

Pass the fitted CDR3 hasher

2. Search using `idx.knn_search`