# Affine and Fractional (x, u) Coordinates

It can be shown that a double-odd elliptic curve is birationally
equivalent to a subgroup of a twisted Edwards curve defined in a
quadratic extension \\(\mathbb{F}_{q^2}\\). The general point addition
formulas on that twisted Edwards curve can then be shown to be complete
as long as we work over that specific subgroup. From these formulas, we
can derive back a coordinate system on the original double-odd elliptic
curve, with only values in \\(\mathbb{F}_q\\), that yield complete
formulas for general point addition in the group \\(\mathbb{G}\\). These
are the \\(x, u\\) coordinates.

## Affine Coordinates

The \\(x\\) coordinate is the same one as in the affine \\(x, y\\)
coordinates. The \\(u\\) coordinate is \\(u = x/y\\). For the
neutral \\(N\\), the \\(u\\) coordinate is set to 0. Given points
\\(P_1 = (x_1, u_1)\\) and \\(P_2 = (x_2, u_2)\\) in \\(\mathbb{G}\\),
their sum in the group \\(P_3 = (x_3, u_3)\\) can be obtained with the
following formulas:
\\[\begin{eqnarray*}
    x_3 &=& \frac{b((x_1 + x_2)(1 + a u_1 u_2) + 2 u_1 u_2 (x_1 x_2 + b))}
               {(x_1 x_2 + b)(1 - a u_1 u_2) - 2b u_1 u_2 (x_1 + x_2)} \\\\
    u_3 &=& \frac{-(u_1 + u_2)(x_1 x_2 - b)}
               {(x_1 x_2 + b)(1 + a u_1 u_2) + 2b u_1 u_2 (x_1 + x_2)}
\end{eqnarray*}\\]
These formulas are **complete**.

## Fractional Coordinates

In order to perform group operations efficiently, we use a fractional
representation of the \\((x, u)\\) coordinates. Namely, a point
\\((x, u)\\) is represented by the quadruplet \\((X{:}Z{:}U{:}T)\\)
with \\(ZT \neq 0\\), and such that \\(x = X/Z\\) and \\(u = U/T\\).

Since \\(u = 1/w\\), decoding of a point from its external
representation (encoding of the value \\(w\\)) into fractional \\(x,
u\\) is done in the same way as in \\((x, w)\\) coordinates; when
\\(x\\) and \\(w\\) have been obtained, the fractional coordinates are
set to \\((x{:}1{:}1{:}w)\\) (with only a special treatment for the case
of the neutral \\(N\\)).

The addition of \\(P_1 = (X_1{:}Z_1{:}U_1{:}T_1)\\) with \\(P_2 =
(X_2{:}Z_2{:}U_2{:}T_2)\\) into \\(P_3 = (X_3{:}Z_3{:}U_3{:}T_3)\\) is
obtained with the following addition formulas:
\\[\begin{eqnarray*}
    X_3 &=& b((X_1 Z_2 + X_2 Z_1)(T_1 T_2 + a U_1 U_2) + 2 U_1 U_2 (X_1 X_2 + b Z_1 Z_2)) \\\\
    Z_3 &=& (X_1 X_2 + b Z_1 Z_2)(T_1 T_2 - a U_1 U_2) - 2b U_1 U_2 (X_1 Z_2 + X_2 Z_1) \\\\
    U_3 &=& -(U_1 T_2 + U_2 T_1)(X_1 X_2 - b Z_1 Z_2) \\\\
    T_3 &=& (X_1 X_2 + b Z_1 Z_2)(T_1 T_2 + a U_1 U_2) + 2b U_1 U_2 (X_1 Z_2 + X_2 Z_1)
\end{eqnarray*}\\]
Like their affine counterparts, these formulas are complete.

