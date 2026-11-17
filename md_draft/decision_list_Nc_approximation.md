# Zero-error decision-list approximation within $N^c$

## 1. Main result and its scope

For every fixed dimension constant $C>0$ and arbitrarily small fixed
approximation exponent $c>0$, there is an explicit constant
$\tau=\tau(C,c)>0$ and a deterministic algorithm with the following guarantee:

$$
d\le C\log_2N,\qquad K^*(S)\le N^\tau
\quad\Longrightarrow\quad
\operatorname{err}_S(L)=0,\qquad |L|\le N^cK^*(S).
$$

On this promised class, its scan/enumeration work, after parameter setup, is
bounded by

$$
\boxed{T_{\mathrm{search}}\le
A\,N^{1+c}(C\log_2N+1),}
$$

where $A$ is an absolute constant, independent of $C,c,N,d$. Section 6 gives
the complete running time, including exact parameter setup, rational encoding
lengths, and an explicit word-size factor for bit-cost computation. No
running-time bound hides dependence on $C$ or $c$ in its constant.

The algorithm does not receive the optimum or an optimal list. It succeeds
with probability $1$, and its guarantee permits arbitrary tie-breaking among
eligible rules. The constants chosen below actually give the stronger
absolute length bound $|L|\le N^{c/2}$ whenever $K^*(S)\ge1$.

**The budget restriction is essential to the statement.** What is proved is

$$
\forall C>0\ \forall c>0\ \exists\tau(C,c)>0.
$$

This report does not establish every $N^c$ ratio on a single fixed range
$K^*(S)\le N^{\tau_0}$ with $\tau_0>0$ independent of $c$, nor an $N^c$
ratio for unrestricted $K^*(S)$. In particular, it is not a polylogarithmic
approximation theorem.

## 2. Model and notation

The explicitly represented input is an indexed labeled Boolean sample

$$
S=((x_i,y_i))_{i=1}^{N},\qquad
x_i\in\{0,1\}^d,\quad y_i\in\{0,1\}.
$$

Assume $N\ge2$ and $d\le C\log_2N$. Samples with $N=1$ are handled by
returning their label as the default. Repeated feature vectors are allowed,
provided their labels agree. Contradictory duplicates are outside the
zero-error promise. No complete-truth-table assumption is made.

A condition $Q\in\{0,1,*\}^d$ is a conjunction of literals. Its width is
the number of specified coordinates. A decision list

$$
L=((Q_1\to b_1),\ldots,(Q_\ell\to b_\ell);b_{\mathrm{def}})
$$

uses the first matching rule and otherwise its default. Both rule labels and
both defaults are allowed. Length counts nondefault rules, not literals:
$|L|=\ell$. Define

$$
\operatorname{err}_S(L)=|\{i\in[N]:L(x_i)\ne y_i\}|,
\qquad
K^*(S)=\min\{|L|:\operatorname{err}_S(L)=0\}.
$$

Write $k=K^*(S)$ only in the analysis. It is unknown to the algorithm. When
$k=0$, all observed labels agree, and the returned list has zero rules.

All logarithms with subscript $2$ are binary; $\ln$ denotes the natural
logarithm. $C$ and $c$ are fixed constants, rather than input-dependent
exponents. Section 6 implements the algorithm for positive rational constants
and counts their setup cost. For arbitrary positive real targets, one can
choose explicit rational $C'\ge C$ and $0<c'\le c$ and apply the theorem
with $C',c'$. Section 6.6 gives a choice and a bound expressed directly in
the target values $C,c$.

**Complexity convention:** every $O(\cdot)$ used for time or space in this
report has an absolute implicit constant, independent of $C,c,N,d$ and the
parameter encodings. In inequalities, $A$ denotes an absolute constant that
can be enlarged uniformly to cover all the displayed bounds.

## 3. Explicit dependence on $C$ and $c$

Set

$$
a=\min\left\{\frac cC,1\right\},\qquad
\ell_0=\left\lceil\log_2\frac8a\right\rceil,\qquad
\beta=\frac{a}{8\ell_0},
$$

