# The Breathing *K*-Means Algorithm

## A novel approximation algorithm for the *k*-means problem

**Breathing *k*-means** finds solutions which
* are usually better than solutions from ***k*-means++**
* are lower-bounded by ***k*-means++** (the worst possible result *is* a solution from ***k*-means++**)
* require more computation time than ***k*-means++** (ca 50%-300% depending on the problem)

The second claim is fulfilled, since 
the default seeding method of **breathing *k*-means** *is* ***k*-means++**.  If no improvement is found by **breathing *k*-means**, it simply returns the ***k*-means++** result used as seed.

The more interesting part about **breathing *k*-means** is probably its ability to improve upon ***k*-means++** in many cases and to do so at moderate  extra computational cost  (considering that the *k*-means problem is NP-hard).

## Further Info
For a detailed motivation and description of the **breathing *k*-means algorithm** see the preprint https://arxiv.org/abs/2006.15666. 

An extended software repo including the data sets used in the above preprint and jupyter notebooks to experiment with them can be found at https://github.com/gittar/breathing-k-means

## API
The included class **BKMeans** is subclassed from [scikit-learn's **KMeans** class](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)
and has thus the same API, only extended by two parameters which, however, can be ignored for normal usage and thus simply left at their default values:

* *m* (breathing depth), default: 5, (m=0 is equivalent to running k-means++)
* *theta* (neighborhood freezing range), default: 1.1


## Installation

```bash
pip install bkmeans
```
## Example 1: running on simple random data set
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

# random 2D data set
X=np.random.rand(1000,2)

# number of centroids
k=100

for i in range(5):
    # kmeans++
    km = KMeans(n_clusters=k)
    km.fit(X)

    # breathing k-means
    bkm = BKMeans(n_clusters=k)
    bkm.fit(X)

    # relative SSE improvement of bkm over km++
    imp = 1 - bkm.inertia_/km.inertia_
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


