# Partial results for shortest conjunction decision lists

**The unrestricted polylogarithmic approximation remains unresolved.**
The strongest structural approximation established here is deterministic,
has exactly zero empirical error, and gives ratio

$$
H_m\sum_{j=0}^{\min(w,d)}2^j{d\choose j}
\le(1+\ln N)(2C\log_2N+1)^w
\tag{1}
$$

if some unrestricted optimum has all rules of one output label of width
at most fixed $w\ge1$. The other label has arbitrary widths, growing
rule count and arbitrary interleaving. No comparator, selected pool,
ordering, owner assignment or optimum length is supplied.

A second theorem gives ratio $H_m\max(1,d)^W$ if the unions of one
output's conditions in every comparator prefix have cube-cover number
at most fixed $W$, on the entire observed support. Counts and widths
on both sides may grow. The algorithm guesses only a constant-size
final union, not the growing prefix word.

These are structural partial theorems. Neither promise is proved for
every optimum. Their proofs below are complete; the later results have
separate proof artifacts. No literature novelty claim is made.

This report records mathematics. Actual elapsed time, final packet,
review scopes and session disposition belong to the closing result
and verification records. Affirmative validation of partial theorems
cannot resolve the unrestricted target.

## 1. Model and conventions

Input is a consistent indexed Boolean sample of size $N\ge2$,
dimension $d\le C\log_2N$ for fixed $C>0$, with $m$ distinct
observed points after deduplication. Keep original $N$ in every
dimension and complexity promise. Conditions are all $3^d$ ternary
conjunction cubes, including all-star. Both labels and defaults and
arbitrary first-match orders are allowed. Length counts nondefault
rules; $K^*$ is the unrestricted zero-error empirical optimum.

Let $H_m=\sum_{i=1}^m1/i\le1+\ln m$, $D=\max(1,d)$.
Monochromatic samples return the correct empty list first, including
$d=0$. Ratios concern mixed samples with $K^*\ge1$.
An exact-point minority-class list is always consistent and has at most
$m-1$ rules. Keep this fallback, and select the shortest verified
consistent output of all applicable procedures.

Global purity means avoiding every observed point of the other label.
Current safety means avoiding the other label only in the actual
remaining sample. Current safety suffices for appending a rule.
Time bounds use elementary word operations unless explicitly labeled
bit bounds; words have $O(\log N)$ bits for fixed $C$. Charging
bit arithmetic adds polynomial logarithmic overhead and does not create
a dependence of the polynomial degree on growing $K^*$.
Greedy set cover of $t$ targets uses at most $H_t$ times a minimum
integral or fractional cover cost, when the available family is fixed.
For example, charge each newly covered target the reciprocal of the
chosen rule's gain. Each comparator set accumulates at most $H_t$
charges; summing against a fractional cover proves the same bound.

## 2. One-output pool activation

**Theorem.** Let $\mathcal P$ be an explicitly enumerable pool of
$p\ge1$ original conditions containing all-star. There is a deterministic
algorithm which, against every consistent comparator with $q$ output-0
rules from $\mathcal P$ and $c$ arbitrary output-1 rules, returns

$$
|A(S)|\le\min\{m-1,pH_m(q+c)\}.
\tag{2}
$$

Either comparator default is allowed. The algorithm does not know the
selected pool subset, its order, $q,c$, or the default.

Maintain actual remaining label sets $R_0,R_1$. Stop if the remainder
is monochromatic, using its label as default. Otherwise:

1. Enumerate all positive conditions avoiding $R_0$.
2. For each $Z\in\mathcal P$ with $Z\cap R_0\ne\varnothing$,
   greedily cover $Z\cap R_1$ using those currently safe conditions.
   Singletons ensure feasibility; empty targets cost zero.
3. Choose $Z$ with the fewest computed preparers. Append its preparers
   and then $Z\to0$, deleting actual matches after each rule and
   checking for monochromatic stopping throughout.

Preparers avoid every remaining zero. After them $Z$ has no remaining
one match, so its zero rule is safe and removes its entire remaining
zero trace, including at least one point. That condition cannot be
activated again. Thus there are at most $\min(p,m)$ activations.
Every emitted rule has positive monochromatic gain; the output has at
most $m-1$ rules.

Fix a comparator as in the theorem and consider any activation after
arbitrary earlier safe choices. Take its first zero rule $Z_*$ still
matching a remaining zero, if one exists. Every earlier comparator zero
condition has its entire zero trace already removed. Each earlier
positive comparator rule is therefore currently safe: its wrong zero
matches were protected by still earlier zero rules. Every remaining one
point matching $Z_*$ was first protected by an earlier positive rule.
So at most $c$ currently safe comparator conditions cover its blockers.