and

$$
\boxed{\displaystyle
\tau(C,c)=\min\left\{\frac14,
\frac{C\beta^2}{128\ln2}\right\}.}
$$

These constants are positive, and $\beta\le1/24$. In the main regime
$0<c\le C$, the second term can be written explicitly as

$$
\frac{c^2}{8192\,C\ln2\,
\left\lceil\log_2(8C/c)\right\rceil^2}.
$$

For fixed $C$ and $c\downarrow0$,

$$
\tau(C,c)=\Theta\left(
\frac{c^2}{C(\log(C/c))^2}\right).
$$

The displayed constants are conservative and are not claimed to be optimal.
The approximation exponent $c$ affects both the searched rule universe and
the permitted exponent $\tau$ of the unknown optimum.

## 4. Algorithm

### 4.1 Exact check for zero or one rule

First check whether all observed labels agree. If so, return their label as
the default and no rules.

Next, test whether a zero-error one-rule list exists. For each proposed default
$b\in\{0,1\}$, let

$$
T_b=\{x_i:y_i\ne b\}.
$$

In this mixed-label case $T_b$ is nonempty. Construct the smallest subcube
$H_b$ containing $T_b$: specify a coordinate precisely when every point of
$T_b$ agrees on it. If no point labeled $b$ belongs to $H_b$, return

$$
(H_b\to1-b);\ b.
$$

This check takes $O(N(d+1))$ time. It is exact: any condition covering $T_b$
contains its subcube hull $H_b$, so a valid one-rule condition exists if and
only if $H_b$ itself is valid. Thus, if neither default passes, then $k\ge2$.

### 4.2 Restricted safe-rule peeling

Compute

$$
D=\lfloor C\log_2N\rfloor,\qquad
h=\min\{d,\lfloor\beta D\rfloor\}.
$$

The search universe consists of all conjunctions of width at most $h$. Its
size is

$$
M_h(d)=\sum_{j=0}^{h}2^j\binom dj.
$$

Maintain the remaining sample indices $U\subseteq[N]$. A condition is safe
on $U$ if it matches a nonempty subset of $U$ and all those labels agree.
Safety is checked on the current remainder; a rule can match oppositely
labeled points already handled by earlier rules.

```text
Input: S and fixed constants C,c

If S is monochromatic:
    return an empty list with its common label as default

For each default b in {0,1}:
    form the subcube hull H_b of the examples labeled 1-b
    if H_b contains no example labeled b:
        return the one-rule list H_b -> 1-b, with default b

Compute beta, D and h as above
U <- all sample indices
L <- empty list
restricted <- true

While U contains both labels:
    if restricted:
        enumerate conditions of width at most h, in increasing width
        find a safe condition Q of minimum width
        if none exists:
            restricted <- false
    if not restricted:
        enumerate all conditions in {0,1,*}^d, in increasing width
        find a safe condition Q of minimum width in that full universe
    let b be the common label of the remaining points matching Q
    append Q -> b to L
    remove all matching indices from U

Return L with the remaining label as default
```

Once the permanent switch occurs, subsequent iterations use the full universe.
The switch never occurs under $k\le N^\tau$. It is included so that the
algorithm also returns a zero-error list on every other consistent sample,
with the more general polynomial running time stated in Section 6.

Any ordering of equal-width eligible rules is allowed. Conditions can be
generated one at a time; no table of all their sample traces is necessary.

## 5. Proof of the approximation guarantee

### 5.1 Structural size–width lemma

We use the published term-decision-list size–width relation in
Beyersdorff–Blinkhorn–Mahajan–Peitl (2023), Lemmas 6.4–6.5. Its single-output
specialization gives the following conservative explicit form:

> A Boolean function computed by a decision list with $k$ nondefault rules
> has an equivalent decision list of width at most
> $$
> W=\min\left\{d,
> 2\left\lceil\sqrt{2d\ln(k+2)}\right\rceil\right\}.
> $$

The $k+2$ absorbs conventions about counting the default and makes the
inequalities strict. Equivalence holds on the entire Boolean cube. The
converted list may be long; the algorithm does not construct it.

