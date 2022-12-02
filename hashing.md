---
layout: default
title: Hashing
nav_order: 3
---

# Hashing CDR3 sequences

Most indexing structures for efficient similarity search (e.g. [Faiss]) require
a set of vectors with equal length as input. Yet, the length of CDR3 regions
varies widely. Adressing this issue, we developed a hashing algorithm that
transforms CDR3 sequences of different lengths into vectors of length 64. Our
algorithm is locality sensitive: **similar CDR3 sequences will translate to
similar vectors**, approximating pairwise alignment and BLOSUM62-based scoring.

*Explanation of hashing method here*

## Usage

All hashing functionality is contained within the `Cdr3Hasher()` class, which
behaves like a [scikit-learn transformer]. Let's start by importing and
initiating the class.

```python
from raptcr.hashing import Cdr3Hasher
cdr3_hasher = Cdr3Hasher(pos_p=0.5, clip=0)
```

There are two noteworthy hyperparameters:

- `pos_p` defines the importance of the relative position of each AA in the CDR3
  for the final hash. If this parameter is increased, slightly modifying the
  location of a certain AA in the CDR3 will have a larger effect on the final
  hash. From our experiments, 0.5 came out as an optimal value.
- `clip` can be used to trim the CDR3s, hence ignoring the first and last *n* AAs.

Next, we can fit our hasher. This method requires no parameters.

```python
cdr3_hasher.fit()
```

{: .note}

Each `.fit()` will result in a different hasher; hence, maker sure to
only call this method once. 

The resulting fitted hasher can then be used in various analyses. Most require
you to directly provide the Cdr3Hasher object, although you can also generate
the hashes yourself using the `.transform()` method. Here, you can pass a single
CDR3 sequence, but also a list or a full `Repertoire` object.

<div class="code-example" markdown=1>

```python
cdr3_hasher.transform("CASSVQSGLNEKLFF")
``` 
</div>

```markdown
array([ 3,  3,  3, -1, -1, -3, -1,  1,  7, -5,  3,  3,  1, -1, -3,  1, -3,
       -1,  7,  1, -3,  1, -3,  3,  3,  5,  3,  3,  1, -1, -1, -3, -5, -3,
       -5,  1,  1,  3,  3,  3,  5, -3,  1, -3,  3, -1,  1,  3,  5, -1, -5,
       -1, -1, -1, -5, -1,  7,  5, -3, -3,  3,  3,  3, -3])
```

or


<div class="code-example" markdown=1>
```python
repertoire = read_AIRR('filepath')
cdr3_hasher.transform(repertoire)
``` 
</div>

```markdown
array([[ 0,  8,  6, ...,  8,  4, -2],
       [ 0, 10,  4, ...,  2,  4, -4],
       [ 3,  9,  5, ...,  3,  1, -9],
       ...,
       [ 2,  4,  6, ...,  2,  0, -6],
       [ 0,  0,  6, ...,  2, -4, -8],
       [-2,  4,  4, ..., -2, -4, -4]])
```


---
[Faiss]:https://faiss.ai/
[ClusTCR]:https://svalkiers.github.io/clusTCR/
[scikit-learn transformer]: https://scikit-learn.org/stable/data_transforms.html