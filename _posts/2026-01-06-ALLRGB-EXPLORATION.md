---
layout: post
title: "All RGB Exploration"
date: 2026-01-06
math: true
categories: math, recreational_coding
---

It's sometimes hard to tell how much you've learned in a year. One way is to look back at problems you once struggled to solve that now seem easy. Here's an example of one of those problems.

The problem is to display every RGB color in a 2-dimensional space. 

Here is a sketch of one way to solve the problem:

```python
def rgb_to_plane():
  row = -1
  col = 0
  plane = []
  for r in range(256):
    for g in range(256):
      for b in range(256):
        if col == 0:
          plane.append([])
          row += 1
        plane[row].append((r,g,b))
        col = (col + 1) % 4096
  return plane
```

The basic idea is that there are `256*256*256 = 16777216` rgb colors, and since `16777216` is a square number, all the colors can fit exactly into a `4096x4096` matrix.

This algorithm gives us a one-to-one mapping from RGB colors to a square matrix, but it doesn't tell us where, given a color, where it is in the matrix; nor does it tell us, given a position in the matrix, what RGB color it is. Let's find those functions. 

Let's tackle the `rgb -> ij` direction first.

**Short detour**

We have already verified that the RGB colors can be mapped to a square matrix -- but why is it so? It turns out it is because 256 is a square number. In fact we can prove the general result that a bijection from a set `s = {0,...,b-1}^3` to a set `v = {0,...,a-1}^2` is possible if and only if `b` is a square number. 

*Proof*.

First we observe that a bijection is possible iff the sets `s` and `v` have the same cardinality. Note that `|s|=b^3` and `|v|=a^2`. 

Now, let `b` be a square number. We need to show that `b^3` is a square number. We have `b = c^2`, and thus `b^3 = (c^2)^3 = (c^3)^2`. Thus `b^3` is a square number and therefore `a` exists, and thus the bijection is possible. 

The other direction of the iff:

Given that the bijection between `s` and `v` exists, we need to show that `b` is a square number.

By assumption, `b^3 = a^2`, so `b = a^(2/3) = (a^(1/3))^2`. Thus `b` is a square number.

*End of proof.*

Incidentally, we've also also shown that `a` must be a cubic number, and if we know `b`, then `a = b^(3/2)`. 

With these results, let's tackle a smaller but exactly analogous problem to the original `rgb -> ij` mapping. Say we have a color scheme represented by three channels, call them `x,y,z`, that can each take on four values. We want to find a bijection from `{0,1,2,3}^3` to `{0,...,a-1}^2` for some `a`. Our result above says that `a = 4^(3/2) = 8`. (Check: `4^3 = 64; sqrt(64) = 8`). 

Using the scheme of the `rgb_to_plane` algorithm, we obtain the following mapping:
```
(0,0,0) -> (0,0)
(0,0,1) -> (0,1)
(0,0,2) -> (0,2)
(0,0,3) -> (0,3)
(0,1,0) -> (0,4)
(0,1,1) -> (0,5)
(0,1,2) -> (0,6)
(0,1,3) -> (0,7)
(0,2,0) -> (1,0)
(0,2,1) -> (1,1)
etc.
``` 

We notice that the tuples on the left can be thought of as encoding three-digit base 4 numbers, and the tuples on the right as encoding two-digit base 8 numbers.

For example, we can write `(0,1,3)` as $$(013)_4$$, which in base 10 is `4^2*0 + 4^1*1 + 4^0*3 = 7`; and `(0,7)` as $$(07)_8$$, which in base 10 is likewise `8^1*0 + 8^0*7 = 7`. 

So we can think of this problem as converting three-digit base 4 numbers to two-digit base 8 numbers. 

There is undoubtedly a clever way to do the conversion efficiently, but it doesn't reveal itself to me immediately, so I'm going to take the long way and convert the left column to base 10, then convert that to base 8.

We've seen how to convert to base 10 already. But how to convert from base 10 to base 8?

We know that the numbers to be converted will all fit into a two-digit base 8 number. Thus we need to find how many times 8 divides the base 10 number, which can be done using the floor operation. The remainder of the base 10 number divided by 8 will be the "ones" digit.

So, for our smaller `xyz -> ij` problem, the function is:

```python
def xyz_to_ij(x,y,z):
  base10 = 4**2 * x + 4 * y + z
  i = base10 // 8
  j = base10 % 8
  return i, j
```
(Check: `xyz_to_ij(0,1,3)` outputs `0,7`, as expected.)

So, we now replace bases with the original RGB bases, which gives the following mapping for the original problem:

```python
def rgb_to_ij(r,g,b):
  base10 = 256**2 * r + 256 * g + b
  i = base10 // 4096
  j = base10 % 4096
  return i, j
```
So, for example, the RGB color `(200,103,240)` will be mapped to position `(3206,2032)`. 

--- 

Now let's tackle the other direction. Given a position `i,j` in the square matrix, what RGB color is it?

Again, let's look at the small problem first. 

We'll follow the same strategy, converting to base 10 and then converting to base 4.

Take the position `(4,6)` in the 8x8 square matrix and regard it as number $$(46)_8$$. 

Its base 10 representation is 8*4 + 6 = 38. 

We want to convert this to base 4. The rightmost digit, `z`, is given by 38 mod 4 = 2. 

Now `floor(38/4) = 9` tells us how many times 4 divides 38. But this won't always yield a valid base 4 number, since we have two digits (`x` and `y`) to work with. So we need to apply the same conversion once more: `9 mod 4 = 1` and `floor(9/4) = 2`. Thus `x = 2` and `y = 1`, and the `xyz` color is `(2,1,2)`. 

Check: `4^2*2 + 4*1 + 2 = 32 + 4 + 2 = 38`, as expected. 

Replacing the bases, we can write the following function:

```python
def ij_to_rgb(i,j):
  base10 = 4096*i + j
  tmp = base10 // 256
  b = base10 % 256
  g = tmp % 256
  r = tmp // 256
  return r, g, b
```

Check: `ij_to_rgb(3206,2032)` returns `(200,103,240)`, as expected.

---

Finally, here's the image that's created using the `rgb_to_plane` scheme and the `PIL` library:

![allrgb](/assets/allrgb.png)

I've inserted black lines at `row = 2032` and `col = 3206` to make it easier to identify the position `(3206,2032)` in the image. And in the zoomed-in screenshot below, I've removed the lines and just blacked-out the color `(200,103,240)` (a shade of purple), which occurs at the predicted position:

![allrgb_pixel](/assets/allrgb_pixel.png)

---

I discovered this problem in Part II of _Programming Principles and Practice_ by Stroustrup. It fascinated me and led me to the website [allrgb.com](https://allrgb.com), where you can submit your own solution to the all rgb problem.