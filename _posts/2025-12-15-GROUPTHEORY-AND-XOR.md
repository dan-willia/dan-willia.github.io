---
layout: post
title: "Group Theory and XOR"
date: 2025-12-14
katex: true
categories: math
---

In my year two coursework at the Open University, I am taking a module called Pure Mathematics, which spends about a month introducing group theory (a topic which I still think has a comic ring to it). I've been especially looking out for connections to logic and computer science, which I found in the propositional calculus. 

Group theory studies groups. A group is comprised of two things: $(G, \circ)$. 

The first, $G$, is a set of of elements $\{g_1, g_2, ...\}$.

The second is an operation, $\circ$, which combines two of the elements in $G$ to produce an element in $G$.

There are four axioms that a group must satisfy.

**Group Axioms**

1. _Closure._ 
	
	Whenever you combine two elements, the third must be in $G$.

1. _Associativity._
	You can group elements with parentheses however you want, and the result doesn't change. For example, $$(g_1 \circ g_2) \circ g_3 = g_1 \circ (g_2 \circ g_3) = g_1 \circ g_2 \circ g_3.$$

1. _Identity._

	There is an element, $e$, that, when combined with any other element (including itself), yields the other. In other words,
$$ g \circ e = g = e \circ g$$

4. _Inverse._

	Each element in $G$ can be combined with an element in $G$ such that together they produce $e$.
$$g \circ h = e = h \circ g.$$

**Example of a group**

Let $G = \{0,1\}$.

Let $\circ$ be $+_2$, i.e. addition modulo 2.

There are four ways of combining the elements of $G$:

$$
\begin{align*}
0 +_2 0 &= 0 \\
0 +_2 1 &= 1 \\
1 +_2 0 &= 1 \\
1 +_2 1 &= 0
\end{align*}
$$

 which is more conveniently shown as a table (called a _Cayley table_):

|$+_2$ | 0 | 1 |
| --- | --- | --- |
|**0** | 0 | 1 |
| **1** | 1 | 0 |

We can see that the four axioms hold: closure, associativity, identity, and inverse. (See if you can determine what's the identity element and what are the inverses of 0 and 1.)

---

Cayley tables bear a resemblance to truth tables, which I learned ages ago in symbolic logic. This led me to wonder, are operations in the propositional calculus groups?

Let's check.

**Logical Or ($\vee$)**

Checking the group $(\{T,F\}, \vee\}).$

Its Cayley table:

|$\vee$ | T | F |
| --- | --- | --- |
|**T** | T | T |
| **F** | T | F |

We can see that it fails the inverse property: $T$ has no inverse. (The identity element is $F$, and there is no way to get from $T$ to $F$.)

**Logical And ($\wedge$)**

The same result as logical or, but this time $F$ has no inverse. (I leave it to the reader to construct the Cayley table.)

**XOR ($\otimes$)**

|$\otimes$ | T | F |
| --- | --- | --- |
|**T** | F | T |
| **F** | T | F |

Now we have an operation which _is_ a group. We can see that the identity element is $F$, and each element is self-inverse: $T \otimes T = F$ and $F \otimes F = F$.

There is a theorem in group theory that all groups of order 2 are isomorphic, and so we can pretty easily come up with a mapping between $(\{T,F\},\otimes)$ and $(\{0,1\}, +_2):$

$$ 
\begin{align*}
(\{T,F\},\otimes) &\longrightarrow  (\{0,1\}, +_2) \\
		T &\longmapsto 1 \\
		F &\longmapsto 0.
\end{align*}
$$

So, the two groups are in some sense "the same": they have identical structural properties and we can even rename the elements of one in terms of the other.

**What does it mean?**

It turns out that XOR forming a group has some interesting consequences in computing. Notice that $\vee$ and $\wedge$ lack the inverse property. This means that there is no way to "undo" the operation. 

**Example**

Let $a,b,c$ be arbitrary bitstrings such that $$a \vee b = c.$$

Say we lose $a$ but still have $b$ and $c$. There is no way to recover $a$. The information has been lost in the $\vee$ operation.

Now say $a,b,c$ are such that $$a \otimes b = c.$$ Now if we lose $a$, we can recover it because of the inverse property. (Recall that elements in the XOR group are self-inverse.)

$$
\begin{align*}
a \otimes b &= c \\
a \otimes b \otimes b^{-1} &= c \otimes b^{-1} \\
a &= c \otimes b
\end{align*}
$$

So, the information can be recovered with XORing. 

This property is applied extensively in error correction codes and cryptography. 

Check out these other resources for more info.

- <https://www.themathcitadel.com/articles/group-theory-xor.html>
- <https://en.wikipedia.org/wiki/One-time_pad>
- <https://en.wikipedia.org/wiki/Parity_bit>
