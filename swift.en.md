# Swift coding style

## Introduction

This document defines the conventions for writing Swift code at Cookpad Inc.

This guide directed for Swift 3.0.2.

## Objective

This guides aim to accomplish these objectives.

1. Clarify purposes 
1. Improve readability
1. Keep consistency
1. Reduce side-effects

## Code style

- **[MUST]** Use four spaces for 1-level of indent.  Do not use the horizontal tab character.
- **[MUST]** Add linebreak to end of files
- **[MUST]** Place open brackets on same lines

```swift
// Bad
func update() 
{
}

// Good
func update() {
}
```

- **[MUST]** Use trailing comma on elements of collections
    - Because of reducing file diffs

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

- **[MUST]** Do not use `()` wraps condition statements

```swift
// Bad
if (a == 0) {
}

// Good
if a == 0 {
}
```

- **[MUST]** Do not use `;` on end of lines

## Class, Struct

- **[MUST]** Do not inherit `NSObject` except if needed to refer from Objective-C.
- **[SHOULD]** Do not use `mutating` attribute for structs.
    - Because structs should be immutable.

## Method

- **[MUST]** Use closest access level as possible.
- **[MUST]** Do not use `Void` as returning type.

```swift
// Bad
func update() -> Void {
}

// Good
func update() {
}
```

## Closure

- **[MUST]** Use trailing closure if the closure has just one argument.

```swift
// Bad
client.search(keyword: "Sushi", completion: { recipes in
})

// Good
client.search(keyword: "Sushi") { recipes in
}
```

- **[MUST]** Do not use `()` when calling the closure has just one argument.

```swift
// Bad
let titles = recipes.map() { $0.title }

// Good
let titles = recipes.map { $0.title }
```

- **[MUST]** Use shorthand argument if closure defined in one line.
    - In contrast, Do not use it if closure defined in multiple lines.

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

- **[MUST]** Do not use `@escaping` without needed
- **[MUST]** Use `weak` instead of `unowned` as capture controls.
    - In some cases, `unowned` improves performance. In other hand readability will be lost.

## Property, Variable

- **[MUST]** Use `let` to make variables immutable as possible.
- **[MUST]** Drop type definition as possible. However, in the case of avoiding type inference for improvement compilation time, you can define clearly.

### Property

- **[MUST]** Use closest access control as possible.
- **[MUST]** Use `private(set)` for the property want to write from limited scopes.
- **[MUST]** Use `weak` as property attributes as possible.
    - You should use `weak` to avoid circular references (eg. `@IBOutlet`, delegate)
- **[MUST]** Do not use `unowned` as property attribute.
    - In some cases, `unowned` improves performance. In other hand readability will be lost.

```swift
// Bad
var delegate: FooDelegate
@IBOutlet var textLabel: UILabel!

// Good
weak var delegate: FooDelegate
@IBOutlet weak var textLabel: UILabel!
```

- **[MUST]** Do not use `get` block as possible on computed properties.

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

- **[MUST]** Do not use `Array<T>`, `Dictionary<K, V>`. Use syntax sugar instead.

```swift
// Bad
let seasonings: Array<String> = ["sugar", "salt", "vinegar", "soy sauce", "miso"]
let ingredients: Dictionary<String, String> = [
    "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half",
]

// Good let seasonings: [String] = ["sugar", "salt", "vinegar", "soy sauce", "miso"] let ingredients: [String: String] = [ "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half",
]
```

- **[SHOULD]** Use `typealias` to represent complex type definitions.

```swift
// Good
typealias RecipeClientCompletionBlock = (Result<[Recipe], APIError>) -> Void
```

- **[SHOULD]** Use classes without `NS` prefixes as possible.

```swift
// Bad
let url = NSURL(string: "https://cookpad.com/")

// Good
let url = URL(string: "https://cookpad.com/")
```


- **[SHOULD]** Do not use `UInt`
    - [Apple announced to avoid using it.](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID320)

## Enum

- **[MUST]** Use lowerCamelCase as value naming.
- **[MUST]** Drop enum type if you can.

```swift
// Bad
user.status = UserStatus.guest

// Good
user.status = .guest
```

## Optional

- **[MUST]** Do not use `Optional<T>`. Use syntax sugar instead.
- **[MUST]** Use non-Optional value as possible.
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
guard let user = manager.currentUser {
    return
}
label.text = user.name
```

## Exception

- **[MUST]** Do not use `NSException`.
- **[SHOULD]** Do not use `try!`. Use `do` ~ `catch` instead.

## Naming

- **[MUST]** Follow [Swift API Design Guideline](https://swift.org/documentation/api-design-guidelines/)
- **[MUST]** Use `_` for unused returning values or arguments.
- **[MUST]** Do not use vendor prefix.
- **[MUST]** Follow same naming rule for constants as variables.
- **[MUST]** To define constants, follow naming rule of variables.
    - Do not use UpperCamelCase, SNAKE_CASE or `k` prefix as constants names.
- **[MUST]** Do not use non-Ascii characters as variable names.
- **[SHOULD]** Use nest structure to reperesent hierarchy.

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

- **[MUST]** Do not use `self` as possible. However, you can use it like following situations.
  - Assign value from variable has same names
  - Call method in closures
- **[MUST]** Do not use syntaxes which will be deprecated in the future.

## Cocoa

= **[MUST]** Do not use factory method. Use constructors instead.

```swift
// Bad
let rect = CGRectMake(10.0, 20.0, 30.0, 40.0)

// Good
let rect = CGRect(x: 10.0, y: 20.0, width: 30.0, height: 40.0)
```

- **[MUST]** Do not use class method to create new instances. Use constructors.

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
