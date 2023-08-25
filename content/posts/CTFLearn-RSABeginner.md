---
title: "CTFLearn RSABeginner"
date: 2023-08-25T10:40:22-07:00
categories: ["CTF", "blog"]
math: true
---

Solve RSA decoding problems when `e` is small enough.

- [Problem link | RSA Beginner](https://ctflearn.com/challenge/119)


## Compute factor $q$ and $p$

In order to decode RSA, we need to compute $p$ and $q$ as factor of $n$.
Generally, it requires a lot of computating but we have a good resource: stored
factor list.

Pip package `factordb-python` is useful:

```
import factordb.factordb import FactorDB

f = FactorDB(n)
f.connect()
factors = f.get_factor_list()
```

## Compute $d$

$d$ is defined using $\phi$:

$$ ed \equiv 1 \mod{\phi} \longleftrightarrow d = (1/e) \mod{\phi} $$

while $\phi = (p-1)(q-1)$.

In order to compute this modular multiplicative inverse, pip package `pycrypto` is useful:

```
import Crypto.Util.number import inverse

d = inverse(e, phi)
```

## Compute $m$

The plain message $m$ is defined as:

$$ m = c^d \mod{n} $$

where we can:

```
pow(c,d,n)
```

The third, optional, parameter is the modulus.
