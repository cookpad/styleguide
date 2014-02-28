# 目次

- [ARC](#ARC)
- [モジュールとクラスの定義](#Definitions_of_modules_and_classes)
- [メソッドの定義](#Definitions_of_methods)
- [プロパティの定義](#Property)
- [変数](#Variables)
- [Enum] (#Enum)
- [命名] (#Naming)
- [未分類] (#Misc)

# Objective-C コーディング規準

## はじめに

本文書は、COOKPADにおけるObjective-C コードのスタイル規準を定めるものである。

基本はAppleのスタイルを採用する。理由はAppleのFrameworkを多用するため、Apple Styleの.hファイルを見ることが多いためである。
https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html

次いでGoogle Objective-Cスタイルガイドラインも参照する。
http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml

- 基本的には見やすさ、美しさ、を重視するが、パフォーマンスに影響がある場合はその限りではない。
- 納得できる合理的な理由がある場合は、無い場合の主観的ルールは書き換えられる。
 - たとえば、インスタンス変数のprefixに _ を付けるのは美しくないという主張に対しては、propertyのsynthesizeの仕様でprefixに _ が付くことになっているため _ を付ける、という主張が採用される。

<a id="ARC"></a>
## ARC

- **[MUST]** ARCで書くこと。見やすいし、ARCで書くとメモリ管理の作法が強制され、コードが統一されるため。非ARCのライブラリはなるべく使わない。

<a id="Definitions_of_modules_and_classes"></a>
## モジュールとクラスの定義

- **[MUST]** propertyやメソッドの定義は可能な限りクラス拡張を使用する。公開する必要のないpropertyやメソッドをヘッダに定義してはいけない。

```
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

  前者はpublic扱い。後者は（概ね）private扱い。

- **[SHOULD]** protocolの実装の宣言は基本的にクラス拡張で行う。
 - 公開しなくて良いものは極力公開しない。

```
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

ただ、そのprotocolの実装をしているということを公開したいというケースではもちろんヘッダーで宣言してよい。

<a id="Definitions_of_methods"></a>
## メソッドの定義

- **[SHOULD]** プライベートメソッドは宣言しない。現在のClangでは宣言なしで定義できるためコード量削減のため記述しない。

```
// Bad
@interface Hoge ()
- (void)_privateMethod1;
- (void)_privateMethod1;
@end

// Good
@interface Hoge ()
@end
```

<a id="Property"></a>
## プロパティの定義

- **[MUST]** propertyの属性としてweakを使えるところでは使う
 - SEGVの防止とインスタンス保持者の明確化のため

```
// Bad
@property (assign) id delegate;
@property (strong) IBOutlet UILabel *label;

// Good
@property (weak) id delegate;
@property (weak) IBOutlet UILabel *label; //< このlabelをどこかにaddSubviewする場合
```

`weak`だと参照しているインスタンスが解放されたタイミングで`nil`になるが、`assign`だとならない。
そのため、インスタンスの参照には`weak`を使うほうが安全。

また、どこかに`addSubview`する類いのものはadd先のViewがそのインスタンスを保持するため、`property`のほうでは`weak`参照にしておくことが望ましい。

- **[MUST]** 代入が必要ないプロパティはreadonlyにする
 - できるだけ副作用の少ないプログラムにするため

```
// Bad
@interface OriginalView
@property (weak) UILabel *label; //< 例えば、このlabelのtextは変更したいがlabel自体は変更しないケース
@end

// Good
@interface OriginalView
@property (readonly) UILabel *label;
@end
```

- **[SHOULD]** @synthesizeは使用しない。現在のClangでは@synthesizeを明記する必要がなくなったためコード量削減のため記述しない。

- **[SHOULD]** @propertyにするのが妥当ではないインスタンス変数の宣言はクラス拡張で先頭に " _ "をつけて行うこと。

```
@interface ViewController () <UITableViewDataSource>
{
    NSNumber *_recipeID;
}
@end
```

よくある例として、あるViewがもっているUILabelのtextプロパティを外部から変更したいようなときがある。
このケースだと、UILabelのプロパティは変更するものの、UILabel自体を変更しないのでUILabelはreadonlyなプロパティとして公開するほうがよい。

- **[SHOULD]** propertyへのアクセスはset/getメソッドではなく "." dot を使うこと。Propertyではないメソッドで "."は使わないことが望ましいが、可読性が高まるならば許容する。

```
// Bad
[view setBackgroundColor:[UIColor whiteColor]];

// Good
view.backgroundColor = [UIColor whiteColor];
```

<a id="Variables"></a>
## 変数

- **[MUST]** スコープがもっとも狭くなるように宣言すること。スコープによるバグを防ぐため。C言語古来の関数の先頭にすべての変数を宣言するスタイルは使わない。

<a id="Enum"></a>
## Enum

- **[SHOULD]** enumでなくNS_ENUMを使う
 - より安全なコードにするため。

```
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
- **[SHOULD]** その他UIKitフレームワークのUIXXXを継承する場合は、基本的にAbcXXXとする。ただしUITableViewCellなど長いクラス名で末尾の単語のみ残せば意味が通るものは例外とする。

```
// Samples
UIView *originalView = [CKDOriginalView new];
UIButton *originalButton = [CKDOriginalButton buttonWithType:UIButtonTypeCustom];
UITextField *originalTextField = [CKDOriginalTextField new];

// 例外
UITableViewCell *originalCell = [[CKDOriginalCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"XXX"];
```

<a id="Misc"></a>
## 未分類

- **[MUST]** Clang 3.4以降のObjective-C Literalsが使用可能なら使うこと。
 - 記述が簡潔になり見やすいため。

http://clang.llvm.org/docs/ObjectiveCLiterals.html


```
// Bad
NSNumber *fortyTwo = [NSNumber numberWithInt:42];

NSArray *array = [NSArray arrayWithObjects:@"one", @"two", nil];

NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:@"Key", @"Value", nil];

// Good
NSNumber *fortyTwo = @42;

NSArray *array = @[@"one", @"two"];

NSDictionary *dictionary = @{@"key" : @"value"};
```
