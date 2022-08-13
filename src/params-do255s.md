# do255s / jq255s

The curve do255s is an ordinary curve with no easily computable
endomorphism. Its equation parameters are such that \\(a\\) is not a
quadratic residue, and \\(a^2 = 2b\\), so that point doubling formulas
in cost 2M+4S (in Jacobian \\((x, w)\\) coordinates) may be used. As
exposed in the [whitepaper](doubleodd.pdf), this implies that the curve
\\(j\\)-invariant is 128, and that there is only a single curve per
field (up to isomorphisms) that matches these criteria, and we can thus
enforce that \\(a = -1\\) and \\(b = 1/2\\). The corresponding Jacobi
quartic, called jq255s, then has equation \\(e^2 = -u^4 + 2u^2 + 1\\).

We apply the following criteria:

  - Curve equation is \\(y^2 = x(x^2 - x + 1/2)\\).

  - Modulus \\(q = 2^{255} - m\\) should be equal to 3 modulo 8. This
    is needed for the curve with that equation to be a double-odd curve.

  - Curve order must be equal to \\(2r\\) for a prime integer \\(r\\).

Under these criteria, the first match is for \\(m = 3957\\). Here are
the resulting curve parameters:

  - **Name:** do255s / jq255s
  - **Field:** integers modulo \\(q = 2^{255} - 3957\\)
  - **Equations:** \\(y^2 = x(x^2 - x + 1/2)\\) and \\(e^2 = -u^4 + 2u^2 + 1\\)
  - **Order:** \\(2r\\), with \\(r = 2^{254} + 56904135270672826811114353017034461895\\)
  - **Generator:**
    \\[\begin{eqnarray*}
        G_x &=& 26116555989003923291153849381583511726884321626891190016751861153053671511729 \\\\
        G_y &=& 28004200202554007000979780628642488551173104653237157345493551052336745442580 \\\\
        G_e &=& 6929650852805837546485348833751579670837850621479164143703164723313568683024 \\\\
        G_u &=& 3
    \end{eqnarray*}\\]
