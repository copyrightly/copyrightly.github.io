---
layout: default
title: "Problem 3189 optimal transport III"
permalink: /problem3189-optimal-transport-III
---

# Optimal Transport III

[Problem 3189](https://leetcode.com/problems/minimum-moves-to-get-a-peaceful-board/): On an `n × n` chessboard, there are `n` rooks placed, and their positions are given by a 2D array `rooks` of length `n`. You can move a rook horizontally or vertically to an adjacent position. The question is: what is the minimum number of moves required to ensure that each row and each column contains exactly one rook (at no point should two rooks overlap)?

An important observation is that moving a rook vertically does not change the number of rooks in that column, and similarly, moving a rook horizontally does not change the number of rooks in that row. Therefore, we can consider the horizontal and vertical movements independently. Specifically, we can first make vertical moves to ensure that each row contains exactly one rook, and then make horizontal moves to ensure that each column contains exactly one rook. This approach simplifies the problem into two separate one-dimensional movement problems.

Taking vertical movement as an example, we count the number of rooks in each row. Then, using a greedy algorithm, we move the excess rooks in each row to the nearest empty row. It's important to note that which specific rook is moved within a row does not affect the outcome. After considering both vertical and horizontal movements separately, the final answer is the sum of the optimal number of moves in both directions. The specific code for this solution is as below.
```python
def minMoves(rooks: List[List[int]]) -> int:
    def min_moves(distribution):
        res = 0
        empty_pos = []
        taken_pos = []
        for i, cnt in enumerate(distribution):
            if cnt == 0:
                if taken_pos:
                    res += i - taken_pos.pop()
                else:
                    empty_pos.append(i)
            else:
                for _ in range(min(cnt - 1, len(empty_pos))):
                    res += i - empty_pos.pop()
                    cnt -= 1
                if cnt > 1:
                    taken_pos.extend([i for _ in range(cnt - 1)])
        return res
    n = len(rooks)
    row_distribution = [0 for _ in range(n)]
    col_distribution = [0 for _ in range(n)]
    for r, c in rooks:
        row_distribution[r] += 1
        col_distribution[c] += 1
    return min_moves(row_distribution) + min_moves(col_distribution)
```

Building on the idea of reducing the problem to one dimension, we can consider the problem from another angle. We counted the number of rooks in each row, but how many moves are required to ensure that each row has exactly one rook? This is, in fact, an optimal transport problem. For an introduction to optimal transport and related issues, you can refer to previous problems [1066](https://copyrightly.github.io/problem1066-optimal-transport-I) and [979](https://copyrightly.github.io/problem979-optimal-transport-II). In this problem, the initial distribution is the number of rooks in each row, and the target distribution is uniform—meaning exactly one rook per row. The cost of moving is defined by the distance between rows (with the distance between two adjacent rows being 1).

We can solve this using the [`ot.emd`](https://pythonot.github.io/all.html#ot.emd) function from the [POT](https://pythonot.github.io/) (Python Optimal Transport) package. The relevant code is shown below.
```python
def get_optimal_transport_cost(distribution1, distribution2, locations1, locations2):
    cost_matrix = ot.dist(
        locations1.reshape(-1, 1), locations2.reshape(-1, 1), "euclidean"
    )
    optimal_plan = ot.emd(distribution1, distribution2, cost_matrix)
    total_cost = np.sum(optimal_plan * cost_matrix)
    return total_cost

def minMoves(rooks):
    n = len(rooks)
    row_distribution = [0 for _ in range(n)]
    col_distribution = [0 for _ in range(n)]
    for r, c in rooks:
        row_distribution[r] += 1
        col_distribution[c] += 1

    uniform_distribution = np.array([1.0 for _ in range(n)]) / n
    uniform_locations = np.array([i for i in range(n)])
    row_min_moves = get_optimal_transport_cost(
        np.array(row_distribution) / n,
        uniform_distribution,
        uniform_locations,
        uniform_locations,
    )
    col_min_moves = get_optimal_transport_cost(
        np.array(col_distribution) / n,
        uniform_distribution,
        uniform_locations,
        uniform_locations,
    )
    return int((row_min_moves + col_min_moves) * n)

minMoves([[0,0],[0,1],[0,2],[0,3]])
# get 6
```
The complete code with testing can be found in this [colab notebook](https://colab.research.google.com/drive/14fWTBJX32jFNX5tR6JVKFA2okqvyBjKN#scrollTo=-n5VOqyfYwg8).

It’s worth noting that using OT (Optimal Transport) here is not an efficient algorithm: the complexity of calculating the distance (cost matrix) between all rows is `O(n^2)`, and the complexity of the emd function is `O(n^3 logn)`. In contrast, the initial greedy solution we presented has a complexity of only `O(n)`. We are simply providing another perspective to approach the problem.

[back](/math-and-algo)
