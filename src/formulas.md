# Formulas

We can derive many formulas working in several coordinate systems.
We give in the following pages formulas for:

  - [Affine \\((x, y)\\) coordinates](formulas-xy.md)
  - [Affine and Jacobian \\((x, w)\\) coordinates](formulas-xw.md)
  - [Affine and fractional \\((x, u)\\) coordinates](formulas-xu.md)
  - [x-only point multiplication ladders](formulas-ladder.md)
  - [Affine and extended \\((e, u)\\) coordinates](formulas-eu.md)

Proofs for these formulas, as well as extra structures and other
mathematical considerations, are explained at length in the
[whitepaper](doubleodd.pdf). The \\((e,u)\\) coordinates relate to
the view of the curve as a Jacobi quartic, as explained in the
[relevant paper](doubleodd-jq.pdf).

In all these formulas, the following apply:

  - The base curve \\(E\\) has equation \\(y^2 = x(x^2 + ax + b)\\).
    Group \\(\mathbb{G}\\) consists of the point of \\(E\\) which are
    not points of \\(r\\)-torsion.

  - The neutral is \\(N = (0, 0)\\). The group law is called "addition"
    and denoted as such. Group \\(\mathbb{G}\\) is homomorphic to the
    subgroup of points of \\(r\\)-torsion \\(E[r]\\) and thus has the
    same resistance to discrete logarithm.

  - In \\((e, u)\\) coordinates, the equation is
    \\(e^2 = (a^2-4b)u^4 - 2au^2 + 1\\). All curve points can now be
    used (not just \\(r\\)-torsion points). The neutral element of
    the group is represented by either \\(N = (-1,0)\\) or
    \\(\mathbb{O} = (1,0)\\) (the "point-at-infinity", which is not
    actually "at infinity" in this coordinate system).
