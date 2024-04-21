---
layout: default
title: "Codeforces 935 E Binary Search on Aribitrary Array"
permalink: /Codeforces935E-binary-search
---

# Binary Search on Aribitrary Array

[Codeforces 935 E Binary Search](https://codeforces.com/problemset/problem/1945/E): Binary search is a widely used and efficient search algorithm, predicated on the assumption that the given data is sorted (either in ascending or descending order). This problem presents an intriguing conclusion: for any permutation of numbers from `1` to `n`, allowing the swapping of two elements' positions, regardless of whether the permutation is sorted or not, we can still use binary search to find the target element. The question then becomes: how do we select the elements to swap?

First, let's introduce the binary search used in the problem. Suppose we have a permutation `p` and we want to find element `x`. Let `l = 1` and `r = n + 1` (assuming the original permutation is 1-indexed). We execute the following loop:
- If `r - l == 1`, end the loop and return `p[l]`.
- Let `m = (l + r) // 2`.
- If `p[m] <= x`, let `l = m`; otherwise, let `r = m`.

An intuitive idea is to perform binary search on the original permutation. Suppose the last obtained element is `y`, we swap `x` and `y` (if `y == x`, then no swapping is needed and we have found `x`). This algorithm seems plausible, but proving its correctness rigorously is not straightforward. Let's consider the sequence formed by each element `p[m]` obtained during the search process. There are several scenarios to consider:
- If `x` does not appear in this sequence: swapping `x` and `y` does not seem to affect the entire search process, so we can eventually find `x`. However, we need to consider two cases to prove this rigorously.
  - If `y` appears only once, swapping `x` and `y` will not affect the search process before the final step, so after the swap, we can obtain `x`.
  - If `y` appears multiple times, we need to replace all occurrences of `y` with `x`. Will this affect our search process? The answer is no. According to the definition of the problem, if we can prove `y <= x`, replacing `y` with `x` will not affect the search process. And precisely because `y` appears multiple times, there must be `y <= x`; otherwise, `y` would become the right endpoint `r` and would not appear more than once in the sequence of `p[m]`.
- If `x` appears in this sequence, we also need to prove `y <= x`. Here, we can prove a more general conclusion: if at some point during the search, `p[l] <= x`, then the final output element must be `<= x`. We can prove this by mathematical induction on the value of `r - l`. If `r - l == 1`, the conclusion holds. If `r - l > 1`, consider the next `p[m]`. If `p[m] <= x`, then let `l = m`, and by the induction hypothesis, the conclusion holds; if `p[m] > x`, then let `r = m`, and again, by the induction hypothesis, the conclusion holds.

In this way, we have completed the rigorous proof of the correctness of this algorithm. In fact, through the proof, we find that this algorithm is not limited to permutations from `1` to `n`; it holds true for any sequence.

[back](/math-and-algo)