If there is no such $Z_*$, the comparator default must be zero while
$R_0\ne\varnothing$. All its positive rules are currently safe and cover
$R_1$. The all-star zero candidate can use that cover. In either case
some candidate's greedy preparation costs at most $H_m c$. The chosen
one costs no more, giving at most $p(1+H_m c)$ total rules.
For $q\ge1$ this proves (2). For $q=0$, a mixed comparator has
default zero and all its positive rules are globally pure. Keep the
ordinary pure-cover trials for both defaults; they cost at most $H_m c$.
This proves every case without an order oracle.

For width $w$, choose all conditions of width at most $w$:
$$
p_w=\sum_{j=0}^{\min(w,d)}2^j{d\choose j}\le(2d+1)^w
\quad(w\ge1),\qquad p_0=1.
$$
The inequality encodes each signed coordinate set as a padded
length-$w$ word. Try both output orientations. If an optimum meets the
one-output width promise this proves (1). A single algorithm can run
all $w=0,\ldots,d$ and keep the shortest output.

Precompute traces of all $3^d$ conditions. A direct greedy cover has
at most $m$ steps, each scanning $3^d$ conditions and $m$ target
bits. One pool takes at most
$$
O(N(d+1)3^d+\min(p,m)pN^2(d+1)3^d).
$$
For fixed width the bound is $O(N^2(d+1)p_w^2 3^d)$.
All widths and both orientations together take
$$
O(N^3(d+1)^2 3^{2d}),
\tag{3}
$$
using $O(N(d+1)+m3^d+d3^d)$ machine words, each $O(\log N)$
bits at fixed $C$. The degree in (3) is independent of growing
width and comparator length.

For $w=1$, elementary literal clipping already gives a pure-cover
polylogarithmic regime; no new regime is claimed there. The extension
of interest is $w\ge2$, growing narrow-label count and unrestricted
interleaving. With both pools unrestricted, $p=3^d$ is too large
for the desired guarantee.

Registered proof and checker: claim C-one-output-pool-activation:r1,
[pool activation](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/one-output-pool-activation-r1.md>).

## 3. Bounded prefix unions

For one comparator output $b$, let $U_j$ be the trace on all $X$
of the union of its output-$b$ conditions before position $j$.
Include the final prefix. Suppose each $U_j$ is the trace of a union
of at most fixed $W$ original cubes. Representing cubes need not be
comparator conditions and are not supplied. Equality concerns points
of both labels.

**Theorem.** A deterministic polynomial algorithm returns
$$
|A_W(S)|\le\min\{m-1,H_m D^W(q+c)\},
\tag{4}
$$
against every comparator with this property for either output, where
$q,c$ count its rules of those two outputs. If an optimum has the
property, the ratio is $H_mD^W$.

For an opposite-output condition $P\to1-b$, every wrong label-$b$
match lies in the earlier $b$-union $U$. Represent that union on
$X$ by $R_1,\ldots,R_w$, $w\le W$. The complement of a cube
fixing $k_i$ literals is the union of its $k_i\le d$ falsified
literal conditions. Distribute these complements to express
$P\setminus\bigcup_i R_i$ as at most $D^W$ original cubes.
Discard contradictory intersections. For $w=0$ retain $P$;
an all-star representative makes the difference empty.
Every retained cube is globally pure opposite output on $X$.
Every point first owned by $P$ lies outside the earlier union, and
is covered. Across all $c$ opposite-output rules these clips cover
their first-owned points using at most $D^Wc$ pure cubes.

If default is $b$, this covers the entire opposite class; ordinary
greedy proves (4). If default is $1-b$, the final $b$-union $U_f$
contains all label-$b$ points. Its opposite-label blockers all have
opposite-output nondefault first owners and are covered by the clips.
Represent $U_f$ by $w\le\min(W,q)$ cubes, using the original
conditions if $q<W$. Put the clipped pure cover first, these $w$
conditions next, and default $1-b$. This is consistent.

The algorithm enumerates both $b$ and every tuple of at most $W$
cubes covering all label-$b$ points. Greedily cover its opposite
blockers with globally pure cubes, append the tuple and use default
$1-b$. Keep ordinary pure-cover trials and the fallback as well.
Every candidate is consistent. The existential final tuple above is
included and costs at most $w+H_mD^Wc\le H_mD^W(q+c)$.
No intermediate prefix representation is guessed.

At most $(W+1)3^{dW}$ tuples are streamed. Conservative bit bounds:
$$
\begin{split}
\text{time }&O(N(d+1)+(W+1)(d+1)^3m^2 3^{d(W+1)}),\\
\text{space }&O(N(d+1)+(m+d+1)(d+1)3^d+W(d+1)).
\end{split}
\tag{5}
$$
Store traces and only one candidate; truncate at the incumbent fallback
length. Degrees depend on fixed $C,W$, not growing $q,c,K^*$.

