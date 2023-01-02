# multiprocessing で距離行列の計算

```py
from multiprocessing import Pool
from tqdm import tqdm
import numpy as np


n_point = 1000
coordinate = np.random.random(size=(n_point, 2))
dist = np.zeros(shape=(n_point, n_point))


def calc_dist(args):
    i, j = args
    a, b = coordinate[i], coordinate[j]
    return i, j, np.sum((a-b)**2)**0.5


def index_list():
    for i in range(n_point-1):
        for j in range(i+1, n_point):
            yield i, j

            
with Pool(processes=8) as pool:
    for i, j, d in tqdm(pool.imap_unordered(calc_dist, index_list()), total=n_point*(n_point-1)/2):
        dist[i, j] = dist[j, i] = d

print(dist[:4, :4])
```