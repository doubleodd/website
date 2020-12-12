# Curve Choices

We define two specific sets of curve parameters for double-odd curves
that leverage the formulas for good performance: do255e and do255s. The
do255e curve is a GLV curve (with equation \\(y^2 = x(x^2 + b)\\)) which
permits use of our doubling formulas with the lowest per-doubling cost
(1M+5S). GLV curves feature some extra structure, namely a fast
endomorphism on the curve that can be leveraged to considerably speed up
point multiplication. Since this extra structure has historically
generated unease about security (no weakness was found, but these curves
are still admittedly "special" in a fuzzy way), we also define do255s,
an ordinary curve with no such structure. do255s uses the equation
\\(y^2 = x(x^2 - x + 1/2)\\), for which we have doubling formulas in
cost 2M+4S.

In both cases, the base field is chosen to be the field of integers
modulo \\(q = 2^{255} - m\\) for a small integer \\(m\\); the value of
\\(m\\) is the main selection parameter. Fastest known implementations
on both 64-bit x86 CPUs and ARM Cortex M0+ CPUs use register-sized limbs
and will provide the same performance for all values of \\(m\\), as long
as \\(m < 2^{15}\\). Moreover, there are some slight benefits, when
doing modular reduction, to a 255-bit modulus (as opposed to a 256-bit
modulus), because that allows an efficient partial reduction. Since the
curve order is close to \\(q\\) and that order is \\(2r\\), where
\\(r\\) is the order of the final group, we will obtain a group of size
about \\(2^{254}\\), which is sufficient to claim "128-bit security" (in
the same way that Curve25519 / Edwards25519 also claims the 128-bit
security level with a subgroup prime order of about \\(2^{252}\\)).

We thus choose the target values of \\(a\\) and \\(b\\) such that all
constants in applicable formulas lead to fast operations, then explore
increasing values of \\(m\\) until a match is found, i.e.
\\(q = 2^{255} - m\\) is prime *and* the curve equation leads to order
\\(2r\\) with \\(r\\) prime.

Once the field is chosen, it only remains to define a conventional
generator \\(G\\) for the group. Which point we use is not important for
security (discrete logarithm relatively to one generator is equivalent
to discrete logarithm relatively to any other generator). To make the
selection deterministic, we use the point in the group \\(\mathbb{G}\\)
with the lowest non-zero \\(u\\) coordinate, interpreted as an integer
in the \\(0...q-1\\) range.

Specific parameters are provided in the following sub-pages:

  - [do255e](params-do255e.md)
  - [do255s](params-do255s.md)
