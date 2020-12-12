# x-only Ladders

A ladder algorithm working only over the \\(x\\) coordinates of points
can be used to compute Diffie-Hellman key exchanges over double-odd
elliptic curves. Such ladders are already known, but our formulas are
a bit faster than what was previously published for such curves.

Suppose that we are computing a multiplication of point \\(P_0\\) whose
\\(x\\) coordinate is known as a field element \\(x_0\\). At any point
in a double-and-add algorithm, our current accumulator point is
represented as the \\(x\\) coordinates of two points \\(P_1\\) and
\\(P_2\\) which are such that \\(P_2 = P_0 + P_1\\) (addition in the
group \\(\mathbb{G}\\)). These \\(x\\) coordinates are known as
fractions, i.e. \\(x_1 = X_1/Z_1\\) and \\(x_2 = X_2/Z_2\\).
We can then compute the \\(x_3 = X_3/Z_3\\) coordinate of
\\(P_3 = P_1 + P_2\\) with:
\\[\begin{eqnarray*}
    X_3 &=& b (X_1 Z_2 - X_2 Z_1)^2 \\\\
    Z_3 &=& x_0 (X_1 X_2 - b Z_2 Z_1)^2
\end{eqnarray*}\\]
with a cost of 4M+2S.

For doubling either \\(P_1\\) or \\(P_2\\),
we observe that when applying the isogeny \\(\theta_1\\) on a point
\\(P\\) whose \\(x\\) coordinate is \\(X/Z\\), to obtain a point
\\(P'\\) with \\(x' = X'/Z'\\) on the dual curve, we can compute
\\(X'\\) and \\(Z'\\) as:
\\[\begin{eqnarray*}
    X' &=& (a^2 - 4b) XZ \\\\
    Z' &=& X^2 + aXZ + bZ^2 \\\\
       &=& (X + Z)(X + bZ) - (a - 1 - b)XZ
\end{eqnarray*}\\]
with a cost of 2M. We can similarly compute \\(\theta_{1/2}\\) on that
point \\(P'\\) with cost 2M, and that yields the \\(x\\) coordinate of
\\(2P\\). In total, we can have the \\(x\\) coordinate of \\(2P\\),
starting from the \\(x\\) cordinate of \\(P\\), in cost 4M.

Each ladder step combines the two operations above: we replace
\\((P_1, P_2)\\) with either \\((2P_1, P_1+P_2)\\) (if the scalar bit
is 0) or with \\((P_1+P_2, 2P_2)\\) (if the scalar bit is 1). Either
way, the cost per scalar bit is 8M+2S. This is not as efficient as
using Jacobian \\((x, w)\\) or fractional \\((x, u)\\) coordinates,
but it allows implementation of key exchange with less code, and
in a more RAM-efficient way, compared with double-and-add algorithms
with window optimizations.

Since we encode points as their \\(w\\) coordinate, not \\(x\\)
coordinate, it is convenient to define the Diffie-Hellman key algorithm
to output, as shared secret, the value \\(w^2\\), for the
\\(w\\)-coordinate of the resulting point. Indeed, with the
\\(\theta_1\\) isogeny, the \\(x\\) coordinate of \\(\theta_1(x, w)\\)
is equal to \\((a^2-4b)/w^2\\); thus, we can make a \\(w^2\\)-only
ladder on curve \\(E(a,b)\\) by simply using an \\(x\\)-only ladder
(with the formulas shown above) on the dual curve \\(E(-2a, a^2-4b)\\).
In that way, a Diffie-Hellman key exchange (multiplication of the point
from the peer by the local private key) can be implemented with a ladder
algorithm *without* requiring full point decoding. This saves a square
root computation. Namely, when the peer's point is received as value
\\(w\\), it suffices to verify that \\(w \neq 0\\) and that \\((w^2 -
a)^2 - 4b\\) is a quadratic residue (with a Legendre symbol), thereby
validating that the value \\(w\\) is correct and *could* be decoded into
a full point, but it is not necessary to actually decode it and retrieve
the \\(x\\) coordinate, which is not needed for a \\(w^2\\)-only ladder.
