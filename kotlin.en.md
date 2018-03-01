# Kotlin coding style

## Introduction

This document defines the conventions for writing Kotlin code at Cookpad.

We follow [Kotlin official Coding Conventions](https://kotlinlang.org/docs/reference/coding-conventions.html) , the styles of naming, spaces, line-breaks, and the others unless there is a special mention.

## Objective

This guide aims to accomplish the following objectives:

1. Clarify purpose
2. Improve readability
3. Keep consistency
4. Reduce side-effects

## Code Style

- **[SHOULD]** Put `,` at the end of elements in Enum.
  - Reduce the difference when we add new elements.

```kotlin
// Bad
enum class Color {
    RED,
    BLUE,
    GREEN
}

// Good
enum class Color {
    RED,
    BLUE,
    GREEN,
}
```

## Method

- **[MUST]** Put lambda expression out of `()` when last argument type of method is function.

```kotlin
// Bad
val ints = Array(5, { it * 2 })

// Good
val ints = Array(5) { it * 2 }
```

- **[MUST]** Omit `()` when method has only one argument that is function.

```kotlin
// Bad
val nums = ints.map({ it * it })

// Good
val nums = ints.map { it * it }
```

- **[MUST]** Do not use `Unit` as return type.

```kotlin
// Bad
fun method(): Unit {
    // do something
}

// Good
fun method() {
    // do something
}
```

## Null-Safety

- **[MUST]** Use `!!` only when you would like to explicit Non-Null variables
- **[SHOULD]** Use `?:` operator as possible to check null value in assignment expression.

```kotlin
// Bad
val message = if (e != null) e.message else ""

// Good
val message = e.message ?: ""
```

## Lambda expression
- **[MUST]** Assign a label for a lambda in nested lambda expressions.
- **[MUST]** Declare parameter names in nested lambda expressions.
- **[SHOULD]** Declare parameter names in single lambda expressions.

```kotlin
// BAD
fun run() {
    hogeEntity?.let { hoge ->
        hoge.fugaEntity?.let { fuga ->
            if (!fuga.isEmpty()) {
                return@let
            }
        }
    }
}

// GOOD
fun run() {
    hogeEntity?.let parent@ { hoge ->
        hoge.fugaEntity?.let child@ { fuga ->
            if (!fuga.isEmpty()) {
                return@child
            }
        }
    }
}
```
