# 目次

- [Ruby バージョン](#ruby-version)
- [インデント](#indentation)
- [空白](#whitespaces)
- [空行](#empty-lines)
- [文字エンコーディングとマジックコメント](#character-encoding-and-magic-comments)
- [1行の文字数](#line-columns)
- [数値](#numbers)
- [文字列](#strings)
- [正規表現](#regular-expressions)
- [配列](#arrays)
- [ハッシュ](#hashes)
- [演算式](#operations)
- [代入式](#assignments)
- [制御構造](#control-structures)
- [メソッド呼び出し](#method-calls)
- [BEGIN と END](#begin-and-end)
- [モジュールとクラスの定義](#module-and-class-definitions)
- [メソッドの定義](#method-definitions)
- [変数](#variables)

# Ruby コーディング規準

## はじめに

本文書は、株式会社ブイクックでの Ruby コードのスタイル規準を定めるものである。
Ruby プログラマとしての素養をある程度備えている者なら誰もが読みやすいコードになるよう、可読性と一貫性を重視して規準を定めている。
「bad」として例示されている記法の中には、特に悪いと思えないようなものも存在しているが、その理由はコードの一貫性を保つためである。

inspired by [cookpad](https://github.com/cookpad/styleguide/blob/master/ruby.ja.md) , [airbnb](https://github.com/airbnb/ruby#redundant-self)

<a name="ruby-version"></a>

## Ruby バージョン
- **[SHOULD]** Ruby 2.0 のプロジェクトでは、キーワード引数、シンボルリテラル(`%i`)など、Ruby 2.0 で新しく加わった Syntax を推奨する。

<a name="indentation"></a>

## インデント

- **[MUST]** 1レベルのインデントに2つの空白を使用する。水平タブを使用してはならない。
- **[MUST]** 長いメソッドチェインの最後のメソッド呼び出しでブロックを渡す場合、最後のメソッド呼び出しのレシーバをローカル変数として抽出し、ブロック付きメソッド呼び出しを独立した式として書くこと。

    ```ruby
    # good
    posts = Post.joins(:user).
      merge(User.paid).
      where(created_at: target_date)
    posts.each do |post|
      next if staff_ids.include?(post.user_id)
      comment_count += post.comments.size
    end

    # bad
    posts = Post.joins(:user).
      merge(User.paid).
      where(created_at: target_date).each do |post|
        next if staff_ids.include?(post.user_id)
        comment_count += post.comments.size
      end
    ```

- **[SHOULD]** 式の途中で改行する場合は、2行目以降を1行目より1段深くインデントすること。

    ```ruby
    # good
    User.active.
      some_scope(foo).
      other_scope(bar)

    # bad
    User.active.
    some_scope(foo).
    other_scope(bar)
    ```


<a name="whitespaces"></a>
## 空白

- **[MUST]** 行末に空白を置いてはならない。

<a name="empty-lines"></a>
## 空行

- **[MUST]** ファイルの末尾ではちょうど1回だけ改行する。

    ``` ruby
    # bad
    class Foo; end

    # EOF

    # bad
    class Foo; end # EOF

    # good
    class Foo; end
    # EOF
    ```

<a name="character-encoding-and-magic-comments"></a>
## 文字エンコーディングとマジックコメント
- **[MUST]** 特別な理由がない場合はスクリプトのエンコーディングは UTF-8 にすること。
- **[SHOULD]** Ruby 2.0 以降は UTF-8 がデフォルトになったので、スクリプトのエンコーディングが UTF-8 の場合はマジックコメントを書かない。
- **[MUST]** マジックコメントが必要な場合は以下の形式で書くこと。

    ```ruby
    # coding: utf-8
    ```

<a name="line-columns"></a>
## 1行の文字数

- **[SHOULD]** 特殊な事情が無い限りは1行の文字数を128文字以内に納めること (全角文字は2文字と数えること)。

<a name="numbers"></a>
## 数値

- **[SHOULD]** 桁数が大きな10進法の数値リテラルは3桁毎に下線を入れて読みやすくする。
  - 例: `1_000_000.001_023`
- **[SHOULD]** 桁数が大きな2進法および16進法の数値リテラルは4桁毎に下線を入れて読みやすくする。
  - 例: `0xABCD_1234`
- **[SHOULD]** 16進法はアルファベットの大文字で書いても小文字で書いても良いが、1つのファイルの中では統一すること。
- **[SHOULD]** Ruby 2.1 以上を使用している場合、分数は `r` サフィックスを用いて書くこと。
  - 例: `1/2r #=> (1/2)`
- **[SHOULD]** Ruby 2.0.0 以前を使用している場合、分数は `Integer#quo` メソッドを使って書くこと。
  - 例: `1.quo(2) #=> (1/2)`
- **[SHOULD]** Ruby 2.1 以上を使用している場合、複素数は `i` または `ri` サフィックスを用いて書くこと。
  - 例: `1 + 2i #=> (1+2i)`

<a name="strings"></a>
## 文字列

- **[SHOULD]** 空文字列は `''` と書くこと。
- **[SHOULD]** 理由が無い限り引数なしの `String.new` を使ってはならない。
- **[MUST]** 文字列内部のエスケープシーケンスが最も少なくなるよう、適切な区切り記号を選択すること。
- **[SHOULD]** `%` 記法で文字列リテラルを書く場合は、括弧を区切り記号に使用すること。括弧の種類はどれでも良い。ただし、次のような特殊な場合は括弧以外を区切り記号として良い:

    ```ruby
    OPEN_PARENTHESES = %!({[!
    ```

- **[MUST]** 式展開の中で `Object#to_s` だけを呼び出してはならない。たとえばこういうの→`"#{obj.to_s}"`
- **[MUST]** 式展開の中括弧を省略してはならない。
- **[SHOULD]** (Ruby 1.9+) Unicode 文字をエスケープシーケンスで書く場合は、`"\xE3\x8C\xB3"` ではなく `"\u{3333}"` のように、`\u` を使うこと。ただし、Ruby 1.8 系でも動作する必要があるスクリプトではこの限りではない。
- **[SHOULD]** ループ内で文字列リテラルを書いてはならない。ここでループとは `while`、`until`、`for`、およびイテレータ (`each` などのブロックを何度も呼び出すブロック付きメソッド呼び出しのブロック内) である。
- **[SHOULD]** 文字列リテラルに対して `String#+` を用いて他の文字列を連結してはならない。式展開を使用すること。
- **[MUST]** 文字列の破壊的追記を `+=` で行ってはならない。`String#<<` または `String#concat` を使用すること。

<a name="regular-expressions"></a>
## 正規表現

- **[SHOULD]** 必要のない後方参照グループを作成してはならない。`(?: ... )` を使うこと。
- **[SHOULD]** 複雑な正規表現を記述する場合は、`x` オプションを利用し、改行、空白、コメント (`(?# ... )`) を使用して可読性を向上させること。
  - 具体例は [uri/common.rb で定義されている正規表現](https://github.com/ruby/ruby/blob/trunk/lib/uri/common.rb#L457) が参考になる。

<a name="arrays"></a>
## 配列

- **[MUST]** 代入の後ろに配列リテラルを複数行で書く場合は、`[` の後ろで改行し、要素行のインデントを1レベル下げ、`]` は独立した行に置いて `[` を書いた行の行頭にインデントを揃える。

    ``` ruby
    # good
    array = [
      :foo,
      :bar,
      :baz,
    ]

    # bad
    array = [ :foo,
              :bar,
              :baz, ]

    # bad
    array = [ :foo,
              :bar,
              :baz,
            ]

    # bad
    array = [
      :foo,
      :bar,
      :baz, ]
    ```

- **[SHOULD]** 複数行の配列リテラルでは、最後の要素の後ろにも `,` を書くこと。
- **[SHOULD]** 文字列配列は%記法 `%w(...)` or `%W(...)` を用いて書くこと。

    ```ruby
    # good
    words = %w(foo bar baz)

    # bad
    words = ['foo', 'bar', 'baz']
    ```

- **[MUST]** 空配列は `[]` と書くこと。
- **[MUST]** 引数なしの `Array.new` を使ってはならない。
- **[SHOULD]** 同じ要素を `n` 個持つ配列を初期化するときは `Array.new(n, obj)` を使用すること。`[obj] * n` と書いてはならない。
- **[SHOULD]** 範囲リテラルを配列に変換するときは、`Range#to_a` ではなく `[*range]` を使うこと。

    ```ruby
    # good
    [*1..10]  #=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

    # bad
    (1..10).to_a  #=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    ```

<a name="hashes"></a>
## ハッシュ

- **[MUST]** ハッシュリテラルを一行に書くときは、`{` と第一要素のキーとの間、および最終要素の値と `}` との間にそれぞれ空白を一つずつ入れること。

    ```ruby
    # good
    { hoge: 1, fuga: 2 }
    # bad
    {hoge: 1, fuga: 2}
    ```

- **[MUST]** ハッシュリテラルは、Symbol を key にするときは HashRocket (Ruby 1.8 までで使われていた `=>` の記法`{ :foo => 42 }`)を使わない。

    ```ruby
    # good
    { first: 42,
      second: 'foo',
    }
    # bad
    { :first => 42,
      :second => 'foo',
    }
    ```

- **[SHOULD]** ハッシュリテラルの key が symbol だが、`.`, `-`等を含む場合、HashRocket に統一するのが望ましい。

    ```ruby
    # good
    { :cookpad => 42,
      :'cookpad.com' => 'foo',
    }
    # bad
    { cookpad: 42,
      :'cookpad.com' => 'foo',
    }
    ```

- **[MUST]** 空ハッシュは `{}` と書くこと。

- **[MUST]** 代入の後ろにハッシュリテラルを複数行で書く場合は、`{` の後で改行し、要素行のインデントを1レベル下げ、`}` は独立した行に置いて `{` を書いた行の行頭にインデントを揃える。

    ```ruby
    # good
    hash = {
      first: 42,
      second: 'foo',
    }

    # bad
    hash = { first: 42,
             second: 'foo',
           }

    # bad
    hash = { first: 42,
             second: 'foo',
    }

    # bad
    hash = { first: 42,
      second: 'foo',
    }

    # bad
    hash = {
    first: 42,
    second: 'foo',
    }
    ```

- **[SHOULD]** 複数行のハッシュリテラルでは、最後の要素の後ろにも `,` を書くこと。
- **[SHOULD]** ハッシュのキーを Symbol にして良い場合は、文字列よりも高速に lookup できるので積極的に Symbol を使うこと。

  ```ruby
  # good
  { foo: 1, bar: 2 }

  # bad
  { 'foo' => 1, 'bar' => 2 }
  ```

- **[SHOULD]** (Ruby 1.9+) ハッシュリテラルのキーがすべて Symbol リテラルのときは `{ key: value }` の記法を使うこと。このとき `:` の後ろに空白を入れること。

<a name="operations"></a>
## 演算式

- **[SHOULD]** 演算子の両側に空白を入れること。ただし `**` の両側には空白は入れないこと。
- **[MUST]** `and`、`or`、および `not` は使用しないこと。
  - ただし、`式 or raise 'message'`の時のみ`or`を使っても良い。
- **[MUST]** 条件演算子を入れ子にしてはならない。
- **[MUST]** 条件演算子を複数行で書いてはならない。

    ```ruby
    # good
    fizzbuzz = if n % 3 == 0
        n % 5 == 0 ? 'fizzbuzz' : 'fizz'
      else
        n % 5 == 0 ? 'buzz' : "#{n}"
      end

    # bad
    fizzbuzz = n % 3 == 0 ? (n % 5 == 0 ? 'fizzbuzz' : 'fizz') : (n % 5 == 0 ? 'buzz' : "#{n}")

    # bad
    fizzbuzz = n % 3 == 0 ?
      (n % 5 == 0 ? 'fizzbuzz' : 'fizz') :
      (n % 5 == 0 ? 'buzz' : "#{n}")
    ```

<a name="assignments"></a>
## 代入式

- **[MUST]** 複合代入はリテラルまたは引数なしのメソッド呼び出しの結果を代入する場合、および2つの変数または属性の値を交換する場合のみ使ってよい。
- **[MUST]** 代入記号の両側に空白を入れること。
- **[MUST]** 条件式で代入式を書かないこと。

<a name="control-structures"></a>
## 制御構造

- **[SHOULD]** `if !condition` の代わりに `unless condition` と書くこと。
- **[SHOULD]** `while !condition` の代わりに `until condition` と書くこと。
- **[SHOULD]** `unless` に対して `else` を使ってはならない。
- **[MUST]** `if`、`unless`、および `case` の条件式の後ろは改行すること。同じ行に本体コードを続けて書いてはならない。
- **[MUST]** `if`、`unless`、および `case` で `then` や `:` を使用してはならない。
- **[MUST]** `while` および `until` の条件式の後ろは改行すること。同じ行に本体コードを続けて書いてはならない。
- **[MUST]** `while` および `until` で `do` や `:` を使用してはならない。
- **[SHOULD]** `unless` および `until` の条件式に複数の項を `||` で結合した論理式 (加法標準形) を書いてはならない。
- **[SHOULD]** 条件式と本体コードが短い場合は修飾子記法を使用すること。
- **[SHOULD]** 条件式が長く複数行になってしまう場合は、その条件式を述語メソッドとして抽出し、適切な名前を付けて使用すること。
- **[MUST]** 制御構造が代入の右辺に来る場合は、本体コードのインデントを2レベル下げ、制御構造の終わりの `end` 行のインデントを本体コードより1レベル戻すこと。

    ```ruby
    # good
    result = if condition
        body_code
      end

    # good
    result =
      if condition
        body_code
      end

    # bad
    result = if condition
      body_code
    end

    # bad
    result = if condition
               body_code
             end
    ```

- **[MUST]** return, next, break が意味のある値を返さない場合は、後ろに式を書いてはならない。
   - コントローラのアクションの内部で redirect_to や render などの継続処理を宣言するメソッドを呼び出す場合は、return や next の後ろにそれらのメソッド呼び出しを記述してよい。

<a name="method-calls"></a>
## メソッド呼び出し

- **[MUST]** メソッド呼び出しの括弧は、省略を許可された場合を除いて、省略してはならない。
- **[MUST]** 引数なしのメソッド呼び出しは括弧を省略すること。
- **[MUST]** DSL-like メソッドの呼び出しでは、メソッド呼び出しの括弧を省略して良い。ただし、第一引数全体が括弧で囲まれる場合は警告が出てしまうため省略してはならない。DSL-like メソッドとは以下のメソッドである。
   - p, print, puts, require などのグローバル関数。
   - attr_reader や private など、クラスやモジュールの定義で用いる宣言的メソッド (Rails のようなフレームワークが提供しているものも含む)。
   - redirect_to や render などの ActionController が提供する、アクションに対する継続処理を宣言するためのメソッド。
   - その他、DSL のために用意されていると考えられるメソッド。
- メソッド呼び出しを入れ子にする場合、他の規則で禁止されている場合を除いて、最も外側のメソッド呼び出しの括弧を省略してもよい。
- **[MUST]** メソッド名とメソッド呼び出しの括弧の間に空白を入れてはならない。

    ```ruby
    # good
    p(1 + 2)

    # bad
    p (1 + 2)
    ```

- **[SHOULD]** Ruby 3.0 からのキーワード引数とポジショナル引数の分離を踏まえ、メソッド呼び出しではキーワード引数に渡しているのかハッシュオブジェクトを渡しているのかを区別する。

    ```ruby
    def foo(a:, b:)
      p(a, b)
    end

    def bar(hash)
      p(hash)
    end

    # good
    foo(a: 1, b: 2)
    # good
    bar({a: 1, b: 2})

    # bad - foo expects keyword arguments, but it passes a hash
    foo({a: 1, b: 2})
    # bad - bar expects a hash, but it passes keyword arguments
    bar(a: 1, b: 2)
    ```

- **[MUST]** ブロック付きメソッド呼び出しでブロックの戻り値を使わない場合（つまりブロックの副作用が目的の場合）は、`do`/`end` 記法でブロックを書くこと。
- **[MUST]** ブロック付きメソッド呼び出しの戻り値に対して処理を行う場合は、ブロックを中括弧で書くこと。

    ```ruby
    # good
    puts [1, 2, 3].map {|i|
      i * i
    }

    # bad
    puts [1, 2, 3].map do |i|
      i * i
    end

    # good
    [1, 2, 3].map {|n|
      n * n
    }.each do |n|
      puts Math.sqrt(n)
    end

    # bad
    [1, 2, 3].map do |n|
      n * n
    end.each {|n|
      puts Math.sqrt(n)
    }
    ```

- **[MUST]** ブロック付きメソッド呼び出しを1行で書く場合は、ブロックを中括弧で書くこと。
- **[MUST]** `do`/`end` によるブロックでは、`do`の前後に空白を1つ入れ、ブロックパラメータの後で改行し、`end` は独立した行に書くこと。ブロック本体のインデントは1レベル下げ、`end` のインデントはメソッド呼び出しの1行目にあわせること。

    ```ruby
    # good
    [1, 2, 3].each do |num|
      puts num
    end

    # bad
    [1, 2, 3].each do |num|
        puts num
      end

    # bad
    [1, 2, 3].each do |num|
                     puts num
                   end

    # bad
    [1, 2, 3].each do |num| puts num end
    ```

- **[MUST]** 中括弧によるブロックでは、`{` の前に空白を1つ入れること。
- **[MUST]** 中括弧によるブロックを1行で書く場合は、`{`　またはブロックパラメータと本体コードの間、および本体コードと `}` の間に空白を1つずつ入れること。

    ```ruby
    # good
    [1, 2, 3].each {|num| puts num }
    [1, 2, 3].each { |num| puts num }

    # bad
    [1, 2, 3].each {|num| puts num}

    # bad
    [1, 2, 3].each { |num| puts num}

    # good
    10.times { puts 'Hello world' }

    # bad
    10.times {puts 'Hello world' }

    # bad
    10.times {puts 'Hello world'}

    # bad
    10.times { puts 'Hello world'}
    ```

- **[SHOULD]** メソッド呼び出しにおいて、実引数の前の式が長い場合は以下の規則に基いて複数行に分けて書くこと。
  - 実引数が長い場合はメソッド呼び出しの開き括弧 `(` の直後で改行し、次の行からインデントレベルを下げて実引数を1行に1つずつ書き、メソッド呼び出しの閉じ括弧 `)` を独立した行にインデントレベルを戻して書くこと。

      ```ruby
      Foo.new(
        arg,
        long_argument,
        key: value,
        long_key: long_value,
        pretty_so_much_very_long_key:
          pretty_so_much_very_tooooooooooooooooooooo_long_value,
      )
      ```

  - 実引数が長くない場合はメソッド呼び出しの開き括弧 `(` に続けて第1引数を書き、次の行からインデントレベルを第1引数に合わせて第2引数以降を1行に1つずつ書き、メソッド呼び出しの閉じ括弧 `)` を最後の引数の直後に書くこと。

      ```ruby
      Foo.new(arg,
              long_argument,
              key: value,
              long_key: long_value)
      ```

  - DSL 的なメソッドを複数行で書く場合は、第1引数をメソッド名の直後に書き、次の行から第2引数以降を1行に1つずつインデントレベルを1つ下げて書くこと。

      ```ruby
      ActionMailer::Base.delivery_method :smtp,
        host: 'localhost',
        port: 25
      ```

<a name="begin-and-end"></a>
## BEGIN と END

- **[MUST]** `BEGIN` ブロックと `END` ブロックは使ってはならない。

<a name="module-and-class-definitions"></a>
## モジュールとクラスの定義

- **[MUST]** メソッドのエイリアスを定義する場合は表記揺れを防止するため `alias` ではなく `alias_method` を使用する。
- **[MUST]** アクセサを定義する場合は `attr_accessor`、`attr_reader`、`attr_writer` を使用し、`attr` は使用しない。
- **[MUST]** クラスメソッドの定義では、無闇にインデントレベルを深くしないために `self.` を使用すること。但し、privateなものとpublicなものを同時に定義したい場合等は、`class << self`を利用しても構わない。

    ```ruby
    class Foo
      # good
      def self.foo
      end

      # bad
      def Foo.foo
      end
    end
    ```

- **[MUST]** プライベートまたはプロテクテッドなクラスメソッドを定義する場合は `class << self`/`end` の中でメソッドを定義し、可視性を変更すること。

   ```ruby
    class Foo
      # good
      class << self
        def foo
        end
        private :foo
      end

      # bad
      def self.foo
      end
      class <<self
        private :foo
      end
    end
   ```


- **[SHOULD]** カレントインスタンスを参照する際は、`self.some_method`よりも`some_method`の方が好ましい

```
# bad
def end_date
  self.start_date + self.nights
end

# good
def end_date
  start_date + nights
end
```

以下のような3つのケースにおいては、`self.`は、良い使い方である。

1. クラスメソッドを定義するケース: `seld.some_method`
2. `self`が`ActiveRecord Model`である場合の属性の割り当てを含む、左辺が割り当てメソッドのケース: `self.guest = user.`
3. 現在のインスタンスクラスを参照するケース: `self.class`


- **[MUST]** メソッド定義の後で、そのメソッドの可視性を変更するために `private` や `protected` や `public` を引数付きで呼び出す場合は、メソッド定義とこれらのメソッド呼び出しの間に空行を入れてはならない。

    ```ruby
    class Foo
      # good
      def foo
      end
      private :foo

      # bad
      def foo
      end

      private :foo
    end
    ```

- **[MUST]** `private` や `protected` や `public` を引数なしで使用する場合、インデントレベルはメソッド定義と同じレベルとし、前後に1行ずつ空白を入れること。

    ```ruby
    # good
    class Foo
      def foo
      end

      private

      def bar
      end
    end

    # bad
    class Foo
      def foo
      end

    private

      def bar
      end
    end

    # bad
    class Foo
      def foo
      end

      private

        def bar
        end
    end

    # bad
    class Foo
      def foo
      end

      private
      def bar
      end
    end
    ```

- **[SHOULD]** モジュールやクラスが外部に公開するメソッドとアクセサに対してドキュメンテーションコメントを Markdown 形式で書くこと。
- **[SHOULD]** ドキュメンテーションコメントは[YARD](http://yardoc.org/)のフォーマットを推奨。
- **[MUST]** ドキュメンテーションコメントとメソッド定義の間に空行を書いてはならない。
- **[MUST]** 一つのクラスに複数の異なる責務を持たせないこと。

<a name="method-definitions"></a>
## メソッドの定義

- **[MUST]** メソッド定義において、引数リストの括弧は省略してはならない。ただし、引数なしメソッドを定義する場合は括弧を省略すること。
- **[MUST]** メソッドと引数リストの括弧の間に空白を置いてはならない。
- **[SHOULD]** メソッドの本体コードにコメントを書かなければ理解できないようなコードを書いてはならない。
  - メソッド本体内にコメントを書くよりも、別のメソッドに分けて適切な名前を付ける方が可読性が向上する。
  - ただし、数式に対する補足や出展などはコード本体中にコメントとして書いても良い。
- **[MUST]** 一つのメソッドに複数の異なる責務を持たせないこと。
- **[MUST]** 引数を破壊してはいけない

    ```ruby
    # good
    def your_method(str)
      new_str = str.sub('xxx', 'yyy')
    end

    # bad
    def your_method(str)
      str.sub!('xxx', 'yyy')
    end
    ```

<a name="variables"></a>
## 変数

- **[MUST]** グローバル変数 (`$foo`) を新たに導入してはならない。
- **[MUST]** クラス変数 (`@@foo`) を使用してはならない。代わりに `class_attribute` を使用すること。
- **[MUST]** 変数名は英単語を省略せずに使って名付けること。ただし、変数名が長くなってしまう場合は、単語の頭文字を除く母音を削除したり、一般的な略語を選択するなどの方法で短縮化してよい。また、慣習的な名前 (ループカウンタとしての `i` や `j` など) は許可する。
- **[SHOULD]** ひとつの変数を異なる役割のために使い回してはならない。そうしたくなる場合は、メソッドを複数のメソッドに分割できる。
- **[SHOULD]** ローカル変数のスコープ (有効範囲) を可能な限り小さくすること。ローカル変数が存在しないメソッドは良いメソッドである。

## その他 (分類の仕方が難しいやつ)

- **[MUST]** 破壊的メソッドを使用する際は、その副作用を最小限の範囲に留めること。
