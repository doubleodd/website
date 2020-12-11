# Formulas

We can derive many formulas working in several coordinate systems.
We give in the following pages formulas for:

  - [Affine \\((x, y)\\) coordinates](formulas-xy.md)
  - [Affine and Jacobian \\((x, w)\\) coordinates](formulas-xw.md)
  - [Affine and fractional \\((x, u)\\) coordinates](formulas-xu.md)
  - [x-only point multiplication ladders](formulas-ladder.md)

Proofs for these formulas, as well as extra structures and other
mathematical considerations, are explained at length in the
[whitepaper](doubleodd.pdf).

In all these formulas, the following apply:

  - The base curve \\(E\\) has equation \\(y^2 = x(x^2 + ax + b)\\).
    Group \\(\mathbb{G}\\) consists of the point of \\(E\\) which are
    not points of \\(r\\)-torsion.

  - The neutral is \\(N = (0, 0)\\). The group law is called "addition"
    and denoted as such. Group \\(\mathbb{G}\\) is homomorphic to the
    subgroup of points of \\(r\\)-torsion \\(E[r]\\) and thus has the
    same resistance to discrete logarithm.
