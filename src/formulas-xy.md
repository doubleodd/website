# Affine (x, y) Coordinates

Given points \\(P_1 = (x_1, y_1)\\) and \\(P_2 = (x_2, y_2)\\) in
\\(\mathbb{G}\\), their sum in the group \\(P_3 = (x_3, y_3) = P_1 +
P_2\\) is computed with:
\\[\begin{eqnarray*}
    x_3 &=& \frac{b((x_1 + x_2)(x_1 x_2 + b) + 2a x_1 x_2 + 2 y_1 y_2)}{(x_1 x_2 - b)^2} \\\\
    y_3 &=& \frac{b(2a(x_1 y_2 + x_2 y_1)(x_1 x_2 + b) + (x_1^2 y_2 + x_2^2 y_1)(x_1 x_2 + 3b) + (y_1 + y_2)(3b x_1 x_2 + b^2))}{-(x_1 x_2 - b)^3}
\end{eqnarray*}\\]

These formulas are complete. Denominators can never be zero (because
\\(x_1 x_2\\) is a quadratic residue, but \\(b\\) is not).
