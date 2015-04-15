# Cookpad CoffeeScript Coding Style Guide

# 目次

- [はじめに](#intro)
- [インデント](#indentation)
- [空白](#whitespace)
- [1行の文字数](#line-columns)
- [数値](#number)
- [文字列](#string)
- [正規表現](#regexp)
- [配列, ハッシュ](#object)
- [変数名](#variable)
- [関数](#function)
- [演算子](#operator)
- [this](#this)
- [JavaScriptの埋め込み](#embed-javascript)
- [雛形](#boilerplate)

<hr id="intro" />

## はじめに

この文書は、クックパッド株式会社における CoffeeScript のスタイル規準を示すものです。
ウェブプログラマであれば誰もが読みやすいコードになるように、可読性と一貫性を重視して規準を定めています。
「bad」として例示されている記法の中には、特に悪いと思えないようなものも存在していますが、その理由は、「書きやすさ」よりも「読みやすさ」、コードの一貫性などを優先するためです。
なお、この文書で定義しないスタイルや、詳細を定義していない部分について議論となった場合は、[Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)([日本語訳](http://cou929.nu/data/google_javascript_style_guide/) )のスタイルを参考にすることとします。


<hr id="indentation" />

## インデント
- **[MUST]** 2スペースを使用する

<hr id="whitespace" />

## 空白
- **[MUST]** 行末に空白を置いてはならない
- **[MUST]** 演算子の前後には空白を1つずつ空ける
  - ただし , の前はスペースを空けない

```coffeescript
foo = 1
bar = 2
if foo < 10
  foo += 10
[foo, bar] = [bar, foo]
```

<hr id="line-columns" />

## 1行の文字数
- **[SHOULD]** 1行の文字数をなるべく80文字以内に納めるようする (全角文字は2文字と数えること)
- **[MUST]** 1行の文字数が128文字を越えてはならない

<hr id="number" />

## 数値
- **[MUST]** 数値は理由がない限り、変数に入れてから使う。その時、変数名は英字の大文字とアンダースコアを使い、定数のように定義する
  - 0や1などの数値で、意味が明確であれば直接使っても良い


<hr id="string" />

## 文字列
- **[SHOULD]** 文字列内部のエスケープシーケンスが少なくなるように適切な区切り記号を使用する
  - HTMLにダブルクォートを使用するので、シングルクォートを推奨する
- **[MUST]** 文字列を連結するときは式展開 ```"#{foo}"``` を使うこと
- **[SHOULD]** 複数行の文字列には <code>""" ..."""</code> を使うことができるが、HTMLはHTMLにHTMLとして書くことが望ましい

<hr id="regexp" />

## 正規表現
- **[MUST]** キャプチャを利用する場合、不要な後方参照を作らないようにする
- **[SHOULD]** /を含む正規表現を書くときは必要に応じて```///[regexp]///```を使用する

<hr id="object" />

## 配列, ハッシュ
- **[SHOULD]** 複数行の配列/ハッシュリテラルを書くときは , を省略する

```coffeescript
foo = [
  'aaaaaa'
  'bbbbbb'
  'cccccc'
]
bar =
  aaa: 'aaaaaa'
  bbb: 'bbbbbb'
  ccc: 'cccccc'
```

<hr id="variable" />

## 変数名
- **[MUST]** JavaScriptの命名規則に従う
  - 変数名は camelCase で、クラスのみ PascalCase にする
- **[SHOULD]** 検索しやすい名前を意識する
  - 短すぎる名前や省略形は避け、変数名に $ は基本的に使用しない。記号は検索しにくい場合があるので、必要以上に使用しないことが望ましい。

<hr id="function" />

## 関数
- **[MUST]** ```->```の前後にはスペースを空ける

```coffeescript
foo = (arg1, arg2) -> # Good
foo = (arg1, arg2)-> # Bad
```


- **[SHOULD]** 引数がないときは括弧を省略する

```coffeescript
bar = -> # Good
bar = () -> # Bad
```

- **[MUST]** メソッドの呼び出しの括弧は基本的に省略しない
  - 括弧を省略するほうが「書きやすい」が、省略されていない方が「読みやすい」ので、読む方を優先する

```coffeescript
# Good
bar.css(top: '10px', left: '10px')

# Bad
bar.css top: '10px', left: '10px'

# Good
bar.bind('click', (evt) ->
  evt.preventDefault()
)

# Bad
foo.bind 'click', (evt) ->
  evt.preventDefault()

# Good
clickHandler = (evt) ->
  evt.preventDefault()

foo.bind('click', clickHandler)
```

- **[SHOULD]** ```this``` を保持したい場合は ```=>``` を使う

```coffeescript
# Good
$(this).delegate('a', 'click', (evt) =>
  $(this)...
)

# Bad
that = this
$(this).delegate('a', 'click', (evt) ->
  $(that)...
)
```


<hr id="operator" />

## 演算子

- **[MUST]** CoffeeScriptには演算子のエイリアスがあるが、エイリアスはコードの統一を阻害する面があるので、原則使用しない
具体的には、

```coffeescript
and, or, is, isnt
```

ではなく、

```coffeescript
&&, ||, ==, !=
```

を使用する

<hr id="this" />

## this

**[MUST]** 単体で @ を使用しない。
```coffeescript
$(this)     # OK
$(@)        # NG
$(this.foo) # OK
$(@foo)     # OK
```

<hr id="embed-javascript" />

## JavaScriptの埋め込み

**[MUST]** 

```coffeescript
a = true
`var x = a ? 1 : 2;`
console.log(x) # 1 
```
のように、CoffeeScriptの中にJavaScriptを埋め込むことができるが、この書き方は使ってはいけない。
原則として、(テンプレートエンジンを除いて)ある言語に別の言語を埋め込む書き方は推奨されません。


<hr id="boilerplate" />

# 雛形

以下は、 CoffeeScript を書く際の 雛形 を示す。
雛形はよく使う書き方なので、読みやすさだけでなく、書きやすさも考慮する。そのため、例外的に括弧の省略を行っている。

## DOM Ready

### jQuery only

```coffeescript
jQuery ($) ->
  # code...
```

Result:

```js
jQuery(function($) {
  // code...
});
```

### jQuery & Zepto

```coffeescript
(this.Zepto || this.jQuery) ($) ->
  # code...
```

Result:

```js
(this.Zepto || this.jQuery)(function($) {
  // code...
});
```

## 即時関数

### jQuery only

```coffeescript
do ($ = jQuery) ->
  # code...
```

Result:

```js
(function($) {
  // code...
})(jQuery);
```

### jQuery & Zepto

```coffeescript
do ($ = this.Zepto || this.jQuery) ->
  # code...
```

Result:

```js
(function($) {
  // code...
})(this.Zepto || this.jQuery);
```
