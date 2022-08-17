# Double-Odd Elliptic Curves

Double-odd elliptic curves are a large subset of elliptic curves: these
are curves whose order is equal to 2 modulo 4 (i.e. the order is the
double of an odd integer). On top of double-odd elliptic curves, we can
define a **prime order group** suitable for building cryptographic
protocols. This is similar to what [Ristretto](https://ristretto.group/)
achieves for twisted Edwards curves, albeit with a different method.
Some highlights of double-odd elliptic curves are:

  - There is no cofactor to deal with. A clean prime order group
    abstraction is provided, with a canonical encoding for elements
    which is automatically verified when decoding.

  - The encoding is economical: for \\(n\\)-bit security, group
    elements are encoded over \\(2n+1\\) bits (compared to \\(2n+3\\)
    for Ristretto).

  - All group operations are supported by unified and complete formulas
    amenable to efficient and secure (constant-time) implementations.

  - No drastically new cryptographic hypothesis is introduced.
    Double-odd elliptic curves are fairly mundane; about 1/4th of all
    elliptic curves are double-odd curves. Such curves are considered
    secure for cryptography, just like any other random elliptic curve
    with a large enough subgroup of prime order.

  - The formulas are *fast* and provide performance at least on par with
    twisted Edwards curves. In some respects they are faster; e.g. point
    doublings can be performed, for half of double-odd curves, with six
    multiplications (down to 1M+5S for some specific curves).

In order to leverage these techniques, two specific double-odd elliptic
curves, on which we define the prime-order groups jq255e and jq255s (the
original version of this work defined the do255e and do255s; the "jq"
groups are algebraically identical, but use a different encoding
process). Both provide "128-bit security" (technically, 127 bits);
jq255e is a GLV curve that can leverage an internal endomorphism for
further improved performance, while jq255s is a perfectly ordinary
curve.

(**New: 2022-08-13**) The original do255e and do255s have been
reinterpreted into a subcase of Jacobi quartics, from which we obtain
the new groups jq255e and jq255s (same groups as previously but a
different encoding format). They have **faster formulas** and in
particular a better encoding and decoding (as efficient as plain Ed25519
point compression). On top of jq255e and jq255s, we define **short
signatures** of length 48 bytes, noticeably shorter than the usual
64-byte signatures of Ed25519 or P-256/ECDSA; this also makes signature
verification faster, down to about 93000 cycles on a 64-bit x86 system
(Coffee Lake core).

## Resources

The [original whitepaper](doubleodd.pdf) contains all the formulas and
demonstrations; it also specifies the use of do255e and do255s in
higher-level cryptographic functionalities (key pair generation, key
exchange, signature, and hash-to-curve). The [additional
whitepaper](doubleodd-jq.pdf) presents the reinterpretation of
double-odd curves as Jacobi quartics, leading to the jq255e and jq255s
groups, and new specifications of the higher-level cryptographic
schemes, in particular *short signatures*.

**Note:** both the original whitepaper and the additional whitepapers
are also published on ePrint:

  - [original whitepaper](https://eprint.iacr.org/2020/1558)

  - [additional whitepaper](https://eprint.iacr.org/2022/1052)

Several implementations of jq255e, jq255s, do255e and do255s exist and
are listed in the [implementations section](implementations.md).

## Outline

Subsequent pages in this site include the following:

  - [A geometrical introduction to double-odd elliptic curves](intro.md):
    a self-contained explanatory text (with pictures)
    to explain the motivation and the core ideas of this work.

  - (**New: 2022-08-13**) [Double-odd Jacobi quartics](jacobi.md) presents
    the reinterpretation of double-odd curves as a special case of
    Jacobi quartics, which leads to faster formulas and an improved
    decoding process.

  - (**New: 2022-08-13**) The [short signatures](shortsig.md) describes
    the signature scheme specified on the jq255e and jq255s groups, i.e.
    the double-odd curves interpreted as Jacobi quartics. This scheme
    makes signature substantially shorter (48 bytes instead of 64), and
    also speeds up signature verification.

  - The [formulas section](formulas.md) lists useful formulas for
    computing over points of double-odd elliptic curves with several
    convenient systems of coordinates.

  - Explicit [curve choices](curves.md) are listed: these are curves
    do255e/jq255e and do255s/jq255s. Selection criteria and curve
    parameters are provided.

  - Some [benchmarks](benchmarks.md) on large (x86) and small (ARM
    Cortex M0+ and M4) architectures are detailed.

  - Available [open-source implementations](implementations.md) are
    listed.

## About

Double-odd elliptic curves were designed by Thomas Pornin, who also
wrote this Web site, with contributions from Paul Bottinelli, Gérald
Doussot and Eric Schorn.
