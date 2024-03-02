---
layout: default
title: "Problem 633 Number Theory"
permalink: /problem633-number-theory
---

# Sum of Two Squares Theorem 

[Problem 633](https://leetcode.com/problems/sum-of-square-numbers/): Determine if a Natural Number Can Be Written as the Sum of Two Squares

To determine if a natural number can be expressed as the sum of two squares, one direct algorithm is to enumerate all natural numbers `k` less than or equal to `sqrt(n)`, and then check if `n - k^2` is a perfect square. In fact, this problem is a classic problem in number theory. [Sum of two squares theorem](https://en.wikipedia.org/wiki/Sum_of_two_squares_theorem) gives a sufficient and necessary condition: a natural number can be written as the sum of two squares if and only if the number is of the form `4k+3`, where the prime factors appear an even number of times. Knowing this theorem, we can decompose `n` and examine the exponent of each prime factor of the form `4k+3`.

However, in terms of algorithm implementation, a natural question arises: how do we decompose `n` into prime factors since there is no direct algorithm to list all prime numbers less than or equal to `sqrt(n)`? In fact, we do not need to list prime factors; we only need to iterate through all natural numbers less than or equal to `sqrt(n)`. For any natural number `m` less than or equal to `sqrt(n)`, if `m` is a factor of `n`, we record the number of times `m` appears in the decomposition. Consider the following two cases:

- If `m` is congruent to 1 modulo 4, it is not necessary to consider it because...
  - If `m` is a prime number, according to the theorem, we do not need to consider the number of times `m` appears.
  - If `m` is a composite number, then the prime factors of the form `4k+3` contained in `m` must appear an even number of times; otherwise, it contradicts with `m` being congruent to 1 modulo 4.
- If `m` is congruent to 3 modulo 4, then if `m` appears an even number of times, `n` can be expressed as the sum of two squares; otherwise, it cannot. You may ask here that we do not know if `m` is a prime number, so we cannot use Sum of Two Squares theorem. In fact, we can consider the prime factors of `m` because `m` is congruent to 3 modulo 4, so `m` must contain a prime factor of the form `4k+3` and appear an odd number of times. In this case, if `m` appears an even number of times, the prime factor of the form `4k+3` also appears an even number of times; if `m` appears an odd number of times, such a factor also appears an odd number of times. Therefore, we can use Sum of Two Squares theorem for judgment.

Based on the discussion above, our algorithm implementation is quite simple. Here is an example of Python code:
```python
def judgeSquareSum(c: int) -> bool:
    for i in range(2, c + 1):
        # iterate until sqrt(c)
        if i * i > c:
            break
        # find a factor of c
        if c % i == 0:
            cnt = 0
            # count exponent of the factor
            while c % i == 0:
                cnt += 1
                c //= i
            # if the factor is in the form of 4k + 3,
            # check if its exponent is odd:
            # if yes, c can't be written as a sum of two squares
            if i % 4 == 3 and cnt % 2 == 1:
                return False
    return c % 4 != 3
```

[back](/math-and-algo)
Since we iterate through all natural numbers less than or equal to `sqrt(n)` and calculate the frequency of each factor of `n`, this part contributes a complexity of `O(log(n))`. Therefore, the overall complexity of the algorithm is `O(sqrt(n)log(n))`.

An interesting question is: for any given natural number, what is the minimum number of squares it can be written as the sum of? We will provide the answer in the next article.
