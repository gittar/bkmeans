# The Breathing *K*-Means Algorithm

An approximation algorithm for the *k*-means problem that (on average) is **better** (higher solution quality) and **faster** (lower CPU time usage) than  ***k*-means++** (in the widely-used implementation from `scikit-learn` and with `n_init=10`, the longtime default value for repetitions). 

**Remark**: Version 1.4 of `scikit-learn` (released 1/2024) introduced a new default value `n_init=1` for ***k*-means++**. As a consequence, the advantage of **breathing *k*-means** in solution quality has become even larger but ***k*-means++** is faster now (by sacrificing solution quality). 

**Technical Report:**
https://arxiv.org/abs/2006.15666 

**Example Code:** [Google Colab notebook](https://colab.research.google.com/drive/1tT3lPJQqSIzQ7NuIhRa8DlSU11lO9jgo?usp=sharing)

## API
The included class **BKMeans** is subclassed from [scikit-learn's **KMeans** class](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)
and has, therefore, **the same API**. It can be used as a plug-in replacement for scikit-learn's **KMeans**. 

There is one new parameter that can be ignored (meaning: left at default) for normal usage:

* *m* (breathing depth), default: 5

The parameter *m* can also be used, however, to generate faster ( 1 < *m* < 5) or better (*m*>5) solutions. For details see the above technical report.


## Release Notes
### Version 1.3
* bug fix: BKMeans.predict(df) now matches BKMeans.labels_
* bug fix: BKMeans is now fully deterministic if parameter random_state is set to non-Null
* compatibility with scikit-learn: unused parameter *y* was not set in the `fit` method

Remark: the fixed bugs did not affect the quality of the codebook results in previous version

### Version 1.2
* make use of the optional `sample_weight` parameter in the `fit` method 
* (contributed by Björn Wiescholek)

### Version 1.1
* speed improvement by setting `n_init=1` by default
* close centroids now defined by nearest neighbor criterion
* parameter `theta` abolished

### Version 1.0
* (initial release)
* "close centroids" were based on distance and a parameter *theta*

## Example 1: running on a simple random data set
Code:
```python
import numpy as np
from bkmeans import BKMeans

# generate random data set
X=np.random.rand(1000,2)

# create BKMeans instance
bkm = BKMeans(n_clusters=100)

# run the algorithm
bkm.fit(X)

# print SSE (inertia in scikit-learn terms)
print(bkm.inertia_)
```
Output:
```
1.1775040547902602
```

## Example 2: comparison with *k*-means++ (multiple runs)
Code:
```python
import numpy as np
from sklearn.cluster import KMeans
from bkmeans import BKMeans
import time

# random 2D data set
X=np.random.rand(1000,2)

# number of centroids
k=100

for i in range(5):
    # kmeans++
    kmp = KMeans(n_clusters=k, n_init=10)
    kmp.fit(X)

    # breathing k-means
    bkm = BKMeans(n_clusters=k)
    bkm.fit(X)

    # relative SSE improvement of bkm over km++
    imp = 1 - bkm.inertia_/kmp.inertia_
    print(f"SSE improvement over k-means++: {imp:.2%}")
```
Output:

```
SSE improvement over k-means++: 3.38%
SSE improvement over k-means++: 4.16%
SSE improvement over k-means++: 6.14%
SSE improvement over k-means++: 6.79%
SSE improvement over k-means++: 4.76%
```


