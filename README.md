# kotlin3d

A lightweight 3D math library written in Kotlin.

- Focused on vector/matrix/quaternion operations for 3D simulations
- Designed for readability and type safety rather than performance micro-optimizations
- Intended as a reusable core for physics simulations, 3D engines, or robotics tools

## Quick Start

### Vector Operations

```kotlin
// Creating vectors
val vec1 = Vector3(1.0, 2.0, 3.0)
val vec2 = Vector3(4.0, 5.0, 6.0)

// Basic arithmetic
val sum: Vector3 = vec1 + vec2
val diff: Vector3 = vec1 - vec2
val scaled: Vector3 = vec1 * 2.0

// Vector properties
val length: Double = vec1.length
val normalized: Vector3 = vec1.unit
val isUnitVector: Boolean = vec1.isUnit

// Vector operations
val dotProduct: Double = vec1 dot vec2
val crossProduct: Vector3 = vec1 cross vec2
val angle: Double = vec1.getAngle(vec2)  // Angle in radians
val parallel: Vector3 = vec1.getParallel(vec2)
val perpendicular: Vector3 = vec1.getPerpendicular(vec2)

// Predefined unit vectors
val xAxis = Vector3.unitX  // (1, 0, 0)
val yAxis = Vector3.unitY  // (0, 1, 0)
val zAxis = Vector3.unitZ  // (0, 0, 1)
```

### Matrix Operations

```kotlin
// Creating matrices
val mat1 = Matrix3(
    1.0, 0.0, 0.0,
    0.0, 1.0, 0.0,
    0.0, 0.0, 1.0
)

// Create from rows or columns
val mat2 = Matrix3.ofRows(
    Vector3(1.0, 0.0, 0.0),
    Vector3(0.0, 1.0, 0.0),
    Vector3(0.0, 0.0, 1.0)
)

// Matrix properties
val determinant: Double = mat1.det
val transpose: Matrix3 = mat1.T
val inverse: Matrix3? = mat1.inv  // Returns null if not invertible

// Matrix operations
val sum: Matrix3 = mat1 + mat2
val product: Matrix3 = mat1 * mat2
val transformed: Vector3 = mat1 * vec1

// Create transformation matrices
val rotation: Matrix3? = Matrix3.createRotation(Vector3.unitZ, Math.PI / 4)  // Rotate 45° around Z-axis
val scaling: Matrix3 = Matrix3.createScale(Vector3.unitX, 2.0)  // Scale 2x along X-axis
val coordSystem: Matrix3 = Matrix3.createCsys(Vector3.unitX, Vector3.unitY)

// Predefined matrices
val identity = Matrix3.identity
val zero = Matrix3.zero
```

### Quaternion Operations

```kotlin
// Creating quaternions
val quat1 = Quaternion(0.0, 0.0, 0.0, 1.0)  // Identity (no rotation)

// Create from rotation axis and angle
val axis: Vector3 = Vector3.unitZ
val angle: Double = Math.PI / 2  // 90 degrees
val rotation: Quaternion? = Quaternion.createRotation(axis, angle)

// Quaternion properties
val norm: Double = rotation!!.norm
val normalized: Quaternion = rotation.unit
val inverse: Quaternion = rotation.inv
val rotationAxis: Vector3 = rotation.axis
val rotationAngle: Double = rotation.angle

// Quaternion operations
val combined: Quaternion = rotation * rotation  // Combine rotations
val rotationMatrix1: Matrix3 = rotation.toMatrix()  // Convert to 3x3 matrix

// Convert matrix to quaternion
val rotationMatrix2: Matrix3? = Matrix3.createRotation(Vector3.unitX, Math.PI / 6)
val fromMatrix: Quaternion? = Quaternion.createFromMatrix(rotationMatrix2!!)
```

### Practical Example: Rotating a Point

```kotlin
// Define a point in 3D space
val point = Vector3(1.0, 0.0, 0.0)

// Rotate 90 degrees around Z-axis using matrix
val rotationMatrix: Matrix3? = Matrix3.createRotation(Vector3.unitZ, Math.PI / 2)
val rotatedPoint1: Vector3 = rotationMatrix!! * point

// Or rotate using quaternion
val rotationQuat: Quaternion? = Quaternion.createRotation(Vector3.unitZ, Math.PI / 2)
val rotatedPoint2: Vector3 = rotationQuat!!.toMatrix() * point

// Both results should be approximately (0, 1, 0)
println(rotatedPoint1.isClose(Vector3(0.0, 1.0, 0.0)))  // true
```

### Comparison and Equality

```kotlin
val vec1 = Vector3(1.0, 2.0, 3.0)
val vec2 = Vector3(1.0, 2.0, 3.0000000001)

// Use isClose for floating-point comparison
vec1.isClose(vec2)  // true (within tolerance)

// Same for matrices and quaternions
val mat1: Matrix3 = Matrix3.identity
val mat2: Matrix3 = Matrix3.identity
mat1.isClose(mat2)  // true

val quat1: Quaternion = Quaternion.identity
val quat2: Quaternion = Quaternion(0.0, 0.0, 0.0, 1.0)
quat1.isClose(quat2)  // true
quat1.isEquivalent(quat2)  // true (also checks negated quaternion)
```

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
