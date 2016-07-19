# 目次

# Swiftコーディング規約

## はじめに

本文書は、CookpadにおけるSwiftコードのスタイル基準を定めるものである。

また、本文書はSwift2.2の言語仕様に準拠している。

## コードスタイル

- **[MUST]** インデントにはソフトタブを使い、幅は4にすること
- **[MUST]** ファイル終端は改行すること
- **[MUST]** オープンブラケットは同じ行に記述すること

```swift
// Bad
func update() 
{
}

// Good
func update() {
}
```

- **[MUST]** 制御構文に`()`を付けてはならない

```swift
// Bad
if (a == 0) {
}

// Good
if a == 0 {
}
```

- **[MUST]** 行末に`;`を付けてはならない
- **[SHOULD]** Xcodeの自動インデントに従うこと
- **[SHOULD]** 配列、辞書を複数行で定義する場合、最後の要素にも`,`を付けること
    - 修正時の差分を最小にするため

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

## クラス・構造体

- **[SHOULD]** 必要が無い限り`NSObject`を継承しないこと
- **[SHOULD]** 利用可能な限り構造体を使うこと
- **[SHOULD]** `mutating`な構造体は利用を避けること
- **[SHOULD]** 複数のプロトコルを実装している場合は、`extension`を用いて構造化するのが望ましい

```swift
// Good
class TableViewController: UIViewController {
}

extension TableViewController: UITableViewDataSource {
    // mark - UITableViewDataSource
}

extension TableViewController: UITableViewDelegate {
    // mark - UITableViewDelegate
}
```

## メソッド

- **[MUST]** 公開する必要が無いメソッドには`private`を明示すること
- **[MUST]** 戻り値が`Void`の場合、記述を省略すること

```swift
// Bad
func update() -> Void {
}

// Good
func update() {
}
```

- **[SHOULD]** クロージャーが1つだけの場合、Trailing Closureを利用すること

```swift
// Bad
client.fetchRecipes(withKeyword: "Sushi", completion: { recipes in
})

// Good
client.fetchRecipes(withKeyword: "Sushi") { recipes in
}
```

- **[SHOULD]** 引数としてブロックを渡すとき、可能な限り`@noescape`を使うこと

## プロパティ、変数

- **[MUST]** 可能な限り`let`を使用すること
- **[SHOULD]** 可能な場合は型宣言を省略すること。ただし、以下のように明示が必要な場合はその限りではない
    - ジェネリクスを利用する場合
    - リテラルを利用する場合
    - 型推論がコンパイル速度に影響を及ぼす場合

### プロパティ

- **[MUST]** 外部から参照しないプロパティについては`private`または`private(set)`を利用すること
- **[MUST]** プロパティの属性として`weak`を使える場所では使うこと
- **[MUST]** Computed Propertyについて、省略可能なとき`get`を省略すること

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

- **[SHOULD]** 副作用を伴う場合、`willSet`, `didSet`に記述すること

### 変数

- **[MUST]** `var`を使った変数の再利用をしてはならない
    - 修正時の差分を最小にするため
- **[MUST]** グローバル変数を用いてはならない

## 型

- **[MUST]** `NSString`型を用いてはならない 
- **[MUST]** `UInt`型の利用は避けること
- **[SHOULD]** 可能な限り`NSArray`, `NSDictionary`, `NSSet`の利用を避け、`Array`, `Dictionary`, `Set`を用いること
- **[MUST]** `Array<T>`, `Dictionary<K, V>`の表記は避け、シンタックスシュガーを使うこと

```swift
// Bad
let seasonings: Array<String> = ["suger", "salt", "vinegar", "soy sauce", "miso"]
let ingredients: Dictionary<String, String> = [
    "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half",
]

// Good
let seasonings: [String] = ["suger", "salt", "vinegar", "soy sauce", "miso"]
let ingredients: [String: String] = [
    "bacon": "100g",
    "lettuce": "1/4",
    "tomato": "half",
]
```


- **[SHOULD]** コールバックなど、複雑なブロック型を扱う場合には`typealias`を使うこと

```swift
// Good
typealias RecipeClientComplationBlock = (Result<[Recipe], APIError>) -> Void
```

## Enum

- **[MUST]** 値はPascalCaseで命名すること。ただしSwift3から命名規則が変わることに留意すること
- **[SHOULD]** enumを定義するとき、通常は`case`をまとめて記述すること。ただし、値を与える場合はこの限りではない

```swift
// Bad
enum UserStatus {
    case Guest
    case LoggedIn
    case Premium
}

// Good
enum UserStatus {
    case Guest, LoggedIn, Premium
}
```

- **[MUST]** 型名が省略可能なときは省略すること

```swift
// Bad
user.status = UserStatus.Guest

// Good
user.status = .Guest
```

## オプショナル

- **[SHOULD]** 非オプショナル型を利用できる場合は可能な限りそちらを使うこと
- **[SHOULD]** 暗黙的アンラップ型(Implicitly unwrapped optional)の使用は避けること
    - 可能な限りオプショナル・バインディングを利用する
- **[SHOULD]** オプショナル・バインディングが必要で`guard`の方が可読性が優れる場合はそちらを使うこと

```swift
// Bad
if let recipes = client.recipes(withKeyword: "寿司") {
    // ...
} else {
    throw APIError.RecipeNotFound
}

// Good
guard let recipes = client.recipes(withKeyword: "寿司") else {
    throw APIError.RecipeNotFound
}

// ...
```

- **[SHOULD]** `flatMap`や`??`を活用すること

## 例外

- **[MUST]** `NSException`を用いてはならない
- **[SHOULD]** `do` ~ `catch`を用いて、`try!`の使用は避けること

## 命名

- **[MUST]** [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines)に従うこと
- **[MUST]** ベンダープレフィックスを付与してはならない
    - Objective-Cからも利用可能にする場合、`@objc`を用いてベンダープレフィックスを付けた命名をObjective-C向けにのみ公開すること
- **[MUST]** 定数の命名も変数に従うこと。PascalCase、SNAKE_CASEを用いたり、`k`プレフィックスやベンダープレフィックスを付与してはならない
- **[MUST]** 非ASCII文字を命名に用いてはならない
- **[MUST]** 利用しない戻り値、引数は`_`にすること

## 構文

- **[MUST]** 浮動小数点型である場合、整数値であっても小数点以下を明示する

```swift
// Bad
let borderRadius: CGFloat = 1
// Good
let borderRadius: CGFloat = 1.0
```

- **[SHOULD]** 必要の無い明示的な`self`参照は避けること

```swift
// Good
public class Pantry {
    var foods = [Food]

    func addFood(food: Food) {
        foods.append(food)
    }
}
```

- **[SHOULD]** 可能な場合、関数型的アプローチを試みること

```swift
// Bad
var ingredients: [Food] = []
for food in pantry.foods {
    if food.isVegetable {
        ingredients.addFood(food)
    }
}

// Good
let ingredients = pantry.foods.filter { $0.isVegetable }
```

- **[MUST]** 将来的に廃止が予定されている構文を使ってはならない
    - インクリメント、デクリメント
    -  C-style for loop

## Cocoa

- **[SHOULD]** ファクトリメソッドではなくコンストラクタを使うこと

```swift
// Bad
let rect = CGRectMake(10.0, 20.0, 30.0, 40.0)

// Good
let rect = CGRect(x: 10.0, y: 20.0, width: 30.0, height: 40.0)
```

- **[SHOULD]** 関数ではなくプロパティ、メソッドを使うこと

```swift
// Bad
let width = CGRectGetWidth(rect)
// Good
let width = rect.size.width
```