For clarity, the constant follows by setting the width threshold and branching
budget both to $t=\lceil\sqrt{2d\ln(k+2)}\rceil$ in their narrowing lemma.
When $t<d$, that lemma uses $A=(1-t/(2d))^{-1}$, and

$$
\ln(A^t)\ge\frac{t^2}{2d}\ge\ln(k+2).
$$

Hence its strict bound on the number of terms wider than $t$ holds, yielding
width $2t$. When $2t\ge d$, the original list already has width at most $d$.

### 5.2 Safe-rule peeling preserves access to a bounded-width comparator

Suppose some width-$h$ list is consistent with $S$. Restrict attention to any
current mixed remainder $U$. Choose its first nondefault rule matching any
point of $U$. Earlier rules match none of $U$, so every remaining point
matching this rule receives its stated label. It is therefore safe on $U$.

If no nondefault rule matches $U$, the comparator outputs its default on all
of $U$, contradicting the mixed remainder. Thus a safe condition of width
at most $h$ always exists.

Arbitrary previous safe choices do not invalidate this argument: the same
fixed comparator remains consistent with every subset of the original sample.
Consequently the restricted search cannot fail when a width-$h$ comparator
exists.

A selected condition is never selected again. At its first use, all remaining
points matching it are removed; its trace on every later remainder is empty.
The restricted algorithm therefore appends at most $M_h(d)$ rules. Each
removed point is labeled correctly by its first matching output rule, and
the final default labels the remaining points correctly. Hence its error is
exactly zero.

### 5.3 The searched universe contains at most $N^{c/2}$ conditions

Let

$$
H_2(u)=-u\log_2u-(1-u)\log_2(1-u).
$$

The standard binomial-ball bound, valid for $0<u\le1/2$, is

$$
\sum_{j=0}^{\lfloor uD\rfloor}\binom Dj\le2^{DH_2(u)}.
$$

To see this, multiply each summand by $u^j(1-u)^{D-j}$. For
$j\le uD$ this factor is at least
$u^{uD}(1-u)^{(1-u)D}=2^{-DH_2(u)}$, and the sum of the resulting binomial
probabilities is at most $1$.

Since $d\le D$ and $h\le\lfloor\beta D\rfloor$,

$$
M_h(d)\le
2^{\beta D}\sum_{j=0}^{\lfloor\beta D\rfloor}\binom Dj
\le2^{D(H_2(\beta)+\beta)}.
$$

Our parameter choice guarantees

$$
C(H_2(\beta)+\beta)\le\frac c2.
$$

Indeed, $H_2(u)\le u\log_2(e/u)$. Using
$\log_2(8/a)\le\ell_0$ and $\log_2(2e\ell_0)\le2\ell_0$ for
$\ell_0\ge3$, we have

$$
H_2(\beta)+\beta
\le\beta\log_2\frac{2e}{\beta}
\le3\beta\ell_0=\frac{3a}{8}\le\frac a2.
$$

As $Ca\le c$ and $D\le C\log_2N$, it follows that

$$
\boxed{M_h(d)\le N^{c/2}.}
$$

### 5.4 The small-optimum promise guarantees enough width

The cases $k=0$ and $k=1$ are solved exactly by the initial checks. Assume
now that $2\le k\le N^\tau$, and put $n=\log_2N$.

Then $n\ge1/\tau$ and

$$
\ln(k+2)\le\ln(2k)\le2\tau\ln N.
$$

The explicit parameter choice gives

$$
Cn\ge\frac C\tau\ge\frac{128\ln2}{\beta^2}.
$$

In particular, $D=\lfloor Cn\rfloor\ge Cn/2$ and $D\ge8/\beta$.
The size–width bound is therefore at most

$$
W\le\min\left\{d,\;4\sqrt{D\tau\ln N}+2\right\}.
$$

Dividing the expression inside the second entry by $D$, we obtain

$$
4\sqrt{\frac{\tau\ln N}{D}}+\frac2D
\le4\sqrt{\frac{2\tau\ln2}{C}}+\frac2D
\le\frac\beta2+\frac\beta4=\frac{3\beta}{4}.
$$

