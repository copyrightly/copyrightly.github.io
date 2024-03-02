---
layout: default
title: "Problem 62 dynamic programming and combinatorics"
permalink: /problem62-combinatorics
---

# Dynamic Programming and Combinatorics

[Problem 62](https://leetcode.com/problems/unique-paths/): Given an `m x n` grid, where a robot is initially positioned at the top-left corner `(0, 0)` and it wants to move to the bottom-right corner `(m - 1, n - 1)` of the grid. The robot can only move down or right. How many different paths can the robot take to reach the bottom-right corner?

Since the robot has two choices at each position (moving down or right), a natural solution is to use dynamic programming (DP). We use `dp(r, c)` to represent the number of different paths from the coordinate `(r, c)` to the bottom-right corner. The transition relation is `dp(r, c) = dp(r, c + 1) + dp(r + 1, c)`, and the termination condition is `dp(m - 1, n - 1) = 0` (if `(r, c)` is out of the grid boundaries, then `dp(r, c) = 0`). The answer required by the problem is `dp(0, 0)`.

In fact, applying combinatorial ideas provides a more direct solution. Regardless of the path, there must be a total of `m + n - 2` steps from the top-left to the bottom-right corner, and among these steps, there are exactly `n - 1` steps to the right and `m - 1` steps downwards. Therefore, the final answer is the combination of `m + n - 2` choose `m - 1`. In Python, we only need to `return math.comb(m + n - 2, m - 1)`.

[back](/math-and-algo)