They can be implemented in cost 10M by using the following algorithm,
with the help of two precomputed constants \\(\alpha =
(4b-a^2)/(2b-a)\\) and \\(\beta = (a-2)/(2b-a)\\):

 1. \\(t_1 \leftarrow X_1 X_2\\)
 1. \\(t_2 \leftarrow Z_1 Z_2\\)
 1. \\(t_3 \leftarrow U_1 U_2\\)
 1. \\(t_4 \leftarrow T_1 T_2\\)
 1. \\(t_5 \leftarrow (X_1+Z_1)(X_2+Z_2) - t_1 - t_2\\)
 1. \\(t_6 \leftarrow (U_1+T_1)(U_2+T_2) - t_3 - t_4\\)
 1. \\(t_7 \leftarrow t_1 + b t_2\\)
 1. \\(t_8 \leftarrow t_4 t_7\\)
 1. \\(t_9 \leftarrow t_3 (2bt_5 + at_7)\\)
 1. \\(t_{10} \leftarrow (t_4 + \alpha t_3)(t_5 + t_7)\\)
 1. \\(X_3 \leftarrow b(t_{10} - t_8 + \beta t_9)\\)
 1. \\(Z_3 \leftarrow t_8 - t_9\\)
 1. \\(U_3 \leftarrow -t_6(t_1 - bt_2)\\)
 1. \\(T_3 \leftarrow t_8 + t_9\\)

The cost of 10M assumes that multiplication by constants \\(a\\),
\\(b\\), \\(\alpha\\) and \\(\beta\\) is inexpensive, i.e. that these
are all small integers or easily computed fractions such as \\(1/2\\).
Also, \\(\alpha\\) and \\(\beta\\) are not defined in \\(a = 2b\\); in
such a case, the implementation can still use the algorithm above by
working on an isomorphic curve where \\(a \neq 2b\\). Indeed, the
transform \\((x, u) \mapsto (2x, u/2)\\) is an easily computable
isomorphism from \\(E(a, b)\\) into \\(E(4a, 16b)\\), which side-steps
the issue.

For point doublings, applying the general algorithm leads to a cost
of 4M+6S (the first six multiplications are squarings in that case).
However, we can do better, with cost 3M+6S. If the source point
is \\((X{:}Z{:}U{:}T)\\), then we can compute its double in the
group \\((X''{:}Z''{:}U''{:}T'')\\) as follows:
\\[\begin{eqnarray*}
    X' &=& (a^2-4b) XZ \\\\
    Z' &=& X^2 + aXZ + bZ^2 \\\\
    X'' &=& 4b X'Z' \\\\
    Z'' &=& X'^2 - 2a X'Z' + (a^2-4b) Z'^2 \\\\
    U'' &=& 2(a^2 - 4b) (X^2 - bZ^2) Z'U \\\\
    T'' &=& (X'^2 - (a^2 - 4b)Z'^2) T
\end{eqnarray*}\\]
These formulas, internally, apply the \\(\theta_1\\) isogeny, then
\\(\theta_{1/2}\\); together, these two isogenies compute a point
doubling in \\(\mathbb{G}\\).

As with Jacobian coordinates, sequences of successive doublings allow
for extra optimizations. In fractional \\((x, u)\\) coordinates, we can
apply the \\(\psi_1\\) isogeny in cost 4M+2S, with an output in Jacobian
\\((x, w)\\) coordinates. We can then apply the \\(\psi\\) and
\\(\theta\\) isogenies repeatedly, to achieve the same marginal
per-doubling costs as in Jacobian \\((x, w)\\) coordinates. The final
isogeny can be modified to produce an output back in fractional
\\((x, u)\\) coordinates with very low overhead (at worst one extra
squaring). This leads to the following costs for \\(n\\) successive
doublings:

  - \\(n\\)(2M+5S)+2M+1S (for all curves)
  - \\(n\\)(1M+6S)+4M-1S (if \\(q = 3\bmod 4\\))
  - \\(n\\)(4M+2S)+2M+2S (if \\(q = 3\\) or \\(5\bmod 8\\))
  - \\(n\\)(1M+5S)+3M (if \\(a = 0\\))
  - \\(n\\)(2M+4S)+2M+2S (if \\(a = -1\\) and \\(b = 1/2\\))

The per-sequence overheads are higher than with Jacobian \\((x, w)\\)
coordinates; thus, it is beneficial to organize operations such that
doublings are always performed in long sequences.

Please refer to [the whitepaper](doubleodd.pdf) (section 4.2) for
additional details on these formulas.