Since $\beta D\ge8$, we have
$3\beta D/4\le\lfloor\beta D\rfloor$. Thus

$$
W\le\min\{d,\lfloor\beta D\rfloor\}=h.
$$

There is a consistent width-$h$ comparator, so the restricted peeling phase
never switches to the full universe. Section 5.2 bounds its length by
$M_h(d)$, and Section 5.3 bounds that quantity by $N^{c/2}$.

For $k\ge1$ this proves, in particular,

$$
\operatorname{err}_S(L)=0,\qquad
|L|\le N^{c/2}\le N^c k.
$$

The exact initial solutions cover $k=0,1$. There is no hidden sufficiently-large-
$N$ qualification: when $k\ge2$ is compatible with the promise, the needed
rounding inequalities already follow from $N^\tau\ge2$.

## 6. Running time with all parameter dependence exposed

### 6.1 Scan/enumeration work on the promised class

Write $M=M_h(d)$. The zero/one-rule checks require at most $A N(d+1)$
operations. Generate the restricted conditions by choosing a width
$j\in\{0,\ldots,h\}$, a $j$-element coordinate subset, and its $2^j$
assignments. Generating the next condition costs at most $A(d+1)$ operations;
this does not enumerate and discard all $3^d$ conditions.

A scan tests every remaining point against the condition in at most
$A(d+1)$ operations per point. One pass, including removal of the selected
condition's trace, therefore takes at most $A N(d+1)M$ operations.
There are at most $\min\{N,M\}$ passes on the promised class. Consequently,

$$
T_{\mathrm{search}}
\le \frac A2 N(d+1)\left[1+M\min\{N,M\}\right]
\le A N^{1+c}(d+1)
\le A N^{1+c}(C\log_2N+1).
$$

Here and below $A$ is absolute: it does not grow with $C$ or $1/c$.
Thus it is legitimate to write

$$
O\!\left(N^{1+c}(C\log_2N+1)\right)
$$

for this part of the work, with a universal implicit constant. The factor
$C\log_2N+1$ is retained even when $C$ is small. The setup cost is counted
separately next.

### 6.2 Exact parameter setup

Suppose the constants are supplied as positive binary-encoded rationals

$$
C=\frac pq,\qquad c=\frac rs,
\qquad p,q,r,s\in\mathbb Z_{>0}.
$$

Reduction to lowest terms is unnecessary. Define

$$
n=\lceil\log_2N\rceil,\qquad
B=\operatorname{bits}(p)+\operatorname{bits}(q)
 +\operatorname{bits}(r)+\operatorname{bits}(s),
$$

where $\operatorname{bits}(t)=\lfloor\log_2t\rfloor+1$ for positive integers.
All dependence on the supplied representations is made explicit through
$p$ and $B$.

Use integer products and comparisons to form $a$. Compute $\ell_0$ exactly
by comparisons with powers of two, and represent $\beta$ as a rational.
The relevant integers have at most $A(B+1)$ bits: in particular
$\ell_0\le B+4$. No floating-point logarithms or entropy evaluations are
needed by the algorithm. The value $\tau$ is used only in the analysis;
the algorithm never needs to evaluate $\ln2$.

The dimension cutoff is also computable exactly. Form the integer
$Z=N^p$ using repeated schoolbook multiplication and set

$$
E=\operatorname{bits}(Z)-1=\lfloor\log_2Z\rfloor,
\qquad D=\left\lfloor\frac Eq\right\rfloor.
$$

This is exactly $\lfloor C\log_2N\rfloor$: for integer $q\ge1$,
$\lfloor x/q\rfloor=\lfloor\lfloor x\rfloor/q\rfloor$.
Finally compute $\lfloor\beta D\rfloor$ by integer multiplication and
division. This avoids rounding errors in the searched width.

Every intermediate power has at most $p(n+1)+1$ bits. There are at most
$p$ schoolbook multiplications; charging each by the square of this maximum
bit length gives a conservative, explicit setup bound

