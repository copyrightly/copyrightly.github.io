---
layout: default
title: "Problem 979 Optimal transport II"
permalink: /problem979-optimal-transport-II
---

# Optimal Transport

[Problem 979](https://leetcode.com/problems/distribute-coins-in-binary-tree/): Given a binary tree with `n` nodes, each node contains a certain number of coins (possibly zero). The total number of coins across all nodes is equal to the number of nodes. You can move coins between adjacent nodes, but each move can only transfer one coin. The question is: what is the minimum number of moves required to ensure that each node has exactly one coin?

One solution is to calculate the number of nodes and coins for each node and all its descendants. The difference between these two values represents the number of moves needed to balance the coins. We can use Depth-First Search (DFS) to compute the required moves for each node, and the final answer will be the sum of all these moves. The code is as blow.
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def distributeCoins(self, root: Optional[TreeNode]) -> int:
        ans = 0
        def dfs(node):
            nonlocal ans
            if not node:
                return 0, 0 # #nodes, #coins
            ln, lc = dfs(node.left)
            rn, rc = dfs(node.right)
            ans += abs(ln - lc) + abs(rn - rc)
            return ln + rn + 1, lc + rc + node.val
        dfs(root)
        return ans
```

Another perspective is to view the coins as a probability distribution on the tree, where the distance between any two adjacent nodes is 1. The problem then becomes finding the minimum total distance to transform the given distribution into a uniform probability distribution. This is a typical Optimal Transport (OT) problem. For an introduction to OT, you can refer to the [post from Problem 1066](https://copyrightly.github.io/problem1066-optimal-transport).

We can use the [`ot.emd2`](https://pythonot.github.io/all.html#ot.emd2) function from the [POT](https://pythonot.github.io/index.html) (Python Optimal Transport) package to solve the OT problem. Three inputs are required: the original probability distribution, the target probability distribution, and the pairwise distances between all nodes (represented as an `n×n` symmetric matrix). The original probability distribution is obtained by dividing the number of coins at each node by `n`. The target distribution is also straightforward: each node has a probability of `1/n`, representing a uniform distribution. The only challenging part is calculating the distances between all nodes. Since the tree cannot be embedded in Euclidean space, we cannot use standard L1 or L2 distances. Instead, we can treat each node as the root in turn and use Breadth-First Search (BFS) to calculate its distance to every other node (see code below, where `nbs` is a dictionary representing the neighbors of each node). Finally, we call the `emd2` function. It is important to note that the result of the function needs to be multiplied by `n` to get the final answer, compensating for the normalization when converting the coin distribution to a probability distribution.

Using OT in this context is not the most efficient algorithm: calculating the distances between all nodes has a complexity of `O(n^2)`, and the `emd2` function has a complexity of `O(n^3 log n)`. In contrast, the DFS solution mentioned earlier has a complexity of only `O(n)`. The purpose here is to present an alternative way to solve the problem.
The following is the code to generate a tree, coin distribution, distance computation and solution using `emd2` (original [Google Colab link](https://colab.research.google.com/drive/1ODlkZugoUu6yImngdVDrV7yRCZjuvqLr#scrollTo=UDeXsBBAe0Xh)).

```python
!pip install pot

import random
from collections import defaultdict
import numpy as np
import matplotlib.pylab as pl
import ot
import ot.plot

def gen_vals(t):
  res = [random.randint(0, t) for _ in range(t)]
  diff = sum(res) - t
  for i in range(t):
    if diff == 0:
      break
    curr = min(diff, res[i])
    res[i] -= curr
    diff -= curr
  return res

def dist(nbs):
  n = len(nbs)
  res = []
  for root in range(n):
    curr = {root}
    last = set()
    d = 0
    curr_d = [0 for _ in range(n)]
    while curr:
      nxt = set()
      for x in curr:
        curr_d[x] = d
        for nb in nbs[x]:
          if nb not in last:
            nxt.add(nb)
      d += 1
      last = curr
      curr = nxt
    res.append(curr_d)
  return res

def gen_graph(t, p):
  nbs = defaultdict(list)
  vals = gen_vals(t)
  curr = [0]
  tree = [str(vals[0])]
  i = 1
  while True:
    last = curr
    if all(x == None for x in curr):
      print("All none in this layer, cannot build graph")
      return None
    curr = []
    for node in last:
      # print("node ", last, node)
      if node != None:
        for _ in range(2):
          pr = random.random()
          # print("pr ", pr, "p ", p)
          if pr <= p:
            curr.append(i)
            tree.append(str(vals[i]))
            nbs[node].append(i)
            nbs[i].append(node)
            i += 1
            if i == t:
              break
          else:
            curr.append(None)
            tree.append("null")
      if i == t:
        break
    if i == t:
      break
  print("vals ", vals)
  # print("\ntree ", [x if isinstance(x, int) else x.replace('"', '') for x in tree])
  print("\ntree", "[" + ", ".join(tree) + "]")
  print("\nnbs ", nbs)
  # print("\ndist ", dist(nbs))
  d = dist(nbs)
  # print(d)
  # d = dist(nbs)
  return vals, tree, nbs, d

n = 10
p = 0.6
vals, tree, nbs, d = gen_graph(n, p)

original_distribution = [1.0 * v / n for v in vals]
normalized_distribution = [1.0 / n for _ in range(n)]
ot_dist = ot.emd2(original_distribution, normalized_distribution, np.array(d)) * n
ot_dist
```

[back](/math-and-algo)
