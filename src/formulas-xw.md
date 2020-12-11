# Affine and Jacobian (x, w) Coordinates

In \\((x, w)\\) coordinates, the curve equation becomes:
\\[ w^2 x = x^2 + ax + b \\]
The neutral \\(N\\) does not have a defined affine \\(w\\) coordinate.
For points in \\(\mathbb{G}\\) other than \\(N\\), the \\(w\\)
coordinates is non-zero.

## Encoding and Decoding

Encoding of \\((x, w)\\) is a simple encoding of the \\(w\\) coordinate.

Decoding proceeds as follows:

  - If \\(w = 0\\), then return the point \\(N\\).

  - Solve for \\(x\\) the equation \\(x^2 - (w^2 - a)x + b = 0\\):

      - Compute \\(\Delta = (w^2 - a)^2 - 4b\\) (which is necessarily
        non-zero).
      - If \\(\Delta\\) is not a quadratic residue, then there is no
        solution (the provided encoding is invalid). Otherwise, there
        are two solutions for \\(x\\), depending on which square root
        of \\(\Delta\\) is used:
        \\[ x = \frac{w^2 - a \pm \sqrt{\Delta}}{2} \\]
      - Exactly one of the two solutions is a quadratic residue;
        use the other one.

## Affine Coordinates

Given points \\(P_1 = (x_1, w_1)\\) and \\(P_2 = (x_2, w_2)\\) in
\\(\mathbb{G}\\), their sum in the group \\(P_3 = (x_3, w_3) = P_1 +
P_2\\) is computed with:
\\[\begin{eqnarray*}
    x_3 &=& \frac{b x_1 x_2 (w_1 + w_2)^2}{(x_1 x_2 - b)^2} \\\\
    w_3 &=& -\frac{(w_1 w_2 + a)(x_1 x_2 + b) + 2b(x_1 + x_2)}{(w_1 + w_2)(x_1 x_2 - b)}
\end{eqnarray*}\\]

These formulas are unified: they work properly for all points except when
\\(P_1\\), \\(P_2\\) or \\(P_3\\) is the neutral \\(N\\).

Specialized doubling formulas, to compute the double \\((x', w')\\) of
the point \\((x, w)\\):
\\[\begin{eqnarray*}
    x' &=& \frac{4bw^2}{(2x + a - w^2)^2} \\\\
    w' &=& - \frac{w^4 + (4b-a^2)}{2w(2x + a - w^2)}
\end{eqnarray*}\\]

Again, these formulas are only unified, since \\(N\\) does not have a
defined \\(w\\) coordinate. Note that if the input point \\(P\\) is
not \\(N\\), then its double \\(2P\\) is not \\(N\\) either, since
the group \\(\mathbb{G}\\) has odd order \\(r\\).

## Jacobian Coordinates

Jacobian coordinates represent point \\((x, w)\\) as the triplet
\\((X{:}W{:}Z)\\) with \\(x = X/Z^2\\) and \\(w = W/Z\\). The
point \\(N\\) is represented by \\((0{:}W{:}0)\\) for any \\(W \neq 0\\).
Jacobian coordinates correspond to an isomorphism between curve
\\(E(a, b)\\) and curve \\(E(aZ^2, bZ^4)\\).

The addition of \\(P_1 = (X_1{:}W_1{:}Z_1)\\) with \\(P_2 =
(X_2{:}W_2{:}Z_2)\\) into \\(P_3 = (X_3{:}W_3{:}Z_3)\\) is obtained with
the following:
\\[\begin{eqnarray*}
    X_3 &=& b X_1 X_2 (W_1 Z_2 + W_2 Z_1)^4 \\\\
    W_3 &=& -((W_1 W_2 + a Z_1 Z_2)(X_1 X_2 + b Z_1^2 Z_2^2)
        + 2b Z_1 Z_2 (X_1 Z_2^2 + X_2 Z_1^2)) \\\\
    Z_3 &=& (X_1 X_2 -b Z_1^2 Z_2^2)(W_1 Z_2 + W_2 Z_1)
\end{eqnarray*}\\]
These formulas can be computed with cost 8M+6S. If using
Chudnovsky coordinates \\((X{:}W{:}Z{:}Z^2)\\) (i.e. we also keep around
the value \\(Z^2\\) in addition to \\(Z\\)), then cost is lowered to
8M+5S, but in-memory representation is larger.

Addition formulas are unified, because they do not compute the correct
result when one or both of the operands are equal to \\(N\\). However,
if \\(P_1 \neq N\\) and \\(P_2 = -P_1\\), then a valid representation of
\\(N\\) is computed. Thus, the only exceptional cases that must be
handled in an implementation are \\(P_1 = N\\) and \\(P_2 = N\\). These
two cases can be handled inexpensively with a couple of conditional
copies, to obtain a complete *routine* which induces no tension between
security and performance.