If one output's conditions partition into $W$ inclusion chains, every
prefix has at most $W$ maximal members, so the promise holds.
Useful conditions in one chain can grow through $d+1$ distinct cubes;
this is not just fixed-count enumeration. No universal bounded chain
count or prefix-union cover number is established.

Registered proof and checker: C-bounded-prefix-union-normal-form:r1,
[prefix unions](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/bounded-prefix-union-normal-form-r1.md>).

## 4. Further restricted approximation and exact regimes

Each following result has a separate argument.
Favorable hypotheses need not cover every optimum.

| Hypothesis | Guaranteed length | Complexity or cutoff |
|---|---|---|
| One output uses $q\le s$ rules, fixed $s$, arbitrary width | $q+H_m c$ | $O((s+1)^2N^2(d+1)3^{d(s+1)})$ |
| One output width $\le w$, fixed $w$, and $q\lceil\log_2p_w\rceil\le a\lfloor\log_2N\rfloor$ | $q+H_m c$ | $O(N^{a+2}(d+1)3^d)$, fixed $a,w,C$ |
| One output total literal budget $B$, $B\lceil\log_2(4d)\rceil\le a\lfloor\log_2N\rfloor$ | $q+H_m c$ | Same time, fixed $a,C$; normalize all-star into default |
| One output narrow pool plus fixed $s$ wide exceptions | $H_m(p_w+s)K^*$, if an optimum has this form | $O((s+1)N^2(d+1)(p_w+s)^2 3^{d(s+1)})$ |
| Full support with short optimum | Exact $K^*$ if $G(K^*,d)\le a\lfloor\log_2N\rfloor$ | $O(N^{a+2}(d+1)3^d)$ |
| Short decision-list membership mask of partial support | Exact $K^*$ under cutoffs below | $O(N^{a+2}(d+1)2^d3^d)$ |
| Fixed $D_0$ output blocks | $\min\{m-1,kH(kH+1)^{D_0-1}\}$, $H=1+\lceil\log_2m\rceil$ | Known layered reconstruction; polynomial for fixed $D_0,C$ |

Here $q,c$ count the two outputs, $k$ is comparator length, and
fixed integer $a\ge1$ controls a polynomial search budget. A guessed one-output
word defines deadlines for opposite-label points and release indices
for opposite rules. One global greedy cover of feasible pairs pays for
each opposite rule once; it avoids expanding every wide complement.

Put $\ell=\lfloor\log_2N\rfloor$, $\delta=\lceil\log_2(d+1)\rceil$.
For the full-support exact row,
$$
G(k,d)=(k+1)[2+(2k+1)(2k+\delta)].
$$
For each fixed $\varepsilon>0$ this eventually covers
$k\le[(1-\varepsilon)a\log_2N/4]^{1/3}$.
The prime-factor bound behind the search is that a $t$-rule indicator
has at most $(d+2^t)^{t+1}$ maximal pure cubes. Finite branching
cutoffs ensure polynomiality.

For the support-mask row, discover a minimum-length $r$-rule list for
the full-cube indicator of $X$, and require
$$
\begin{split}
F(r,d)&=(r+1)[2+(2r+1)(2r+\delta)]\le a\ell,\\
J(k,r,d)&=\delta+(2k+r+1)(2k+r+\delta),\\
(k+1)[2+(r+1)J(k,r,d)]&\le a\ell.
\end{split}
$$
Omit the first cutoff for $r=0$. Eventually these include
$\max(r,k)\le[(1-\varepsilon)a\log_2N/9]^{1/4}$.
Fixed $r$ retains a cube-root target range with the displayed cutoff.
Arbitrary partial supports need not have short masks.

