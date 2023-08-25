---
title: "CTFLearn Impossible Equation"
date: 2023-08-18T09:44:48-07:00
categories: ["CTF", "blog"]
math: true
---

A problem to exploit the input guard using math.

- [Problem link](https://ctflearn.com/challenge/1234)

The problem statement is:
```
$ nc rivit.dev 10011
X * 212103456793011 = 183057226632645
X = ?
```

It seems we want to compute the number of x:

$$ x = \frac{183057226632645}{212103456793011}$$

If you compute it, it should be something like `0.8630563094088945586`. However, it won't give you the flag because x must be an integer. That said we need to overflow.

$$ 212103456793011x = 183057226632645 \mod{2^{64}} $$

or,

$$ 212103456793011x - 2^{64}q = 183057226632645$$

The solution to x is:

$$ x = \frac{183057226632645 + 2^{64} q}{212103456793011} $$

and it's integer solutions:

$$ x = 18446744073709551616 n + 9585860797856392871, n \in \mathbb{Z} $$

which has the smallest solution:

$$ x = 9585860797856392871 $$

## Takeaways

Even though it is categorized as programming, I'm sure it's more like math
problem. The last time I used modulo arithmetic in practice was analysis of RSA and that
experience helped me to solve this a lot.
