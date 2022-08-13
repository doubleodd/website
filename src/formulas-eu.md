# Affine and Extended (e, u) Coordinates

As described in the [Double-Odd Curves as Jacobi Quartics](jacobi.md)
section, double-odd curves can be turned into Jacobi quartics with
the following change of variables:
\\[\begin{eqnarray*}
    u &=& \frac{x}{y} \\\\
    e &=& u^2 \left(x - \frac{b}{x}\right)
\end{eqnarray*}\\]

In this coordinate system, the curve equation is
\\(e^2 = (a^2-4b)u^4 - 2au^2 + 1\\). The point of order two is
\\(N = (-1,0)\\), while the point "at infinity" has definite coordinates
\\(\mathbb{O} = (1,0)\\). Several formulas are known for such curves.

## Affine Coordinates

Given \\(P_1 = (e_1, u_1)\\) and \\(P_2 = (e_2, u_2)\\), their sum
\\(P_3 = (e_3, u_3)\\) can be computed with the following formulas:
\\[\begin{eqnarray*}
    e_3 &=& \frac{(1 + (a^2-4b) u_1^2 u_2^2)(e_1 e_2 - 2a u_1 u_2)
        + 2(a^2-4b) u_1 u_2 (u_1^2 + u_2^2)}
        {(1 - (a^2-4b) u_1^2 u_2^2)^2} \\\\
    u_3 &=& \frac{u_1 e2 + u_2 e_1}{1 - (a^2-4b) u_1^2 u_2^2}
\end{eqnarray*}\\]

These formulas are **complete** (they are complete for Jacobi quartics
with a single point of order 2, a category that includes all double-odd
curves).

## Decoding and Encoding

The encoding format for double-odd Jacobi quartic curves requires the
definition of a *sign convention*, which is a function \\(S\\) over
field elements, such that:

  - For all field elements \\(x\\), \\(S(x) = 0\\) or \\(1\\).
  - \\(S(0) = 0\\).
  - For any \\(x \neq 0\\), \\(S(-x) = 1 - S(x)\\).

Elements \\(x\\) for which \\(S(x) = 1\\) are said to be *negative*; the
other elements are called *non-negative*. For our fields of interest
(integers modulo a prime integer \\(q\\)), we use the least significant
bit of the integer representation of the field element (in the \\(0\\)
to \\(q-1\\) range).

The **encoding process** of point \\((e,u)\\) is then the following:

  - If \\(e\\) is non-negative, encode \\(u\\) as bytes.
  - Otherwise, encode \\(-u\\) as bytes.

The **decoding process** works as follows:

  - Decode the input bytes as the \\(u\\) coordinate.
  - Compute \\(e^2 = (a^2-4b)u^4 - 2au^2 + 1\\).
  - Extract \\(e\\) as a square root of \\(e^2\\). If the encoding is valid,
    then there will be two square roots, exactly one of which being
    non-negative: choose that specific root as \\(e\\).

The encoding/decoding process does *not* need any special treatment for
any group element, including the neutral.

## Extended Coordinates

Extended coordinates represent a point \\((e,u)\\) as a quadruplet
\\((E{:}Z{:}U{:}T)\\) such that \\(Z\neq 0\\), \\(e = E/Z\\), \\(u = U/Z\\)
and \\(u^2 = T/Z\\) (this implies that \\(U^2 = TZ\\)).

The addition of \\(P_1 = (E_1{:}Z_1{:}U_1{:}T_1)\\) with \\(P_2 =
(E_2{:}Z_2{:}U_2{:}T_2)\\) into \\(P_3 = (E_3{:}Z_3{:}U_3{:}T_3)\\) is
obtained with the following addition formulas:
\\[\begin{eqnarray*}
    E_3 &=& (Z_1 Z_2 + (a^2-4b) T_1 T_2)(E_1 E_2 - 2a U_1 U_2) + 2(a^2-4b) U_1 U_2 (T_1 Z_2 + T_2 Z_1) \\\\
    Z_3 &=& (Z_1 Z_2 - (a^2-4b) T_1 T_2)^2 \\\\
    U_3 &=& (E_1 U_2 + E_2 U_1)(Z_1 Z_2 - (a^2-4b) T_1 T_2) \\\\
    T_3 &=& (E_1 U_2 + E_2 U_1)^2
\end{eqnarray*}\\]
Like their affine counterparts, these formulas are complete.

