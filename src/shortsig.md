<style type="text/css">
    .coal img {
        background-color: #E0E0E0;
    }
    .navy img {
        background-color: #E0E0E0;
    }
    .ayu img {
        background-color: #E0E0E0;
    }
</style>
# Short Signatures

Since the Jacobi quartic \\((e,u)\\) coordinates come with an improved
decoding process that changes the on-the-wire encoding of group
elements, thereby breaking backward compatibility with the original
do255e and do255s, we take the opportunity to define an altered
signature scheme. That new scheme yields **shorter signatures** (48
bytes instead of 64), which is important for some applications (e.g. if
the signature must fit in some payload in a cramped QR code). As a nice
side-effect, the change also makes signature verification *faster*,
while not changing the performance of signature generation.

The idea is, in fact, quite old, and should be well-known, since it was
already proposed by Schnorr himself back in 1989, and also proposed
again several times (at least by [Naccache and
Stern](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.58.659)
in 2001, and [Neven, Smart and
Warinschi](https://www.esat.kuleuven.be/cosic/publications/article-1167.pdf)
in 2009). Weirdly enough, there is no standard that leverages it, even
though the benefits are non-negligible. It is applicable to any
Schnorr signature system, not only double-odd curves (but, of course,
double-odd curves work nicely with it, with their very good performance
and compact encoding).

A Schnorr signature scheme can be described as follows:

  - The private key is a scalar \\(d\\). The corresponding public key
    is \\(Q = dG\\), with \\(G\\) begin the conventional generator for
    a prime order group of order \\(r\\).

  - To sign a message \\(m\\), a per-signature secret scalar \\(k\\)
    is generated (uniformly among integers modulo \\(r\\)), then the
    point \\(R = kG\\) is computed (this is the *commitment*). A
    *challenge* value is then computed with a hash function \\(H\\)
    as \\(c = H(R \parallel Q \parallel m)\\) (i.e. we hash the
    commitment, the public key and the message together to get the
    challenge). The challenge is interpreted as an integer, and the
    *response* to that challenge is \\(s = k + cd\\). The signature
    value is nominally the triplet \\((R, c, s)\\).

  - The verification of the signature entails checking that the
    challenge \\(c\\) indeed matches \\(H(R \parallel Q \parallel m)\\),
    and that the equation \\(R = sG - cQ\\) is fulfilled.

Since *verifying* the challenge means *recomputing* it, that value does
not need to be transmitted, leading to a signature containing
exactly the pair \\((R,s)\\). This is exactly how such things are done
in the well-known [EdDSA](https://www.rfc-editor.org/rfc/rfc8032)
scheme. In the case of Ed25519, \\(R\\) and \\(s\\) are encoded over
32 bytes each, for a total signature size of 64 bytes.

We can also choose to omit \\(R\\) instead of \\(c\\): given \\(Q\\),
\\(s\\) and \\(c\\), we can *recompute* \\(R = sG - cQ\\). This leads to
an alternate representation of the signature as the pair \\((c,s)\\).
From this, we can get a gain in size by noticing that \\(c\\) does not
actually *need* to be as large as the group. It can be shorter.

The [Neven-Smart-Warinschi
paper](https://www.esat.kuleuven.be/cosic/publications/article-1167.pdf)
includes some detailed security analysis, which we can summarize as
follows:

  - The security of Schnorr signatures does not depend upon the
    collision resistance of the hash function \\(H\\).

  - There are known proofs of security that reduce the security of
    Schnorr signatures to the discrete logarithm problem in the group
    with some conditions on the size of the group and the size of the
    output of \\(H\\). They show that if you target "\\(n\\) bits of
    security", then you will get them from a group of size \\(2^{3n}\\)
    and a hash function output of \\(2n\\) bits (assuming ideal group
    and hash function).

  - The proofs are not tight; they don't show that attacks are possible
    up to these sizes. In fact, against the best known attacks, the
    \\(n\\)-bit security level is obtained with a \\(2^{2n}\\) group
    (e.g. a 256-bit elliptic curve) and a hash output of only \\(n\\)
    bits.

  - We already disregard the proofs in practice. For instance, Ed25519
    claims "128-bit security" with a 256-bit curve, while the proofs
    would call for a 384-bit curve.

From this analysis, we should get a *practical* security of 128 bits if
we use a 256-bits-or-so elliptic curve (e.g. jq255e or jq255s) and a
128-bit challenge \\(c\\). Combined with the \\((c,s)\\) representation
of signatures, this yields a total signature size of **48 bytes**.

In the fine details of the Neven-Smart-Warinschi analysis, we see that
the hash function must behave quite closely to an ideal hash function,
which rules out "narrow-pipe" designs; in plain words, we get our full
128-bit security only if the hash function \\(H\\) is really a good hash
function with a 256-bit output, that we then truncate to 128 bits. A
standard 256-bit hash function should work; we choose BLAKE2s (which is
secure but also quite fast and maps well to small 32-bit systems).
SHA-256, or even SHA3-256, would also work.

The **speed advantage** comes from the fact that the computation of
\\(R = sG - cQ\\) can be split into:
\\[\begin{equation*}
    R = (s \bmod 2^{128}) G + (\lfloor s/2^{128} \rfloor) (2^{128} G) + c (-Q)
\end{equation*}\\]
i.e. a linear combination of *three* points (two of which being amenable
to precomputations) with half-size (128 bits) coefficients. This yields
better performance than the usual EdDSA signature verification, even if
using the Antipa *et al* optimization with [Lagrange's lattice basis
reduction](https://eprint.iacr.org/2020/454): the latter leads to a
linear combination of *four* points with half-size coefficients (and
adds the cost of Lagrange's algorithm, which we avoid here).

In total, we can get signature verification on jq255e and jq255s to run
in less than 100k cycles on an Intel x86 CPU (Coffee Lake core). The
main advantage of the short signatures is their reduced size, but the
speed gain is nice as well.

It shall be noted that the shorter signature format is not compatible
with batch verification. With EdDSA signatures, one can leverage the
presence of \\(R\\) in the signature to verify many signatures with a
lower per-signature cost; this optimization cannot be applied to the
shorter \\((c,s\\)) signature format. Of course, the point of batch
verification is to reduce the per-verification cost, and that is less
needed if the verification of a single signature is already fast.
