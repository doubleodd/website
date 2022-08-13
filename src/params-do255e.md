# do255e / jq255e

The curve do255e is a GLV curve, i.e. with equation \\(y^2 = x(x^2 + b)\\).
Its Jacobi quartic counterpart, jq255e, has equation \\(e^2 = -4bu^4 + 1\\).
We apply the following criteria:

  - Modulus \\(q = 2^{255} - m\\) should be equal to 5 modulo 8. For the
    GLV curve not to be supersingular, we need \\(q\\) to be equal to
    1 or 5 modulo 8; the second choice makes computation of square roots
    in the field easier to implement.

  - Since the \\(j\\)-invariant of such a curve is fixed (it's 1728),
    there are only two potential curves (up to isomorphisms) to check
    for a given field. We can thus always enforce that \\(b = 2\\) or
    \\(-2\\), which are convenient values for implementation.

  - Curve order must be equal to \\(2r\\) for a prime integer \\(r\\).

Under these criteria, the first match is for \\(m = 18651\\). Here are
the resulting curve parameters:

  - **Name:** do255e / jq255e
  - **Field:** integers modulo \\(q = 2^{255} - 18651\\)
  - **Equations:** \\(y^2 = x(x^2 - 2)\\) and \\(e^2 = 8u^4 + 1\\)
  - **Order:** \\(2r\\), with \\(r = 2^{254} - 131528281291764213006042413802501683931\\)
  - **Generator:**
    \\[\begin{eqnarray*}
        G_x &=& 2 \\\\
        G_y &=& 2 \\\\
        G_e &=& 3 \\\\
        G_u &=& 1
    \end{eqnarray*}\\]