They can be implemented in cost 8M+3S by using the following algorithm:
 1. \\(n_1 \leftarrow E_1 E_2\\)
 1. \\(n_2 \leftarrow Z_1 Z_2\\)
 1. \\(n_3 \leftarrow U_1 U_2\\)
 1. \\(n_4 \leftarrow T_1 T_2\\)
 1. \\(n_5 \leftarrow (Z_1 + T_1)(Z_2 + T_2) - n_2 - n_4\\)
 1. \\(n_6 \leftarrow (E_1 + U_1)(E_2 + U_2) - n_1 - n_3\\)
 1. \\(n_7 \leftarrow n_2 - (a^2-4b) n_4\\)
 1. \\(E_3 \leftarrow (n_2 + (a^2-4b) n_4)(n_1 - 2a n_3) + 2(a^2-4b) n_3 n_5\\)
 1. \\(Z_3 \leftarrow n_7^2\\)
 1. \\(T_3 \leftarrow n_6^2\\)
 1. \\(Z_3 \leftarrow ((n_6 + n_7)^2 - n_6 - n_7)/2\\)

The cost of 8M+3S assumes that multiplication by constants \\(2a\\) and
\\(a^2-4b\\) is inexpensive. i.e. that these are all small integers or
easily computed fractions such as \\(1/2\\).

While the general addition formulas work also for point doublings
(with cost 2M+9S), we can do better. The following formulas compute
the double of a point, yielding the result in Jacobian \\((x,w)\\)
coordinates (they also work with \\(N\\) and \\(\mathbb{O}\\), even
though in general \\((x,w)\\) coordinates do not support these points):
\\[\begin{eqnarray*}
    X' &=& E^4 \\\\
    W' &=& 2Z^2 - 2aU^2 - E^2 \\\\
       &=& Z^2 - (a^2-4b) T^2 \\\\
       &=& E^2 + 2aU^2 - 2(a^2-4b) T^2 \\\\
    J' &=& 2EU
\end{eqnarray*}\\]
(There are three possible choices to compute \\(W'\\)). This operation
can be done in cost 2M+2S, or 1M+3S when the field is such that
\\(q = 3 \bmod 4\\) (because, in that case, \\(4b-a^2\\) is a square
and we can use its square root to transform a multiplication into
a squaring). The result is in Jacobian \\((x,w)\\) coordinates, and
must be transformed back into extended \\((e,u)\\) coordinates, with:
\\[\begin{eqnarray*}
    Z' &=& W'^2 \\\\
    T' &=& J'^2 \\\\
    U' &=& J'W' \\\\
    E' &=& 2X' - Z' + aT'
\end{eqnarray*}\\]
which can be computed with cost 3S.

By combining these two operations, we get point doublings in cost
2M+5S, or 1M+6S when the field is such that \\(q = 3 \bmod 4\\).

Since we have the choice of representant for each group element, we
can compute \\(2P+N\\) instead of \\(2P\\); in that case, the formulas
for the doubling to \\((x,w)\\) Jacobian coordinates are as follows:
\\[\begin{eqnarray*}
    X' &=& 16b U^4 \\\\
       &=& 16b (TZ)^2 & & \\\\
    W' &=& E^2 + 2aU^2 - 2Z^2 \\\\
       &=& (a^2-4b) T^2 - Z^2 \\\\
       &=& 2(a^2-4b) T^2 - E^2 - 2aU^2 \\\\
    J' &=& 2EU
\end{eqnarray*}\\]
We again get several choices, both for \\(X'\\) and for \\(W'\\). These
formulas are convenient for the case of curves with \\(a = 0\\)
(such as jq255e), because they allow a cost of 1M+3S (for a total of
1M+6S for the complete doubling) even if the field is such that
\\(q = 1 \bmod 4\\).

Since we transiently use Jacobian \\((x,w)\\) coordinates, we can insert
extra doublings using that coordinate system, before the conversion back
to extended \\((e,u)\\) coordinates. That way, we can leverage the same
asymptotic cost for long sequences of point doublings as in the
\\((x,u)\\) coordinates, with a lower overhead when starting the
sequence:

  - \\(n\\)(2M+5S) (for all curves)
  - \\(n\\)(1M+6S) (if \\(q = 3\bmod 4\\))
  - \\(n\\)(1M+5S)+1S (if \\(a = 0\\))
  - \\(n\\)(2M+4S)+2S-1M (if \\(a = -1\\) and \\(b = 1/2\\))

Please refer to [the additional whitepaper](doubleodd-jq.pdf) (sections
6.5 to 6.7) for additional details on these formulas.
