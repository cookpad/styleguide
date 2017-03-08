# Swift coding style

## Introduction

This document defines the conventions for writing Swift code at Cookpad.

This guide is for Swift 3.0.2.

## Objective

This guide aims to accomplish the following objectives:

1. Clarify purpose
1. Improve readability
1. Keep consistency
1. Reduce side-effects

## Code style

- **[MUST]** Use four spaces for 1-level of indentation. Do not use the horizontal tab character.
- **[MUST]** Add linebreak at end of files.
- **[MUST]** Place open brackets on same lines.

```swift
// Bad
func update() 
{
}

// Good
func update() {
}
```

- **[MUST]** Use trailing comma on elements of collections.
    - This will reduce file diffs in the future.

```swift
// Bad
let ingredients = [
    "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half"
]

// Good
let ingredients = [
    "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half",
]
```

- **[MUST]** Do not use external () to wrap conditional statements.

```swift
// Bad
if (a == 0) {
}

// Good
if a == 0 {
}
```

- **[MUST]** Do not use `;` at end of lines.

## Classes, Structs

- **[MUST]** Unless a reference from Objective-C is needed, do not inherit from NSObject.
- **[SHOULD]** Since structs should be immutable, do not use `mutating` attribute for structs.

## Method

- **[MUST]** Use the most restrictive access modifier if possible. Prefer `private` -> `fileprivate` -> `internal` -> `public`.
- **[MUST]** Do not use `Void` as return type.

```swift
// Bad
func update() -> Void {
}

// Good
func update() {
}
```

## Closure

- **[MUST]** Use trailing closure if the method has exactly one closure.

```swift
// Bad
client.search(for: "Sushi", completion: { recipes in
})

// Good
client.search(for: "Sushi") { recipes in
}
```

- **[MUST]** Do not use `()` when the calling closure has just one argument.

```swift
// Bad
let titles = recipes.map() { $0.title }

// Good
let titles = recipes.map { $0.title }
```

- **[MUST]** Use shorthand argument if closure is defined on a single line.
    - In contrast, do not use it if the closure is defined on multiple lines.

```swift
// Bad
let titles = recipes.map { recipe in recipe.title }

// Good
let titles = recipes.map { $0.title }
```

```swift
// Bad
client.search(keyword: "Sushi") {
    if $1 != nil {
        recipes = $0
    }
}

// Good
client.search(keyword: "Sushi") { results, error in
    if error != nil {
        recipes = results
    }
}
```

- **[MUST]** Do not use `@escaping` unless necessary.
- **[MUST]** Use `weak` instead of `unowned` as capture controls.
    - While `unowned` can sometimes improve performance, readability will be lost.

## Property, Variable

- **[MUST]** Use `let` to make variables immutable when possible.
- **[MUST]** Drop type definitions when possible. However, when trying to avoid type inferencing for faster compilation time, you may choose to use them.

### Property

- **[MUST]** Use the most restrictive access modifier if possible.
- **[MUST]** Use `private(set)` for the property want to write from limited scopes.
- **[MUST]** Use `weak` for property attributes when possible.
    - You should use `weak` to avoid circular references (eg. `@IBOutlet`, delegate)
- **[MUST]** Do not use the `unowned` property attribute.
    - In some cases, `unowned` improves performance. On the other hand, readability will be lost.

```swift
// Bad
var delegate: FooDelegate
@IBOutlet var textLabel: UILabel!

// Good
weak var delegate: FooDelegate
@IBOutlet weak var textLabel: UILabel!
```

- **[MUST]** Do not use a `get` block when possible on computed properties.

```swift
// Bad
var foodCount: Int {
    get { return foods.count }
}

// Good
var foodCount: Int { 
    return foods.count 
}
```

- **[SHOULD]** If setting a property has a side-effect, write it in `willSet` or `didSet`.

### Variable

- **[MUST]** Do not use global variables.


## Type

- **[MUST]** Do not use `Array<T>`, `Dictionary<K, V>`. Use syntactic sugar instead.

```swift
// Bad
let seasonings: Array<String> = ["sugar", "salt", "vinegar", "soy sauce", "miso"]
let ingredients: Dictionary<String, String> = [
    "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half",
]

// Good 
let seasonings: [String] = ["sugar", "salt", "vinegar", "soy sauce", "miso"] 
let ingredients: [String: String] = [ 
    "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half",
]
```

- **[SHOULD]** Use `typealias` to represent complex type definitions.

```swift
// Good
typealias RecipeClientCompletionBlock = (Result<[Recipe], APIError>) -> Void
```

- **[SHOULD]** Use classes without `NS` prefixes when possible.

```swift
// Bad
let url = NSURL(string: "https://cookpad.com/")

// Good
let url = URL(string: "https://cookpad.com/")
```

- **[SHOULD]** Do not use `UInt`
    - [Apple announced to avoid using it.](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID320)

## Enum

- **[MUST]** Use lowerCamelCase for value names.
- **[MUST]** Drop enum type if you can.

```swift
// Bad
user.status = UserStatus.guest

// Good
user.status = .guest
```

## Optional

- **[MUST]** Do not use `Optional<T>`. Use syntax sugar instead.
- **[MUST]** Use non-Optional value when possible.
- **[MUST]** Do not use `guard` without optional-binding.

```swift
// Bad
guard isLoggedIn else {
    return
}

// Good
if !isLoggedIn {
    return
}
```

```swift
// Good
guard let user = manager.currentUser else {
    return
}
label.text = user.name
```

## Exception

- **[MUST]** Do not use `NSException`.
- **[SHOULD]** Do not use `try!`. Use `do` ~ `catch` instead.

## Naming

- **[MUST]** Follow [Swift API Design Guideline](https://swift.org/documentation/api-design-guidelines/)
- **[MUST]** Use _ for unused return values or arguments.
- **[MUST]** Do not use vendor prefixes.
- **[MUST]** Follow the same naming rules for constants as variables.
    - Do not use UpperCamelCase, SNAKE_CASE or `k` prefix when naming constants.
- **[MUST]** Do not use non-Ascii characters in variable names.
- **[SHOULD]** Use nested structures to represent hierarchy.

```swift
// Bad
enum RecipeType {
    case none
}

class Recipe {
}

// Good
class Recipe {
    enum Type {
        case none
    }
}

```

## Syntax

- **[MUST]** Do not use `self` when possible. However, you can use it in the following situations:
  - Assign value and variable have the same names.
  - Call method in closures.
- **[MUST]** Do not use any syntax which will be deprecated in the future.

## Cocoa

- **[MUST]** Do not use factory methods. Use constructors instead.

```swift
// Bad
let rect = CGRectMake(10.0, 20.0, 30.0, 40.0)

// Good
let rect = CGRect(x: 10.0, y: 20.0, width: 30.0, height: 40.0)
```

- **[MUST]**  Do not use class methods for instantiation. Use constructors.

```swift
// Bad
let url = NSURL.fileURL(withPath: "/foo/bar")

// Good
let url = URL(fileURLWithPath: "/foo/bar")
```

- **[MUST]** Do not use functions to handle structs. Use property instead.

```swift
// Bad
let width = CGRectGetWidth(rect)

// Good
let width = rect.width
```