For doubling point \\((X{:}W{:}Z)\\) into \\((X'{:}W'{:}Z')\\), formulas
are:
\\[\begin{eqnarray*}
    X' &=& 16bW^4 Z^4 \\\\
    W' &=& -(W^4 + (4b-a^2)Z^4) \\\\
    Z' &=& 2WZ(2X + aZ^2 - W^2)
\end{eqnarray*}\\]
These formulas are *complete*: they work properly for all inputs, including
the neutral element \\(N\\).

Generic implementation of point doubling is easily done in cost 2M+5S.
If the base field is such that \\(q = 3\bmod 4\\), then \\(4b - a^2\\)
is a square, and, provided that we have \\(e = \sqrt{4b - a^2}\\) and
that the constant \\(e\\) is small, then we can compute \\(W'\\) as
\\((e/2)(2WZ)^2 - (Z^2 + eW^2)^2\\), which leads to an implementation in
cost 1M+6S.

More interestingly, if parameters are such that \\(-a\\) is a square,
and that \\(2b = a^2\\), then we can obtain an implementation of
doubling in cost 2M+4S. Analysis shows that such a situation can happen
only if \\(q = 3\bmod 8\\), and there will be a single curve (up to
isomorphism) that matches these conditions; we can thus assume that
the curve parameters are \\(a = -1\\) and \\(b = 1/2\\). In that case,
the point doubling algorithm becomes the following:

 1. \\(t_1 \leftarrow WZ\\)
 1. \\(t_2 \leftarrow t_1^2\\)
 1. \\(X' \leftarrow 8 t_2^2\\)
 1. \\(t_3 \leftarrow (W + Z)^2 - 2t_1\\)
 1. \\(W' \leftarrow 2t_2 - t_3^2\\)
 1. \\(Z' \leftarrow 2t_1 (2X - t_3)\\)

Successive doublings rely on the definition of some isogenies:
\\[\begin{eqnarray*}
    \psi_\pi : E(a, b) &\longrightarrow& E(-2a\pi^2, \pi^4(a^2-4b))[r] \\\\
    (x, w) &\longmapsto& \left(\pi^2 w^2, -\frac{\pi (x - b/x)}{w}\right)
\end{eqnarray*}\\]
for a constant \\(\pi \neq 0\\) (and defining that the image of the
point-at-infinity and the image of \\(N\\) are both the
point-at-infinity in the target curve). These functions are isogenies on
the curves (that is, using classic point addition) and the image is the
subgroup of points of \\(r\\)-torsion in the destination curve. If two
constants \\(\pi\\) and \\(\pi'\\) are such that \\(2\pi\pi' = 1\\),
then for any \\(P \in E(a, b)\\), \\(\psi_{\pi'}(\psi_{\pi}(P)) = 2P\\).
In other words, composing the two isogenies brings us back to the
original curve, and together they compute a point doubling (on the
curve).

To obtain a similar effect with our group \\(\mathbb{G}\\), we also
define the following functions:
\\[\begin{eqnarray*}
    \theta_\pi : E(a, b) &\longrightarrow& \mathbb{G}(-2a\pi^2, \pi^4(a^2-4b)) \\\\
    (x, w) &\longmapsto& \left(\frac{\pi^2(a^2-4b)}{w^2}, \frac{\pi (x - b/x)}{w}\right)
\end{eqnarray*}\\]
using the notation that \\(\mathbb{G}(a, b)\\) is our prime order group,
defined over a base double-odd elliptic curve with parameters \\(a\\)
and \\(b\\). With these functions, we get that both
\\(\theta_{1/2}(\psi_1(P))\\) and \\(\theta_{1/2}(\theta_1(P))\\)
compute the double (in \\(\mathbb{G}\\)) of point \\(P\\).

We can then compute a succession of doublings by a sequence of
invocations of these functions, freely switching between \\(\psi\\) and
\\(\theta\\) as best suits computations, in order to minimize cost. This
yields complete formulas; the neutral \\(N\\) is represented by
\\((0{:}W{:}0)\\) for any \\(W \neq 0\\), and the point-at-infinity
(which will appear whenever applying a \\(\psi\\) function on the neutral
\\(N\\)) is represented by \\((W^2{:}W{:}0)\\) for any \\(W \neq 0\\).
Using these techniques, we can obtain the following costs for computing
\\(n\\) successive doublings, depending on the base field and the curve
parameters:

  - \\(n\\)(2M+5S) (for all curves)
  - \\(n\\)(1M+6S) (if \\(q = 3\bmod 4\\))
  - \\(n\\)(4M+2S)+1M (if \\(q = 3\\) or \\(5\bmod 8\\))
  - \\(n\\)(1M+5S)+1S (if \\(a = 0\\))
  - \\(n\\)(2M+4S) (if \\(a = -1\\) and \\(b = 1/2\\))

Please refer to [the whitepaper](doubleodd.pdf) (section 4.1) for
additional details on these formulas.
