---
layout: default
title: "Problem 650 prime factorization"
permalink: /problem650-prime-factorization
---

# Prime Factorization

[Problem 650](https://leetcode.com/problems/2-keys-keyboard/): Imagine a screen displaying a single letter "A". At each step, you have two possible operations:
- **Copy**: Copy all the letters currently on the screen (you can't just copy a portion).
- **Paste**: Paste the previously copied letters.

Given a natural number `n`, what is the minimum number of operations required to have exactly `n` letter "A"s on the screen?

## Intuitive Approach
A straightforward way to approach this problem is to consider each step as a choice between copying or pasting, and then moving on to the next state. We can compare the two operations at each step and choose the optimal one. This can be implemented using dynamic programming.

For each step, we can use two variables to describe the state: the current number of letters on the screen and the number of letters copied. We then examine the results of both copy and paste operations and choose the best option. The specific code implementation is shown below.
```python
def minSteps(self, n: int) -> int:
    @lru_cache(None)
    def dp(curr, copied):
        if curr == n:
            return 0
        res = float('inf')
        # paste at this step
        if copied > 0 and curr + copied <= n:
            res = 1 + dp(curr + copied, copied)
        # copy at this step
        if copied < curr and curr * 2 <= n:
            res = min(res, 1 + dp(curr, curr))
        return res
    return dp(1, 0)
```

## Alternative Approach
Let's consider the problem from a different angle. Suppose there are currently `x` letters on the screen. If you perform a copy operation (denoted as C), the number of letters remains `x`. If you then perform a paste operation (denoted as CP, a total of two operations), the number of letters becomes `2x`. Another paste operation (denoted as CPP, a total of three operations) results in 
`3x` letters. Following this pattern, after `g` operations, you would have `gx` letters, denoted as CPP…P (with `g−1` P's).

This leads to the observation that the sequence of operations can be summarized as several repetitions of this CPP…P pattern. Suppose there are `k` such repetitions, each of length `g_i`. Based on this discussion, the total number of operations would be `g_1 + g_2 + ⋯ + g_k`​, resulting in `g_1 × g_2 × ⋯ × g_k`​ letters.

The problem now is to find the minimum value of `g_1 + g_2 + ⋯ + g_k`​ such that `g_1 × g_2 × ⋯ × g_k = n`.

This might seem like an optimization or integer programming problem, but we can solve it using a simple inequality. For two natural numbers `a` and `b` greater than 1, we have `ab ≥ a + b` (since `(a−1)(b−1)>0`, it follows that `a + b < ab + 1`). This tells us that by decomposing a product into its factors, the sum either decreases or remains the same.

For any natural number, repeatedly decomposing it into factors essentially means factoring it into its prime components. Therefore, the solution to this problem is the sum of all the prime factors of `n`. The code implementation for this approach is shown below.
```python
def minSteps(self, n: int) -> int:
    ans = 0
    d = 2
    while d <= n:
        while n % d == 0:
            ans += d
            n //= d
        d += 1
    return ans
```

[back](/math-and-algo)
