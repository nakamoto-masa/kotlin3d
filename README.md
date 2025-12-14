# kotlin3d

A lightweight 3D math library written in Kotlin.

- Focused on vector/matrix/quaternion operations for 3D simulations
- Designed for readability and type safety rather than performance micro-optimizations
- Intended as a reusable core for physics simulations, 3D engines, or robotics tools

## Motivation

This library started as a response to the pain points I kept running into with existing 3D math stacks (Eigen, JVM math helpers, ad-hoc engine utilities):

- Precise 64-bit (double) variants of vectors/matrices/quaternions were rare even though physics/CAE style work needs them.
- APIs were stacked with abstractions, so I had to read deep specs to discover which type to instantiate for simple transforms.
- Feature sets exploded into hundreds of loosely organized functions, making it easy to pick the wrong helper or miss a capability entirely.
- Translating equations from papers is straightforward, but validating the implementation is not - the lack of deterministic, readable code made testing painful.

`kotlin3d` exists so that I can keep the math obvious, traceable, and trustworthy when building simulation tooling.

## Why Kotlin?

- Kotlin lets me write code that looks close to the underlying math: operator overloading, data classes, and extension functions keep vector and matrix expressions legible without template gymnastics.
- It runs anywhere a JVM runs, so I can embed the library inside existing Java services or desktop tooling without changing the deployment model.
- Kotlin is effectively a safer, more expressive superset of Java, which means I can lean on the mature JVM ecosystem while keeping the API concise and type-safe.

## Intended Users

- Engineers who already understand 3D math fundamentals (vectors, matrices, quaternions, coordinate frames).
- People who prioritize numerical accuracy and reproducibility over raw frame-rate oriented performance.
- Anyone who needs a dependable double-precision math core inside Kotlin/JVM systems without adopting a full physics or graphics engine.

## Designed Use Cases

- Building physics or robotics prototypes where I need clean vector/matrix/quaternion operations before committing to heavier engines.
- Running numerical or scientific tooling on the JVM when Python/C++ libraries are unavailable or would complicate deployment.
- Expressing coordinate transforms, line/plane intersections, and other geometric utilities in a readable, testable form that can be reused across simulations.

## Non-Goals

- This is not a graphics/renderer toolkit; it intentionally skips GPU bindings, scene graphs, and other visually oriented features.
- Micro-optimizing for the fastest possible SIMD path is out of scope - the code favors clarity, double precision, and correctness checks.
- It also is not meant to replace full game or physics engines; think of it as a focused math kernel you can drop into those larger stacks when Kotlin makes sense.
