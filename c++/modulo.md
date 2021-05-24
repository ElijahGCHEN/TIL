## Why some questions require the result to modulo a big number (e.g. 998244353)

1. This big number is usually a prime number. Modolo a prime number can reduce the probability of hash collision. 
2. The maximal int value is 2<sup>31</sup>-1, i.e. 2147483647, so the sum of two modoloed numbers doesn't exceed INT_MAX.
3. The maximal long value is 2<sup>63</sup>-1. Since 998244353 < INT_MAX, and INT_MAX<sup>2</sup> < LONG_MAX, so the multiplication of two modoled numbers doesn't exceed LONG_MAX.

## Why using a prime as modulo can reduce the probability of hash collision
Prime numbers are chosen to best distribute data among hash buckets. If the distribution of inputs is random and evenly spread, then the choice of the hash code/modulus does not matter. It only has an impact when there is a certain pattern to the inputs.

This is often the case when dealing with memory locations. For example, all 32-bit integers are aligned to addresses divisible by 4. 
