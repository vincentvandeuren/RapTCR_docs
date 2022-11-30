---
layout: default
title: Indexing
nav_order:  4
---

# Indexing of CDR3 vectors

To efficiently find similar CDR3s in large datasets, their vectors can be
indexed. These indexes store a compact approximation of the vector space (e.g.
using clustering, search trees, etc) that can be used to reduce the search scope
and quickly -- but approximately -- retrieve nearest neighbours for a given
input query.

## Usage

Practically, the wide range of possible indexes can be imported from
`raptcr.indexing` module. These classes all follow the same steps:

1. **Initialize the index.**

    Constructing the index is fairly straightforward, you just have to pass it
    the data and hasher object you want to use.

    ```python
    # import necessary components
    from raptcr.readers import read_AIRR
    from raptcr.hashing import Cdr3Hasher
    from raptcr.indexing import FlatIndex

    # read in data and create hasher
    data = read_AIRR("airr_example.tsv")
    hasher = Cdr3Hasher().fit()

    # initialize index and add data
    index = FlatIndex(hasher=hasher)
    index.add(data)
    ```


2. **Search the constructed index**

    ```python
    query_seqs = ['CASSKRDRGNGGYTF', "CASSITPGQGTDEQYF"]
    knn_result = index.knn_search(query_seqs, k=5)
    ```
    
    This generates a `KnnResult` object containing the search result. The *k*
    nearest neighbours for a specific query sequence can be extracted as
    follows:

     <div class="code-example" markdown=1>
    ```python
    knn_result.extract_neighbours("CASSITPGQGTDEQYF")
    ``` 
    </div>

    ```markdown
    [('CASSITPGQGTDEQYF', 0.0),
    ('CASGITPGQGTDEQYF', 8.944272),
    ('CASSATPGQGRDTQYF', 15.6205),
    ('CASSLPGQGTEQYF', 15.748015),
    ('CASITGPTGNEQYF', 16.370705)]
    ```

    This list in the format `[(seq, distance), ...]` contains the *k* CDR3
    sequences closest to the query in terms of the L2 (euclidean) distance
    between their respective vector representations. 

## Available indexes

| Class name | Explanation | Hyperparams |
|:-----------|:------------|:------------|
| `FlatIndex` | Flat index for Euclidean distance, implemented using [Faiss]. | None |
| `IvfIndex` | Inverted file index implemented using [Faiss]. This uses a rough k-means clustering to group the vectors in centroids. At query time, only a subset of these centroids are probed. | `n_centroids`, `n_probes` |
| `HnswIndex` | Index using hierarchical navigable small networks, implemented using [Faiss]. | `n_links` |
| `PynndescentIndex` | Uses [PyNNDescent] for approximate nearest neighbours descent. | `k`, `diversify_prob`, `pruning_degree_multiplier` |

--------

[Faiss]: https://github.com/facebookresearch/faiss
[PyNNDescent]: https://pynndescent.readthedocs.io/en/latest/