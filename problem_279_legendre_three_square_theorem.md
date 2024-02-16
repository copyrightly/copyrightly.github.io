---
layout: default
title: "Problem 279 Number Theory"
permalink: /problem279-Legendre-three-square-theorem
---

# Lengedre's three-square theorem 

[Problem 279](https://leetcode.com/problems/perfect-squares/): Given an natural number, what is the minimum number of perfect squares that sum to it?

In our previous article ([Problem 633](https://copyrightly.github.io/problem899-algebra)), we discussed the necessary and sufficient conditions for a natural number to be expressed as the sum of two squares. A natural question arises: for any given natural number, what is the minimum number of perfect squares that sum to it? This is precisely the problem addressed by LeetCode 279. One solution is to utilize BFS (Breadth-First Search), systematically listing perfect squares, the sums of two perfect squares, the sums of three perfect squares, and so on until reaching the given number.

Here, we introduce the number theory approach to solving this problem. In addition to the [Sum of two squres theorem](https://en.wikipedia.org/wiki/Sum_of_two_squares_theorem) mentioned in the [previous article](https://copyrightly.github.io/problem899-algebra), we also need two additional conclusions: Lagrange's Four-Square Theorem and Legendre's Three-Square Theorem. Lagrange's theorem states that any natural number can be expressed as the sum of no more than four squares. While this provides an upper limit on the answer, it does not specify which numbers can only be expressed as the sum of four squares and which can be expressed with fewer squares. This leads to Legendre's theorem, which provides a necessary and sufficient condition for a number to be expressible as the sum of four squares (no fewer), namely, the number must be of the form `4^k(8m+7)`, where `k` and `m` are natural numbers.

With this, we can provide a complete answer to the problem posed at the beginning: for a given natural number `n`,

- If `n` is of the form `4^k(8m+7)`, the answer is 4.
- Otherwise,
  - If `n` itself is a perfect square, the answer is 1.
  - If `n` can be expressed as the sum of two square numbers (determined by the Sum of two squres theorem, as discussed in the [previous article LeetCode 633](https://copyrightly.github.io/problem899-algebra)), the answer is 2.
  - Otherwise, the answer is 3.

The following is Python code implementing this algorithm:
```
def numSquares(n: int) -> int:

  # using binary search to check if a number is a perfect square
    def isSquare(x):
        l, r = 1, x
        while l <= r:
            m = l + (r - l) // 2
            curr = m * m
            if curr == x:
                return True
            elif curr > x:
                r = m - 1
            else:
                l = m + 1
        return False

  # using Sum of Squares Theorem to check is x can be written as a sum of two squares
    def isTwoSquareSum(x):
        for i in range(2, x):
            if i * i > x:
                break
            if x % i == 0:
                cnt = 0
                while x % i == 0:
                    cnt += 1
                    x //= i
                if i % 4 == 3 and cnt % 2 == 1:
                    return False
        return x % 4 != 3

  # using Legendre’s Theorem to check if x can only be written as a sum of four squares
    def isFourSquareSum(x):
        while x % 4 == 0:
            x //= 4
        return x % 8 == 7

  # by Lagrange’s Theorem, the answer is at most 4
    if isSquare(n):
        return 1
    if isFourSquareSum(n):
        return 4
    if isTwoSquareSum(n):
        return 2
    return 3
```

The complexity mainly lies in determining whether a number can be expressed as the sum of two squares using the Sum of two squres theorem, with a complexity of `O(sqrt(n)log(n))` (as discussed in the [previous article LeetCode 633](https://copyrightly.github.io/problem899-algebra)). The complexity of Legendre's theorem (`isFourSquareSum` function) is only `O(log(n))`, so the overall complexity of the algorithm is O(sqrt(n)log(n)).
