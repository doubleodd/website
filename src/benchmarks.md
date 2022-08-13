# Benchmarks

We list here some speed benchmarks of double-odd curves.

## Double-Odd Jacobi Quartics

The main implementation for jq255e and jq255s are in Rust, as part of
the [crrl library](https://github.com/pornin/crrl); since that library
also implements Ed25519, this allows comparing the relative performance
of the curves. On an Intel x86 i5-8259U (Coffee Lake core), with Rust
1.59.0, compiled with the "`-C target-cpu=native`" flags, the following
values are obtained (in clock cycles):

| Operation     |       jq255e |       jq255s |      Ed25519 |
| :------------ | -----------: | -----------: | -----------: |
| Decode point  |         9371 |         9295 |         9491 |
| Encode point  |         7847 |         7874 |         7864 |
| Point mul     |        72080 |       107875 |       107730 |
| Point mulgen  |        43570 |        44915 |        40456 |
| Sign          |        54738 |        56160 |        51581 |
| Verify        |        82839 |        86837 |       113983 |

These are the measured operations:

  - *Decode point*: read 32 bytes, and decode these into a curve point.
    This includes validation that the point is correctly on the curve.
    Main operation is a square root computation (a merged square root and
    inversion for Ed25519).

  - *Encode point*: encode a point into 32 bytes. The point is the
    output of some computations, i.e. not necessarily in already
    normalized affine coordinates. Main operation is an inversion
    in the field. Note: for Ristretto255, this operation is somewhat
    more expensive, at about 9800 cycles.

  - *Point mul*: multiply a point by a given scalar. An ECDH key
    exchange will primarily use this operation, as well as a point
    decode (on the peer's public key) and a point encode (on the
    resulting shared secret point).

  - *Point mulgen*: multiply the conventional generator (base point) by
    a scalar. In all three curves, the implementation uses four
    precomputed tables, each containing 16 points in extended affine
    coordinates, for a total of 6144 bytes. Some minor speed-ups could
    be achieved with more or larger tables.

  - *Sign*: sign some data. This assumes that the signer's private key
    has already been loaded in RAM, along with the encoding of the
    signer's public key (if not stored along, then the public key can
    be recomputed from the private key, at the cost of one mulgen
    and one encode operations).

  - *Verify*: verify a signature. This assumes that the signer's public
    key has already been decoded into a point; add the cost of a point
    decode operation to get the total verification cost using a
    32-byte-encoded public key.

All these operations are implemented in pure constant-time code, except
signature verification, which is assumed to operate only on public data.

**Commentary:** we see that double-odd curve are on par with Ed25519 for
most operations (Ristretto offers the same performance as Ed25519,
except for point encoding, which is slightly slower since it involves a
square root operation). Ed25519 is slightly better for operations that
use mostly general point additions, while jq255e and jq255s are slightly
faster for point doublings. Jq255e, specifically, benefits from the
GLV endomorphism for computing general point multiplications by a scalar.
Signature verification on jq255e and jq255s is quite faster than on
Ed25519 thanks to the use of [short signatures](shortsig.md).

## Original Double-Odd Curves Benchmarks

