# 目次

# Swiftコーディング規約

## はじめに

本文書は、CookpadにおけるSwiftコードのスタイル基準を定めるものである。

また、本文書はSwift2.2の言語仕様に準拠している。

## 目的

このスタイルガイドは以下のような目標を達成するために策定されている。

1. 意図を明確にする
1. 冗長さを排除し、可読性を高める
1. 一貫性を持たせる
1. 副作用を減らす

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

- **[MUST]** 配列、辞書を複数行で定義する場合、最後の要素にも`,`を付けること
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

- **[MUST]** 制御構文に`()`を付けないこと

```swift
// Bad
if (a == 0) {
}

// Good
if a == 0 {
}
```

- **[MUST]** 行末に`;`を付けないこと

## クラス・構造体

- **[MUST]** Objective-Cからの参照が必要な場合を除き`NSObject`を継承しないこと
- **[SHOULD]** `mutating`な構造体は利用を避けること
    - 一般的に構造体は不変であることが期待されるため

## メソッド

- **[MUST]** スコープが最も狭くなるように適切なアクセスレベルを設定すること
- **[MUST]** 戻り値が`Void`の場合、記述を省略すること

```swift
// Bad
func update() -> Void {
}

// Good
func update() {
}
```

- **[MUST]** クロージャーが1つだけの場合、Trailing Closureを利用すること

```swift
// Bad
client.fetchRecipes(withKeyword: "Sushi", completion: { recipes in
})

// Good
client.fetchRecipes(withKeyword: "Sushi") { recipes in
}
```

- **[MUST]** 引数としてブロックを受け取るとき、そのブロックの生存期間がメソッドの生存期間よりも短い場合、`@noescape`を使うこと

## プロパティ、変数

- **[MUST]** 変更が不要な値は`let`を用いて定数にすること
- **[MUST]** 可能な場合は型宣言を省略すること。ただし、以下のように明示が必要な場合はその限りではない
    - ジェネリクスを利用する場合
    - リテラルを利用する場合
    - 型推論がコンパイル速度に影響を及ぼす場合

### プロパティ

- **[MUST]** 公開する必要が無いプロパティには`private`を明示すること
- **[MUST]** 外部から読み込めるが、書き込ませたくないプロパティには`private(set)`を明示すること
- **[MUST]** プロパティの属性として`weak`を使える場所では使うこと
    - `@IBOutlet`やデリゲートなど、循環参照が発生しうる箇所に付与すること

```swift
// Bad
var delegate: FooDelegate
@IBOutlet var textLabel: UILabel!

// Good
weak var delegate: FooDelegate
@IBOutlet weak var textLabel: UILabel!
```

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

- **[MUST]** グローバル変数として定義しないこと

## 型

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
- **[SHOULD]** `NSString`型を使用しないこと
- **[SHOULD]** `UInt`型を使用しないこと

```swift
// Good
typealias RecipeClientComplationBlock = (Result<[Recipe], APIError>) -> Void
```

## Enum

- **[MUST]** 値はlowerCamelCaseで命名すること。ただしSwift 3から命名規則が変わることに留意すること
- **[SHOULD]** enumを定義するとき、通常は`case`をまとめて記述すること。ただし、値を与える場合や可読性を損なう場合はこの限りではない

```swift
// Bad
enum UserStatus {
    case Guest
    case LoggedIn
    case Premium
}

// Good
enum UserStatus {
    case guest, loggedIn, premium
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

- **[MUST]** 非オプショナル型を利用できる場合はそちらを使うこと
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

- **[SHOULD]** 暗黙的アンラップ型(implicitly unwrapped optional)の使用は避けること
    - 可能な限りオプショナル・バインディングを利用する

## 例外

- **[MUST]** `NSException`を使用しないこと
- **[SHOULD]** `do` ~ `catch`を用いて、`try!`の使用は避けること

## 命名

- **[MUST]** [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines)に従うこと
- **[MUST]** 利用しない戻り値、引数は`_`にすること
- **[MUST]** ベンダープレフィックスを付与しないこと
    - ただし、`NSObject`の継承や既存クラスのextensionなどを用いてObjective-Cからも利用可能になる場合は付けること
- **[MUST]** 定数の命名も変数に従うこと
    - PascalCase、SNAKE_CASEを用いたり、`k`プレフィックスやベンダープレフィックスを付与してはならない
- **[MUST]** 非ASCII文字を命名に使用しないこと

## 構文

- **[MUST]** 浮動小数点型である場合、整数値であっても小数点以下を明示すること

```swift
// Bad
let borderRadius: CGFloat = 1
// Good
let borderRadius: CGFloat = 1.0
```

- **[SHOULD]** 必要の無い明示的な`self`参照は避けること。ただし、`self`を付与した場合の方が可読性が高い場合は使っても良い

```swift
// Good
public class Pantry {
    private(set) var foods: [Food] = []

    func addFood(food: Food) {
        foods.append(food)
    }
}
```

- **[MUST]** 将来的に廃止が予定されている構文を使わないこと
    - 例えば以下のようなものである
        - インクリメント、デクリメント
        - C-style for loop
        - `Selector`
        - 引数の`var`キーワード


## Cocoa

- **[MUST]** ファクトリメソッドではなく、コンストラクタを使うこと

```swift
// Bad
let rect = CGRectMake(10.0, 20.0, 30.0, 40.0)

// Good
let rect = CGRect(x: 10.0, y: 20.0, width: 30.0, height: 40.0)
```

- **[MUST]** コンビニエンスコンストラクタではなく、コンストラクタを使うこと

```swift
// Bad
let url = NSURL.fileURLWithPath("/foo/bar")

// Good
let url = NSURL(fileURLWithPath: "/foo/bar")
```

- **[MUST]** 関数ではなく、プロパティやメソッドを使うこと

```swift
// Bad
let width = CGRectGetWidth(rect)

// Good
let width = rect.width
```