$$
\boxed{T_{\mathrm{setup}}\le A F,\qquad
F=p^3(n+1)^2+(B+n+10)^2.}
$$

This bounds the setup in elementary bit operations and hence also in word
operations. It includes reading the parameter encodings, forming the rational
cutoff, and computing $D,h$. Faster powering is possible but is unnecessary
for the guarantee.

Combining setup and search, the complete promised-class bound is

$$
\boxed{T_{\mathrm{word}}(N,d;p,q,r,s)
\le A\!\left[
 p^3(n+1)^2+(B+n+10)^2+
 N^{1+c}(C\log_2N+1)
\right].}
$$

There are no $C$- or $c$-dependent constants hidden in this inequality.
Encoding length must be stated when parameters are inputs: their numerical
values alone cannot bound the time needed to read arbitrarily long encodings.
Section 6.6 removes this ambiguity with a prescribed choice of parameters.

### 6.3 Computation model and explicit bit-cost bound

The search bound counts word-RAM operations: memory accesses, Boolean
operations, comparisons, shifts, and integer arithmetic. A sufficient word
length for the entire algorithm, including setup and fallback, is

$$
\boxed{w=8\left[p(n+1)+B+n+d+10\right]\ \text{bits}.}
$$

This is deliberately conservative. It holds sample addresses, enumeration
counters for the full $3^d$ universe, the integer $N^p$, and rational-cutoff
arithmetic. It specifies the word-size dependence instead of treating
parameter-dependent integers as unbounded unit-cost objects.

In the bit-cost RAM model, using schoolbook arithmetic, each such word
operation can be implemented with at most $A w^2$ bit operations; a word
read or write costs at most $A w$. Thus an explicit conservative bound is

$$
\boxed{T_{\mathrm{bit}}
\le A w^2\!\left[
 p^3(n+1)^2+(B+n+10)^2+
 N^{1+c}(C\log_2N+1)
\right].}
$$

The word-RAM and bit-cost RAM guarantees are distinct. This report makes no
unstated unit-cost assumption for real arithmetic, nor a claim about a
multitape Turing-machine simulation of random access.

### 6.4 Behavior outside the small-optimum promise

On every consistently labeled sample, the full universe contains a safe
condition: specifying all coordinates of a remaining feature vector suffices.
The fallback therefore still returns zero error. Including the restricted
prefix and setup, the general word-operation bound is

$$
\boxed{T_{\mathrm{word}}
\le A\left[F+N^2(d+1)3^d\right]
\le A\left[F+
 N^{2+C\log_2 3}(C\log_2N+1)\right].}
$$

The corresponding bit-cost bound multiplies the right-hand side by $w^2$,
with an absolute constant. The restricted search costs at most one extra
pass when it fails, and it has at most $3^d$ conditions per pass.

The faster promised-class bound and the $N^c$ approximation guarantee require
$k\le N^{\tau(C,c)}$. The algorithm does not have to test that promise.
In particular, no $N^{1/c}$ or $N^k$ factor appears in its running time.

### 6.5 Memory and output size

Stream conditions instead of storing all their traces. Besides the input
and output, the algorithm uses at most $A(N+d+1)$ words for the remaining
indices, current and selected conditions, enumeration state, and a constant
number of parameter-arithmetic registers. The integers in those registers
fit the explicit word length $w$ above.

On the promised class, the output contains at most $N^{c/2}$ rules. Storing
conditions as ternary vectors, a conservative total bound is

$$
S_{\mathrm{word}}
\le A\left[N(d+1)+(d+1)N^{c/2}+1\right],
$$

including the explicit input and output. The total bit-space bound is at most
$w$ times this right-hand side. The zero/one-rule branches obey the same
bounds. Outside the promise, replace $N^{c/2}$ by $N$.

### 6.6 A bound using only the target values $C,c$

For arbitrary positive target constants, prescribe the following rational
parameters rather than allowing arbitrary encodings:

$$
P=\max\{1,\lceil C\rceil\},\qquad
u=\max\{0,\lceil\log_2(1/c)\rceil\},\qquad
c'=2^{-u},\qquad C'=P.
$$

