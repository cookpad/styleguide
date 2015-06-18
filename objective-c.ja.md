# 目次

- [モジュールとクラスの定義](#Definitions_of_modules_and_classes)
- [メソッドの定義](#Definitions_of_methods)
- [プロパティの定義](#Property)
- [変数](#Variables)
- [型](#Types)
- [Enum] (#Enum)
- [命名] (#Naming)
- [構文] (#Misc)

# Objective-C コーディング規準

## はじめに

本文書は、COOKPADにおけるObjective-C コードのスタイル規準を定めるものである。

基本はAppleのスタイルを採用する。理由はAppleのFrameworkを多用するため、Apple Styleの.hファイルを見ることが多いためである。

* [Apple Coding Guidelines](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)

基本的には標準的なスタイルを定めている。また、Swiftへの移行を見越して、複数の選択肢があるときはSwiftに近いスタイルを採用する。なお、「正しいコードを書く」というのは前提なので、そのためのtipsなどは記載しない。

<a id="Definitions_of_modules_and_classes"></a>
## モジュールとクラスの定義

- **[MUST]** 公開する必要のないpropertyやメソッドの定義はクラス拡張を使用して実装ファイルに記述する。ヘッダファイルには公開メソッドとプロパティのみを記述すること

```objc
// Bad
// ViewController.h

@interface ViewController : UIViewController
@property (weak) IBOutlet UILabel *label;
@property (weak) IBOutlet UIButton *button;
@property (weak) IBOutlet UITextView *textView;
@end

// Good
// ViewController.m

@interface ViewController ()
@property (weak) IBOutlet UILabel *label;
@property (weak) IBOutlet UIButton *button;
@property (weak) IBOutlet UITextView *textView;
@end

@implementation ViewController
@end
```

- **[MUST]** protocolの適用と実装も公開の必要がなければ実装ファイルでクラス拡張により行う
  - もちろん公開の必要があればヘッダファイルに書いてよい

```objc
// Bad
// ViewController.h

@interface ViewController : UIViewController <UITableViewDataSource>
@end

// Good
// ViewController.m

@interface ViewController () <UITableViewDataSource>
@end

@implementation ViewController
@end
```

<a id="Definitions_of_methods"></a>
## メソッドの定義

- **[MUST]** プライベートメソッドは、 `_` prefixを付けてはいけない

```objc
// Bad
- (void)_privateMethod1
{
  // ...
}

// Good
- (void)privateMethod1
{
  // ...
}
```

<a id="Property"></a>
## プロパティの定義

- **[MUST]** propertyの属性として `nonatomic` をつけること
  - atomicによってスレッドセーフになるのは実質的に構造体のみと考えられるため
  - またatomicであってもそのクラス自体がスレッドセーフになるわけではない
  - atomicをつける妥当な理由がある場合は、それをコメントに書いたうえで使うこと
- **[MUST]** propertyの属性としてweakを使える場所では使うこと

```objc
// Bad
@property (nonatomic) id delegate;
@property (nonatomic) IBOutlet UILabel *label;

// Good
@property (nonatomic, weak) id<FooDelegate> delegate;
@property (nonatomic, weak) IBOutlet UILabel *label;
```

- **[MUST]** 代入が必要ないプロパティはreadonlyにする
 - できるだけ副作用の少ないプログラムにするため

```objc
// Bad
@interface OriginalView
@property (nonatomic, weak) UILabel *label; //< 例えば、このlabelのtextは変更したいがlabel自体は変更しないケース
@end

// Good
@interface OriginalView
@property (nonatomic, readonly) UILabel *label;
@end
```

- **[MUST]** `assign` は冗長なので指定しないこと
- **[MUST]** `NSString`, `NSArray`, `NSDictionary`, `NSSet` のプロパティは `copy` を指定すること
  - 実体がimmutableなオブジェクトであればメモリ割り当てのコストは発生しないし、mutableなオブジェクトであれば安全のためにcopyすべきであるため
  - その他、必要に応じて `NSCopying` なクラスでは `copy` を指定すること
- **[MUST]** 原則としてインスタンス変数は宣言せず、必要なプロパティは `@property` として宣言すること

```objc
// Bad
@interface ViewController () <UITableViewDataSource>
{
    NSString *_recipeID;
}
@end

// Good
@interface ViewController () <UITableViewDataSource>
@property (nonatomic, copy) NSString *recipeID;
@end

```

- **[MUST]** プロパティへのアクセスはメソッド呼び出しではなく ドット記法を使うこと

```objc
// Bad
[view setBackgroundColor:[UIColor whiteColor]];

// Good
view.backgroundColor = [UIColor whiteColor];
```

* **[MUST]** プロパティの宣言などでカラムの位置は調整しなくてよい
  * 修正時の差分を最小にするため

```objc
// Bad
@property (nonatomic)       int       foo;
@property (nonatomic, copy) NSString *bar;

// Good
@property (nonatomic) int foo;
@property (nonatomic, copy) NSString *bar;
```


<a id="Variables"></a>
## 変数

- **[MUST]** スコープがもっとも狭くなるように宣言すること
- **[MUST]** 変数の再利用をしてはいけない
  - 同じ型の変数であっても、用途が違う場合は都度用途に適した名前を付けて宣言すること
- **[MUST]** 定数としては `extern NSString *const FOO_BAR` またはNS_ENUMを使うこと
  - 定数としてマクロを使ってはならない

<a id="Types"></a>
## 型

- **[MUST]** pointerのアスタリスクは型ではなく変数につけること
- **[MUST]** 原則として生の `id` 型を使用してはいけない
  * CocoaTouchの仕様上必要な場合を除き、明示的に使う必要はないはずである
  * プロトコルつきの `id<Protocol>` はこの限りではない
- **[MUST]** Xcode 7以上を使っている場合はコレクション型でgenericsを使うこと
  * genericsにできない場合は設計が間違っている可能性がある
- **[SHOULD]** `__nullable` / `__nonnull` が使える場所ではなるべく使うこと
  * [Nullability and Objective-C - Swift Blog - Apple Developer](https://developer.apple.com/swift/blog/?id=25)

<a id="Enum"></a>
## Enum

- **[MUST]** enumでなくNS_ENUMを使う
 - より安全なコードにするため。

```objc
// Bad
typedef enum {
    TypeNone = 0,
    TypeDone = 1,
} Type;

// Good
typedef NS_ENUM(char, Type) {
    TypeNone = 0,
    TypeDone = 1,
};
```

後者だと`Type`型として定義できる数値を限定できる。例えば、誤って`TypeNG = 0xFF`とかを加えてしまったときにコンパイルエラーを出してくれる。

<a id="Naming"></a>
## 命名

- **[MUST]** UIViewControllerを親に持つControllerはAbcViewController、その他のControllerはAbcControllerとする（Abcを任意の名称で）。
- **[MUST]** その他UIKitフレームワークのUIXXXを継承する場合は、基本的にAbcXXXとする。ただしUITableViewCellなど長いクラス名で末尾の単語のみ残せば意味が通るものは例外とする。

```objc
// Samples
UIView *originalView = [CKDOriginalView new];
UIButton *originalButton = [CKDOriginalButton buttonWithType:UIButtonTypeCustom];
UITextField *originalTextField = [CKDOriginalTextField new];

// 例外
UITableViewCell *originalCell = [[CKDOriginalCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"XXX"];
```

<a id="Misc"></a>
## 構文

- **[MUST]** Objective-C Literalsを使うこと

http://clang.llvm.org/docs/ObjectiveCLiterals.html

```objc
// Bad
NSNumber *fortyTwo = [NSNumber numberWithInt:42];

NSArray *array = [NSArray arrayWithObjects:@"one", @"two", nil];

NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:@"Key", @"Value", nil];

// Good
NSNumber *fortyTwo = @42;

NSArray *array = @[@"one", @"two"];

NSDictionary *dictionary = @{@"key" : @"value"};
```


- **[MUST]** `nil` の条件判定は `if (x == nil)` または `if (x != nil)` とする

```objc
// Bad
if (nil != x) { /* ... */ }
if (nil == x) { /* ... */ }
if (!x) { /* ... */ }
if (x) { /* ... */ }

// Good
if (x != nil) { /* ... */ }
if (x == nil) { /* ... */ }
```
