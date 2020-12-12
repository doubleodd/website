# Implementations

The following open-source (MIT license) implementations of curves
do255e and do255s are available:

  - **Reference implementation** in Python:

    [https://github.com/doubleodd/py-do255](https://github.com/doubleodd/py-do255)

    This implementation is only for reference purposes; it computes
    correct values, but does so in a sub-optimal way and, crucially,
    is not free of timing-based side channels, since it relies on
    Python's big integers.

    This repository includes comprehensive test vectors for curves
    do255e and do255s (both low-level operations such as point addition,
    and high-level functionalities: key exchange, signature,
    hash-to-curve). The script that generates these vectors leverages
    the Python implementation.

  - **Go implementation**:

    [https://github.com/doubleodd/go-do255](https://github.com/doubleodd/go-do255)

    The Go implementation is considered correct and secure; all the code
    is strictly constant-time. It is pure Go code that should run on any
    supported Go platform. It internally uses 64-bit types with
    `math/bits` functionalities (especially `Add64()`, `Sub64()` and
    `Mul64()`) which provide decent performance in a portable way on
    64-bit platforms.

  - **C and assembly implementations**:

    [https://github.com/doubleodd/c-do255](https://github.com/doubleodd/c-do255)

    This repository contains some extensive demonstration code in C and
    assembly:

      - C code (with 64-bit intrinsics) for 64-bit x86
      - C code with some inline assembly for 64-bit x86 with BMI2 and ADX
        opcodes (e.g. Intel Skylake and later cores)
      - C code (with 32-bit intrinsics) for 32-bit x86
      - Assmbly code for ARM Cortex M0+

    While this implementation was written for research and demonstration
    purposes, it follows APIs and conventions appropriate for general
    inclusion in applications. All the code is strictly constant-time.
    This is the code that was used for the benchmarks reported in
    the [whitepaper](doubleodd.pdf).