Then $C'\ge C$ and $0<c'\le c$. If $0<c\le1$, also $c/2<c'\le c$.
Run the algorithm with $C',c'$; its required optimum promise is now
$k\le N^{\tau(P,c')}$, with this precise parameter choice.

Here $p=P,q=1,r=1,s=2^u$ and

$$
B_P=\lceil\log_2(P+1)\rceil+u+4.
$$

The fully explicit value-based word-operation bound is

$$
\boxed{T_{\mathrm{word}}
\le A\!\left[
 P^3(n+1)^2+(B_P+n+10)^2+
 N^{1+c'}(P\log_2N+1)
\right]
\le A\!\left[
 P^3(n+1)^2+(B_P+n+10)^2+
 N^{1+c}(P\log_2N+1)
\right].}
$$

All displayed quantities are explicit functions of $N,C,c$. In particular,
$P\le C+1$ and $u\le1+\max\{0,\log_2(1/c)\}$: the setup has cubic dependence
on the rounded dimension constant and at most quadratic dependence on
$\log_2(1/c)$, while the main search exponent is at most $1+c$.

For this prescribed choice the promise exponent simplifies to

$$
\boxed{\displaystyle
\tau(P,c')=
\frac{1}{8192\,P\,2^{2u}\ln2\,
 \lceil\log_2(8P\,2^u)\rceil^2}.}
$$

The minimum with $1/4$ is unnecessary here because $P\ge1$, $c'\le1$.
For the bit-cost bound, use
$w_P=8[P(n+1)+B_P+n+d+10]$ and multiply the word-operation bound by $w_P^2$
with an absolute constant. This choice proves the arbitrary-real-target
version without hiding a dependence on $C$ or $c$ in parameter encodings.

## 7. The simpler parameter-free algorithm

If the faster bound is unnecessary, the earlier algorithm is sufficient:
repeatedly choose a minimum-width safe condition from all $3^d$ conditions,
stop at a monochromatic remainder, and use its label as the default.
This algorithm needs neither $C,c$ nor $k$ and always returns zero error
on a consistently labeled sample.

Its unconditional length and running-time bounds are

$$
|L|\le\sum_{j=0}^{W}2^j\binom dj,\qquad
T\le A N^2(d+1)3^d,
$$

with $W$ as in Section 5.1 and $A$ absolute. Under $d\le C\log_2N$ its
running time is at most $A N^{2+C\log_2 3}(C\log_2N+1)$ word operations;
there is no parameter setup because this algorithm receives no $C,c$.
These imply the earlier asymptotic
$\forall c\,\exists\tau$ result. The explicit algorithm above adds a
width cutoff and an exact one-rule check to obtain a faster running time
and an all-sample-sizes guarantee on the stated promise.

## 8. What remains unresolved

This result permits growing $k$, including $k=N^{o(1)}$ for every fixed
$c>0$ eventually. It does not resolve an arbitrarily small $N^c$ ratio on
a fixed positive range $k\le N^{\tau_0}$ independent of $c$. It also does
not establish a polylogarithmic ratio for general mixed-label decision lists.

The original noisy/additive-error question in `summary.md` is separate.
All guarantees in this report concern exact consistency and multiplicative
approximation of the number of nondefault rules.

## Reference

Olaf Beyersdorff, Joshua Blinkhorn, Meena Mahajan, and Tomáš Peitl,
**Hardness Characterisations and Size-Width Lower Bounds for QBF
Resolution**, *ACM Transactions on Computational Logic* 24(2), Article 10
(2023), Lemmas 6.4–6.5.

- DOI: <https://doi.org/10.1145/3565286>.
- Author version:
  <https://repositum.tuwien.at/bitstream/20.500.12708/154458/3/Beyersdorff-2023-ACM%20Transactions%20on%20Computational%20Logic-am.pdf>.

The size–width lemma is the imported structural theorem. The algorithm,
explicit parameter choice, rounding analysis, and running-time guarantees
above are derived here from it.
