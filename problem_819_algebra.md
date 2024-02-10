---
layout: default
title: "Problem 899 algebra"
permalink: /problem899-algebra
---

# String and Algebra

[Problem 899](https://leetcode.com/problems/orderly-queue/): Given a string `s` and an integer `k <= len(s)`, we can move any one of the first `k` letters of `s` to the end of `s`. If we can perform this operation any number of times, return the smallest (in lexicographic order) string that can be obtained.

It was precisely this problem that inspired me to start summarizing this type of problem. I spent an entire afternoon pondering over this problem, but ultimately couldn't solve it. After reading the official solution, I suddenly realized that the essence of this problem is a fundamental theorem of higher algebra: [any permutation can be expressed as a composition of several transpositions](https://www.sfu.ca/~mdevos/notes/geom-sym/14_transpositions.pdf). I remember this theorem from my textbooks, but I never thought it would be hidden in an algorithmic problem related to strings. If you realize this theorem, then this problem can be easily solved; otherwise, you may find yourself completely at a loss.

The problem states that we can move any one of the first `k` letters in `s`, considering two cases: `k = 1` and `k > 1`.

- When `k = 1`, we simply rotate `s`: continuously move the first letter to the end. We can list all the strings obtained in this way and then find the smallest one. The time complexity is O(n^2).

- The case when `k > 1` is more complex. We can choose any one of the first `k` letters, which seems to result in a large number of possible strings. So we wondered if all permutations of `s` could be obtained? Here the theorem mentioned earlier comes into play. To show that we can obtain all permutations of `s`, we only need to prove that we can achieve any transposition. This is not difficult to prove. Assuming `s = s1 x s2 y s3`, where `s1`, `s2`, and `s3` are strings with lengths greater than or equal to 0, and `x` and `y` are two letters. We want to prove that we can swap the positions of `x` and `y` through the operations mentioned in the problem statement:

```
s1 x s2 y s3 (move s1 to the end)
x s2 y s3 s1 (move s2 to the end, note that we need k > 1 here)
x y s3 s1 s2 (move x to the end)
y s3 s1 s2 x (move s3 s1 to the end, note that we need k > 1 here)
y s2 x s3 s1 (move y s2 x s3 to the end)
s1 y s2 x s3
```

This way we have successfully swapped the positions of `x` and `y`. Then, according to the theorem, we can achieve any permutation of `s`, so the answer is to sort all the letters in `s`. In Python, we only need to return `sorted(s)`, and the time complexity is O(n*log(n)).

In this way, a problem marked as hard on LeetCode can be perfectly solved by a theorem of higher algebra.