The [original whitepaper](whitepaper.pdf) defined the do255e and do255s
groups, which were implemented in C (with assembly) on x86 (Coffee
Lake), ARM Cortex M0+ and ARM Cortex M4 systems. These implementations
are published on:
[https://github.com/doubleodd/c-do255](https://github.com/doubleodd/c-do255)

All values are expressed in clock cycles on their respective platforms.
Take care that the described operations are slightly distinct from the
one uised above; in particular, the "verify (with pubdec)" operation
combines the decoding of the public key and the actual signature
verification.

### Results

For **do255e**, the following timings are obtained (X25519 values listed
for reference):

| Operation            | x86 (64-bit) | ARM Cortex M0+ | ARM Cortex M4 |
| :------------------- | -----------: | -------------: | ------------: |
| keygen               |        47974 |        1422257 |        270235 |
| key exchange         |       100834 |        2616600 |        492927 |
| sign                 |        52324 |        1502127 |        316991 |
| verify (with pubdec) |       110226 |        3255070 |        558732 |
| mul                  |        75296 |        2192130 |        385869 |
| mulgen               |        41110 |        1363789 |        243046 |
| X25519               |        95437 |        3229983 |        563310 |

Curve do255e benefits from the efficient endomorphism. Curve **do255s**
does not, yielding somewhat larger costs:

| Operation            | x86 (64-bit) | ARM Cortex M0+ | ARM Cortex M4 |
| :------------------- | -----------: | -------------: | ------------: |
| keygen               |        52272 |        1572978 |        298080 |
| key exchange         |       141992 |        3591395 |        654557 |
| sign                 |        56806 |        1653043 |        344930 |
| verify (with pubdec) |       158780 |        3746889 |        700994 |
| mul                  |       116702 |        3173254 |        548706 |
| mulgen               |        45456 |        1514461 |        270870 |
| X25519               |        95437 |        3229983 |        563310 |

**Commentary:** while the main benefit of double-odd curves is that they
provide a prime-order group with canonical encodings and decodings,
thereby avoiding all cofactor issues, the achieved performance is also
quite decent. On embedded systems (represented here by the M0+ and the
M4), curve do255e consistently outperforms X25519, while at the same
time being more versatile since it inherently supports the usual panoply
of group-related functionalities (Diffie-Hellman key exchange, Schnorr
signatures).

### Operations

The measured operations are the following:

  - **keygen**: production of a new public/private key pair;

  - **key exchange**: ECDH key exchange (processing of a received public
    key from the peer with the local private key, and derivation of a
    shared key);

  - **sign**: signature generation over a given message;

  - **verify**: verification of a signature.

These operations include all encoding and decoding steps; in particular,
public keys use the defined 32-byte format, which is akin to point
compression and involves a square root computation and a Legendre symbol
when decoding. For key pair generation, a random "seed" is provided as
an initialized SHAKE context, ready to output bytes (in practice, this
is equivalent to saying that the 32-byte private key is provided, and
the cost is that of computing the public key and encoding it). For key
exchange, the costs of decoding the peer's public key, and deriving the
computed point into a shared key (of length 32 bytes) are included. For
signature generation and verification, a short message (or hash value
thereof) is provided to the function. Signature verification operates
over the encoded public key and thus includes the cost of decoding the
public key.

Signature verification time is intrinsically variable; all reported
times for that operation are averages.

For comparison purposes, the costs of point multiplication alone are also
provided:

  - **mul**: multiplication of an arbitrary point by a scalar;

  - **mulgen**: multiplication of the conventional generator by a scalar.

In both cases, input and output points use an in-memory decoded format
(in Jacobian coordinates).

Some X25519 timings are also provided. For the ARM Cortex M0+ and M4, we
measured the best reported opensource implementations (on the
[M0+](https://github.com/pornin/x25519-cm0) and on the
[M4](https://github.com/Emill/X25519-Cortex-M4)) on our test hardware.
For x86, we use the figures reported by Nath and Sarkar in
[https://eprint.iacr.org/2020/378](https://eprint.iacr.org/2020/378)
(table 2, for variable base scalar multiplication). Note that "key
exchange" on double-odd curves does arguably strictly more than X25519,
since it reliably reports whether the peer's point was valid (on the
right curve) or not, while X25519 does not yield that information.

## Platforms

### x86 (64-bit)

The x86 platform is an Intel Core i5-8295U, clocked at 2.3 GHz.
Operating system is a regular Linux (Ubuntu 20.04). Frequency upscaling
("TurboBoost") has been disabled, to ensure more consistent measures.
Compiler is Clang 10.0.0; compilation flages are `-O2 -march=skylake`.
Most of the implementation is written in C; inline assembly is used for
the multiplications and squarings in the field, as well as the inner
loops of inversion (optimized binary GCD) and Legendre symbol
computations. Internal representation of a field element is over four
limbs of 64 bits each; the `mulx`, `adcx` and `adox` opcodes are used.
There is no attempt at using AVX2 explicitly (but the compiler might
use it when translating C code).

Clock cycle counts are obtained through the `rdtsc` opcode (using
performance counters yields identical results). The measured operation
is executed 2000 times; the first 1000 are considered to be "warm-up",
then the median execution time of the last 1000 is returned.

### ARM Cortex M0+

Test system is an Atmel (now Microchip) SAMD20 Xplained Pro board, using
an ATSAMD20J18 microcontroller. The board was clocked at 8 MHz, allowing
read access to the Flash memory without any wait state. Elapsed time is
measured with one of the microcontroller counters, configured to use the
CPU clock as source; that counter is thus a cycle counter. All
interrupts are disabled. Measures have 1-cycle accuracy and are
reproducible; they also exactly match timings inferred from manual
counting of executed opcodes.

Most the M0+ implementation is written in assembly. Some non-time
critical parts are written in C; C compiler is GCC 9.2.1 with flags
`-Os -mthumb -mlong-calls -mcpu=cortex-m0plus`. Internal representation
of a field element is eight limbs of 32 bits each. RAM usage (stack)
has been kept under 2 kB.

### ARM Cortex M4

The M4 test system is an STM32F4DISCOVERY board, featuring an STM32F407
microcontroller. The board was clocked at 24 MHz to ensure Flash access
with no wait state.

RAM configuration must be given some attention. While the board has
192 kB of RAM, one third of it (64 kB) is called the CCM (closely
coupled memory). The CCM is linked directly to the CPU, to ensure the
most efficient access; however, access to the rest of the RAM goes
through the interconnection matrix that regulates memory traffic between
the CPU, Flash, and peripherals. That matrix tries to allow accesses to
RAM (for data) and to Flash (for code) to proceed concurrently, but is
not always successful. Thus, if the main RAM is not in CCM, then some
slowdown is observed (about 10 to 15%), probably because of access
conflicts between data and code within the matrix. In order to avoid
this cost, the main RAM (in particular, the data stack) has been placed
in the CCM.

The STM32F407 also has a prefetching mechanism for instructions, and
caches (separate instruction and data caches). These features apply
*only* to accesses to the Flash, not to the RAM (the caches sit between
the interconnection matrix and the Flash, and not closer to the CPU).
They can be programmatically enabled or disabled; experimentally, in our
setup, enabling prefetching and/or caches does not change the timings,
which is consistent with the idea that at 24 MHz, Flash access does not
have wait states.

When the X25519 implementation from Emil Lenngren is measured, we
obtain a cost of 563310 cycles, which is slightly above the reported
figure of 548873 cycles. This may indicate that there is some remaining
contention somewhere in the overall system.

The M4 implementation is very similar to the M0+ implementation; it uses
the same C files, but all the assembly parts have been rewritten to
leverage the abilities of the ARMv7-M opcodes. The same C compiler and
flags are used. RAM usage (stack) has similarly been kept under 2 kB.
