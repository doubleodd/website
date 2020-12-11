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
    with a large enough subgroup with a prime order.

  - The formulas are *fast* and provide performance at least on par with
    twisted Edwards curves. In some respects they are faster; e.g. point
    doublings can be performed, for half of double-odd curves, with six
    multiplications (down to 1M+5S for some specific curves).

In order to leverage these techniques, two specific double-odd elliptic
curves are defined: do255e and do255s. Both provide "128-bit security"
(technically, 127 bits); do255e is a GLV curve that can leverage an
internal endomorphism for further improved performance, while do255s is
a perfectly ordinary curve.

## Resources

The [whitepaper](doubleodd.pdf) contains all the formulas and
demonstrations; it also specifies the use of do255e and do255s in
higher-level cryptographic functionalities (key pair generation, key
exchange, signature, and hash-to-curve). Most of the present site
consists in extracts from this whitepaper.

Several implementations of do255e and do255s exist and are listed
in the [implementations section](implementations.md).

## Outline

Subsequent pages in this site include the following:

  - [A geometrical introduction to double-odd elliptic curves](intro.md):
    a self-contained explanatory text (with pictures)
    to explain the motivation and the core ideas of this work.

  - The [formulas section](formulas.md) lists useful formulas for
    computing over points of double-odd elliptic curves with several
    convenient systems of coordinates.

  - Explicit [curve choices](curves.md) are listed: these are curves
    do255e and do255s. Selection criteria and curve parameters are
    provided.

  - Available [open-source implementations](implementations.md) are
    listed.

## About

Double-odd elliptic curves were designed by Thomas Pornin, who also
wrote this Web site, with contributions from Paul Bottinelli, Gérald
Doussot and Eric Schorn.