The layered reconstruction is imported, with no novelty claim, from
Dhagat–Hellerstein (1994) and the account in Nevo–El-Yaniv (2002),
Section 3.4:
[proceedings paper](https://cdn.aaai.org/Symposia/Fall/1994/FS-94-02/FS94-02-014.pdf),
[JMLR paper](https://www.jmlr.org/papers/volume3/nevo02a/nevo02a.pdf).
Fixed block count and polylogarithmic $k$ give a polylogarithmic ratio;
growing block count is not covered uniformly.
The single parameter-free layered algorithm has time
$O(N(d+1)3^d+(d+1)m^6 3^{2d})$, with polynomial space and degree
independent of growing $k,D_0$.

Proof artifacts:
[fixed skeleton](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/bounded-output-skeleton-cover-r1.md>),
[narrow growing skeleton](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/growing-narrow-output-skeleton-r1.md>),
[literal budget](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/output-literal-budget-skeleton-r1.md>),
[exceptions and parity](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/pool-exceptions-parity-boundary-r1.md>),
[prime branching](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/prefix-factor-full-prime-branching-r1.md>),
[support masks](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/short-list-support-mask-r1.md>),
[layered reconstruction](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/layered-exact-reconstruction-r2.md>).

## 5. Recognized completions and full-table structure

Both constant completions $f_0,f_1$ can be formed in polynomial
original $N$, since $2^d\le N^C$. A short list for either fits the
sample. Relative full-table optimum guarantees do not transfer:
$K^*(S)\le K^*(f_c)$ goes in the unhelpful direction. An absolute
polylogarithmic completion length does imply that ratio on mixed samples,
using only $K^*(S)\ge1$.

| Recognized completion structure | Length, also capped by $m-1$ | Time; bit space |
|---|---|---|
| Disjoint AND/OR modules: $b$ complex forks, nonsimple core width $r$ | $[(d+1)2^r]^{b+1}-1$ | $O(N(d+1)+(d+1)^{10}6^d)$; $O(N(d+1)+(d+1)^6 4^d)$ |
| Hybrid disjoint AND/OR/XOR modules and coordinate branches: $b$ complex forks, $s$ XOR nodes, core width $r$ | $2^s[(2d+1)2^r]^{b+1}-1$ | $O(N(d+1)+(d+1)^{18}6^d)$; $O(N(d+1)+(d+1)^{10}4^d)$ |
| Algebraic normal form with $w$ inclusion chains of nonconstant monomials | $(d+1)^w-1$, minimized over all input polarities | $O(N(d+1)+(d+1)^3 12^d)$; $O(N(d+1)+(d+1)^2 3^d)$ |
| Positive OR/XOR read-once formula on $r$ overlapping conjunction atoms, common polarity, atoms 2-cover-free, $b$ complex forks, $s$ binary XOR nodes | $2^s(r+1)^{b+1}-1$; XOR of OR groups sharpens to $\prod_j(r_j+1)-1$ | $O(N(d+1)+(d+1)^5 16^d)$; $O(N(d+1)+(d+1)^3 4^d)$ |

Fixed $b$, $r=O(\log(d+1))$, $s=O(\log(d+1))$ suffice in the
hybrid row. Fixed algebraic chain count suffices in the third. In the
last row, fixed $b$, polynomially many atoms in $d$, and
$s=O(\log(d+1))$ suffice. All are recognized polylogarithmic regimes,
not universal structural theorems about a short optimum.

Algorithms verify complete table equality, retain parameter/default
frontiers rather than the first parse, and cap reconstruction costs.
XOR substitution works with overlapping variables and charges a product
of label-leaf counts. Intersections of original conjunctions remain
original cubes after discarding contradictions.

For the overlapping-atom row, minimal nonconstant algebraic supports
recover atoms. The coefficient of a pair union determines OR versus
XOR at their least common ancestor: 2-cover-freeness excludes a third
atom from that union. The coefficient graph is a cograph, whose
union/join decomposition recovers a canonical tree. Full-table
verification remains essential.

One limitation is explicit: an OR of $k$ disjoint width-$k$ cubes
has a $k$-rule list, but requires at least
${k\choose\lfloor k/2\rfloor}$ algebraic chains in every polarity.
Other algorithms handle that family; this is no portfolio lower bound.

Two full-table comparisons are proved separately. A unate disjoint-block
DNF or CNF has exact unrestricted optimum $t+e$, where $t$ counts
blocks of width at least two and $e$ indicates the presence of singleton
blocks. A linear read-once AND/OR formula with $r$ alternating nonempty
fresh-variable layers compiles to $r$ rules and has
$K^*\ge\lceil r/2\rceil$, giving a 2-approximation. The device is a
coordinate-once path: every cube matches an interval and changes at
most twice, so a $K$-rule list's output changes at most $2K$ times.
The general growing-fork read-once comparison remains unresolved.
Finite optimum agreement in small families is not a proof.

Proof artifacts:
[functional modules](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/functional-module-compilation-r1.md>),
[hybrid compiler](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/functional-hybrid-compilation-r1.md>),
[polarity chains](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/polarity-chain-compilation-r1.md>),
[overlapping atoms](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/overlapping-algebraic-modules-r1.md>),
[read-once comparisons](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/sol-max-readonce-comparison-r1.md>).

### Inhibitory common-prefix recovery

For a full table consider
$$
f=(\bigvee_{A\in\mathcal G}x_A)\land
       \neg(\bigvee_{B\in\mathcal H}x_B).
$$
All atoms have common width $w$, $\mathcal G\ne\varnothing$;
cross intersections and distinct inhibitor intersections have size
at most $t$; $w>3t$. Consumer-consumer intersections are unrestricted.
Every inhibitor also avoids $I=\bigcap_{A\in\mathcal G}A$.
Unknown common coordinate polarity and output flip are allowed.

Minimal true supports are exactly $\mathcal G$. Positive zero
certificates of size at most $w$ are exactly $\mathcal H$:
a zero certificate $S$ requires for every $A$ an inhibitor
$B_A\subseteq S\cup A$, so $|S\cap B_A|\ge w-t$.
Two distinct witnesses give $|S|\ge2w-3t>w$.
One common witness therefore satisfies $B\setminus S\subseteq I$,
and avoidance of $I$ forces $B\subseteq S$, hence $B=S$.

Subset/superset transforms extract the families in all $2^{d+1}$
orientations. Check all hypotheses and full recomposition. Emit
inhibitor-zero rules, consumer-one rules, default zero, restoring signs
and output flip; choose the shortest accepted orientation or fallback.
Time is $O(N(d+1)+(d+1)^3 8^d)$, space
$O(N(d+1)+(d+1)^2 2^d)$ bits, independent of growing atom counts.
If some unrestricted full-table optimum has this form, output is exactly
$K^*$. Without that promise it only competes with representations
in the recognized class.

Constant completions need not preserve even an optimal-form extension.
Observe the four points with $x_2\ne x_3$ in three variables and label
by $x_1$. Empirical optimum is one and natural extension $x_1$ has
the form. Both constant completions
$x_1\land(x_2\mathbin{\rm XOR}x_3)$ and
$x_1\lor\neg(x_2\mathbin{\rm XOR}x_3)$ are outside this class under
every orientation. The strict threshold and avoidance condition also
have explicit boundary witnesses.
See [inhibitory recovery](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/sol-max-inhibitory-recovery-r1.md>).

### Direct empirical recovery on union-closed supports

There is a direct partial-sample extension. In some unknown input polarity,
let the observed supports $X_p$ be closed under bitwise union. Require
the consumer and inhibitor atom supports themselves to belong to $X_p$,
with the same common width, intersection and common-intersection avoidance
conditions just stated. No missing label is assigned.

Minimal observed true supports recover $\mathcal G$. Observed supports
$S\in X_p$, $|S|\le w$, with no observed true superset recover
$\mathcal H$. The critical equivalence still holds: if no inhibitor
lies in $S\cup A$, union closure makes $S\cup A$ an actually observed
true extension. The $w>3t$ witness argument then applies unchanged.
Test all polarities and output flips, verify observed recomposition only,
and retain the shortest candidate or fallback.

If some unrestricted empirical optimum has this form, the output is
exactly $K^*(S)$, with growing counts and width allowed and no supplied
comparator. Conservative deterministic bit bounds are
$$
O((d+1)^3[N+m^2 2^d])\ \text{time},\qquad
O((d+1)^2[N+m+1])\ \text{space}.
$$
Union closure, atom membership and the optimum-form promise remain
explicit restrictions. They are not established for arbitrary samples.
The earlier four-point $x_2\ne x_3$ example becomes union-closed after
flipping $x_3$, and this procedure returns its one-rule empirical optimum.
That example is an interface check; the elementary one-rule hull baseline
also solves it. The theorem concerns the stated class with growing $K^*$.
See [direct union-closed recovery](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/union-closed-inhibitory-recovery-r1.md>).

## 6. Computable owner-hull certificates and the exact gap

The surviving relaxation route has a polynomial output algorithm, but
its certificate has no universal polylogarithmic upper bound.
Use a universal word of $m$ copies of all labeled conditions and a
free fixed-default dummy. Try both defaults and the first feasible
integer cap $k$ in a rational localized pair-moment LP.
Every optimum embeds, so $k\le K^*$. All Boolean pair boxes,
variable/complement localizations of prefix-OR, wrong-label guards,
endpoints and budget rows are included. The word has $T=2m3^d$
real positions and polynomially many rational variables and rows.

For position $h$, let $A_h$ be correctly labeled points with positive
marginal first-owner increment. Replace its condition by the cube hull
of $A_h$, dropping empty owners. Compute the fractional globally pure
cover cost $c_h\in[1,m]$ of $A_h$.
For each threshold $r\ge1$, retain the first occurrence of every
distinct labeled hull with $c_h>r$, in chronological order. Their
number is $p_r$. Let $B_r$ be the actual error set of this hard word
with the LP default, and $\tau_r$ its fractional globally pure cover
cost. A greedy pure prefix followed by the hard word is consistent and
$$
|L_r|\le p_r+H_m\tau_r,\qquad
\tau_r\le r(p_r+1)k.
\tag{6}
$$
Try $r=1$ and every distinct $c_h$, polynomially many thresholds.
All quantities are computed, not supplied by an oracle. Cost one
is equivalent to a globally pure owner hull.

The proof conditions on each first wrong hard consumer. Its guard
supplies unit earlier owner mass; earlier hard hulls cannot contribute,
so that mass comes from easy owners. The localized budget gives total
conditional selection weight at most $k$. Replacing easy owners by
their globally pure fractional covers costs at most $rk$ per hard
consumer, plus $rk$ for terminal blockers. Their union is safe.
One global greedy pure cover proves (6).

The missing global estimate is precisely
$$
\boxed{\min_r[p_r+H_m\tau_r]\le
       \operatorname{poly}(d,\log m)\,k.}
\tag{7}
$$
The present argument may pay $rk$ separately for many consumers,
while a real list shares preparation. Minimum total selection mass,
positive-support hull normalization and pairwise consistency have not
removed that payment. A global coupling, sparsification or charging
theorem must control these computed quantities. A favorable unknown
optimal owner assignment alone does not suffice.

Explicit Boolean owners and OR witness chains can require each selected
cube to have an owner and both sides of every free coordinate witnessed.
An integer optimum can be narrowed to actual first-owner hulls preserving
its first matches, so the full dictionary retains an optimal comparator.
These constraints remain polynomial. They are a proposed strengthening,
not a proved bound (7); marginal matching does not establish chronological
joint realizability. Retain the original cap $k$ after restrictions.

Two parity results distinguish feasibility strength
from extraction. First, in the full chronological model with explicit
Boolean first owners and their nonnegative pair boxes, every feasible
point on the full parity table has real selection cost $F$ satisfying
$$
F+1\ge2^{d/2}.
$$
No PSD or hull-OR hypothesis is required for this bound. To prove it,
couple the first-owner partitions of opposite-label neighboring points
using their pair moments. If both owner conditions contain the other
point, whichever is earlier contradicts the later owner's uncovered
prefix. Thus an undirected cube edge carries at most one unit of
opposite-direction owner mass. For rule free dimension $r_j$, owner
mass $M_j$, and selection mean $x_j$, including the selected free
default, this gives
$$
\sum_jM_j=2^d,\quad \sum_jr_jM_j\le d2^{d-1},
\quad M_j\le2^{r_j}x_j.
$$
Jensen's inequality yields
$\sum_jx_j\ge\sum_jM_j2^{-r_j}\ge2^{d/2}$.
Subtracting the default's unit gives the stated real-cost bound.
This is a discriminator for the strengthened relaxation, not an
approximation theorem.

Second, stronger feasibility alone cannot bound mean-hull extraction
for arbitrary solutions. Fix an actual optimal full-parity list of cost
$K^*$, normalize it to actual owner hulls, and average its images
under coordinate permutations and even bit flips. Embed rule $j$
in dictionary copy $j$. This is a genuine optimal-list distribution,
satisfying all-order moments, PSD and owner-OR witnesses. The mean
owner hulls are stable. At a fixed copy, every supported nonpure cube
has the same purification cost, so a threshold retains its whole
same-output orbit or none. The first hard orbit covers the entire
cube, making the hard word constant. Consequently every threshold has
$\tau_r=2^{d-1}$, and pure blocker-prefix extraction emits that many
singletons. For $d$ divisible by three,
$K^*\le5^{d/3}-1$, so this extraction can lose a positive power of $N$.
The mixture is minimum-cost over the exact integer convex hull; it
does not establish minimum F or first feasibility in the polynomial
relaxation, and does not refute extreme-point selection or a different
solver/extraction. A decomposition into its genuine atoms would
immediately give exact lists. Thus a proof of (7) must use substantive
solution selection or a different extraction, rather than mere
feasibility and support-hull stability.
The surviving selection question is whether a minimum-$F$ extreme
solution at the original full-dictionary cap, with subsequent owner-hull
stabilization and that cap retained, admits (7). No proof or counterexample
for that complete selection rule is established here.

See [parity edge bound](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/parity-owner-edge-lower-bound-r1.md>)
and [optimal-mixture extraction boundary](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/parity-orbit-owner-certificate-r2.md>).

Proof artifacts:
[owner-hull rounding](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/owner-hull-impurity-rounding-r1.md>),
[purification filter](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/owner-purification-filter-r1.md>),
[quantitative hull witnesses](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/owner-hull-witness-strengthening-r1.md>).

An alternative sufficient original-model target is a polynomial algorithm
which on every remainder of $u$ points finds a safe block of $t$
rules covering $g>0$ points with
$g/t\ge u/[\operatorname{poly}(d,\log N)(K^*+1)]$.
Harmonic charging then gives the desired ratio. Existence of an optimal
block is insufficient for its efficient discovery.

## 7. Retained unrestricted guarantee and baselines

The retained unrestricted bound is
$$
|L|\le40n(n+1)N^{\rho(C)}K^*,\quad
n=\lfloor C\log_2N\rfloor,
\tag{8}
$$
with zero error, singleton fallback, and deterministic time
$O(N^2(d+1)3^d)$. Let
$$
\begin{split}
\beta(q)&=4q/[4+q+\sqrt{16-16q+q^2}],\\
J(q)&=\{q\ln2+(1-q)\ln[(2-3\beta)/(1-\beta)]
                   -\ln(2-\beta)\}/\ln2,\\
\bar q&=(10-\sqrt{10})/15.
\end{split}
$$
Take $q_{\rm cap}\in(0,1/2]$ with
$H_2(q_{\rm cap})=\min(1,C)/C$, and
$q_C=\min(\bar q,q_{\rm cap})$. Then
$\rho(C)=C[H_2(q_C)-J(q_C)]$.
For $C=1$, this is
$\log_2[(11+2\sqrt{10})/9]\approx0.944821416$,
versus approximately $0.948812606$ for the earlier optimized envelope.
This modest power improvement is retained for completeness. Further
fixed-power tuning was set aside under the user's polylogarithmic priority.

See [uniform hypergeometric theorem](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/hypergeometric-uniform-r1.md>).
Its published size-width premise was checked against Beyersdorff,
Blinkhorn, Mahajan and Peitl, Lemmas 6.4–6.5:
[author version](https://repositum.tuwien.at/bitstream/20.500.12708/154458/3/Beyersdorff-2023-ACM%20Transactions%20on%20Computational%20Logic-am.pdf).

| Starting method | Scope and limitation |
|---|---|
| Singleton fallback | At most $m-1$ rules; factor $(m-1)/K^*$ |
| Hull test | Exact $K^*\le1$, $O(N(d+1))$ time |
| Enumerate short words | Exact fixed $K^*$; degree grows with $K^*$ |
| All-rule bounded width | Safe peeling uses at most $\sum_{j\le w}2^j{d\choose j}$ conditions when every comparator rule has width at most $w$ |
| Size-width peeling | Small-$K^*$ $N^c$ guarantees do not imply unrestricted arbitrarily small exponent |
| Pure cover | $H_m$ factor against a globally pure cover, not arbitrary protected rules |
| Common prefix with $r$ preparers and $s$ consumers | $s(1+rH_m)H_m$; shared preparers may be paid repeatedly |
| Subset DP | $2^m3^d\operatorname{poly}(m,d)$, not polynomial for general logarithmic dimension |
| Random-restriction compression | Distribution/access assumptions and nonzero approximation error do not transfer automatically to exact empirical optimization |

Rivest's classical width parameter is not rule count.
Lovett–Wu–Zhang's compression result is nearby work, not adopted as
an exact-sample approximation theorem:
[primary preprint](https://arxiv.org/abs/1909.10658).
Planar rectangle and IP-prefix ACL theorems also require a predicate
conversion theorem before use for arbitrary wildcard bits. A cube
fixing the low bit in each of two fixed binary address fields becomes
a checkerboard requiring exponentially many planar rectangles.
This fixed-encoding mismatch is not a theorem against every reduction.

There is a genuinely matching TCAM formulation: sequential cover with
unobserved assignments initially declared don't-cares. A cube may cover
only its output class or already covered points (or initial don't-cares),
and the sequence covers the whole observed sample. Its minimum number
of cubes is exactly $K^*+1$: append a default all-star cube to a list;
conversely replace the final useful cover cube by its output as default.
An $\alpha$-approximation for this interface would give at most
$(2\alpha-1)K^*$ nondefault rules on mixed samples. Polynomial
truth-table time would be polynomial original $N$. However the closest
checked exact method branches recursively over sequential covers and is
exponential; the inspected practical successors supply no usable global
approximation theorem. This is a bounded source finding, not a claim that
no such theorem exists. See McGeer–Yalagandula, Sections V–VII,
[primary paper](https://shiftleft.com/mirrors/www.hpl.hp.com/personal/Praveen_Yalagandula/papers/INFOCOM09.pdf).

## 8. Decisive failures, with scope limits

| Mechanism | Checked obstruction | Limitation |
|---|---|---|
| Large initial safe-rule gain | Full table of $\bigwedge_{a=1}^p\bigvee_{b=1}^p x_{a,b}$, optimum at most $p$, every safe cube has mass at most $2^{-p}$ | Does not exclude good multi-rule blocks |
| Specified minimum-width peeling and cleanup | Explicit two-block-XOR families force relative loss $2^{\Omega(\sqrt{\log N}\log\log N)}$ under the stated deletions | Other layered methods handle these families |
| Near-linear log prime count | Nested prefixes give $2^{\Omega(t^2)}$ maximal pure cubes | Not hardness of all algorithms |
| Extract only positively selected predicates | Coupled Boolean owners with PSD can require new predicates even allowing reordering | Original dictionary has short lists; no unrestricted gap |
| Pair moments automatically extend to Boolean owners | Four-variable full-table PSD witness has a negative product of derived owner increments | Extension strictness only; true optimum one |
| Observed prefixes extend automatically to all unobserved ORs | Three-variable partial pair/PSD witness is nonextendible | True optimum one, no optimum gap |
| Full capped pair/PSD relaxation is exact | Original instances have integer optimum $11t$, feasible cost $10t$ | Constant gap does not refute polylog rounding |
| Hull support normalization enforces quantitative witnesses | 59-point, 17-variable fixed-word minimum-cost extreme witness violates free-coordinate half rows | Non-PSD; original full-dictionary optimum two, not a first-cap counterexample |
| Constant completion preserves structural extension | One-rule sample in Section 5 loses inhibitory class in both completions | Absolute short-completion results remain valid |

Proof artifacts:
[width/deletion boundary](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/sol-max-minwidth-deletion-r1.md>),
[prime growth](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/sol-max-nested-prefix-primes-r1.md>),
[coupled predicates](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/sol-max-coupled-three-owner-r1.md>),
[Boolean owner extension](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/explicit-owner-extension-r1.md>),
[unobserved OR extension](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/all-cube-or-extension-r1.md>),
[capped pair/PSD gap](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/claims/capped-prefix-sixteen-graph-gap-r1.md>),
and the quantitative hull proof linked in Section 6.
None is a hardness theorem for the requested approximation.

There is also an exact progress test beyond a first-step plateau.
Let $g_h=\bigwedge_{j=1}^h[x_j\lor(a_j\land b_j)]$ and
$f_h=z\mathbin{\rm XOR}g_h(X)\mathbin{\rm XOR}g_h(Y)$, with disjoint
factors. Its supplied comparator has $8h^2+8h+1$ rules.
Fix one active gadget group in each factor. A canonical consumer has
condition $z=0$ and one true certificate for the other groups in each
factor, output zero, leaving the two active groups free.

Write $t=2^h,r=2h,U=(t+r)^2$. At a canonical state with $a,b$
activated certificate endpoints and $e$ executed consumers, the
full residual fractional pure-cover costs are exactly
$$
\tau_0=U-t(a+b)+ab-e,\qquad \tau_1=U-4(a+b),
$$
under the proof's directed-guard condition. Guards exist when
$ha<2^{h-1}$ and $hb<2^{h-1}$.
The actual greedy preparer search over every currently pure cube,
with fixed star-first ties, pays four rules per fresh endpoint.
Within the entire canonical consumer pool, maximum potential-drop per
rule therefore chooses two fresh endpoints for its first
$\lfloor(2^{h-1}-1)/h\rfloor$ packages, costing nine rules each
for $h\ge4$. The proof supplies primal covers and duals against
every newly pure residual cube, not just the original pure dictionary.

This is an exponential cost for a restricted consumer pool, despite
correct preparer reuse. The unrestricted algorithm must either pay
that prefix or select an outside consumer earlier. Global consumer
dominance and a polynomial-cost outside escape are both unproved.
The theorem identifies that interface; it is not a full trajectory
counterexample. See [canonical package epoch](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/sol-max-package-trajectory-r1.md>).
The supplied comparator's conditions have width at most five, so other
bounded-width portfolio methods already handle these samples. The result
tests this package-selection mechanism, not the complete portfolio.

An outside-consumer test makes the missing transition concrete. With
the same local condition but opposite output, the initial actual greedy
package also costs nine rules; for $h\ge3$ its two residual costs are
swapped, so its potential score ties the canonical package. Four of its
preparers are globally reusable false/false clauses, and its deleted
point mass is strictly larger. Neither fact identifies a global
maximum-score consumer or a repeated efficient escape.

At later canonical states, the specified feasible opposite package has
an exact residual formula, but greedy need not select its eight
preparers. A guarded $h=6$ matching state has an actual ninth preparer
from a newly pure, unexecuted consumer cube; the resulting cost-ten
package is lower-score than an available canonical fresh package.
Thus a feasible shared-preparation plan cannot silently be used as
the algorithm's actual transition. The full consumer-selection and
amortization obligation remains open.
See [opposite-kernel analysis](<C:/Users/chan_/Desktop/codex/Decision_lists/26-10-30-Upper bound/work/notes/sol-max-opposite-kernel-escape-r1.md>).

## 9. Computation and review limits

Versioned proof artifacts include standard-library Python checkers and
JSON results. Some checker entry points write results next to source.
Reproduce frozen evidence by importing pure functions with saving
disabled, where available, or running an isolated scratch copy.
Never overwrite registered evidence. Packet checks verify bytes and
declared dependencies, not mathematical truth.

Finite checks include every Boolean table through three variables for
the hybrid compiler, all nonempty partial three-variable samples in
the recorded checks, small inhibitory classes and bounded read-once optimum
comparisons. They support implementations. Unbounded guarantees rely on
proofs, not extrapolation. No universal small completion, read-once
optimality, bound (7), unrestricted polylog ratio or impossibility
theorem is established.

New mathematical workers and fresh reviewers under the explicit model
instruction use gpt-6.1-sol with max reasoning. The coordinator cannot
change or independently verify its own underlying model. Independent
LLM review is fallible and is not formal verification. Final review
records must identify actual packet, actual scopes and the original
target's unresolved status.
