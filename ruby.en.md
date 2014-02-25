# Table of Contents

- [Ruby version](#ruby-version)
- [Indentation](#indentation)
- [Whitespaces](#whitespaces)
- [Empty lines](#empty-lines)
- [Magic comment](#magic-comments)
- [Line columns](#line-columns)
- [Numbers](#numbers)
- [Strings](#strings)
- [Regular expressions](#regular-expressions)
- [Arrays](#arrays)
- [Hashes](#hashes)
- [Operations](#operations)
- [Assignments](#assignments)
- [Control structures](#control-structures)
- [Method calls](#method-calls)
- [BEGIN and END](#begin-and-end)
- [Module and class definitions](#module-and-class-definitions)
- [Method definitions](#method-definitions)
- [Variables](#variables)

# Ruby coding style

## Introduction

This document defines the regulations to write Ruby code in COOKPAD Inc.
These regulations are formed with respect for readability and maintenability so that developers familiar to Ruby can read codes easily.
To keep the readability and the consistency of code, there are examples which is not so bad but is denoted as "bad".

## Ruby version
- **[SHOULD]** Projects using Ruby 2.0 should prefer Ruby 2.0 syntax where possible: e.g. keyword arguments, symbol array literal notation(`%i`).

## Indentation

- **[MUST]** Use two spaces for 1-level of indent.  Do not use the horizontal tab character.
- **[MUST]** When you want to pass a block in the last method call of a long method chain, you must instead extract the receiver of the last method call into a local variable, and write the method call with block as a separate statement on the following line.

    ```ruby
    # good
    posts = Post.joins(:user)
      .merge(User.paid)
      .where(created_at: target_date)
    posts.each do |post|
      next if stuff_ids.include?(post.user_id)
      comment_count += post.comments.size
    end

    # bad
    posts = Post.joins(:user)
      .merge(User.paid)
      .where(created_at: target_date).each do |post|
        next if stuff_ids.include?(post.user_id)
        comment_count += post.comments.size
      end
    ```

- **[SHOULD]** When breaking an expression over multiple lines, you must increase the indentation level.

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

## Whitespaces

- **[MUST]** Do not put whitespaces at the end of line.

## Empty lines

- **[MUST]** Do not put empty lines at the end of file.

## Magic comments

- **[SHOULD]** Do not use magic comments on Ruby 2.0+ because UTF-8 is set as the default encoding after Ruby 2.0.
- **[MUST]** Use the following style for magic comments on Ruby 1.9+

    ```ruby
    # coding: utf-8
    ```

## Line columns

- **[SHOULD]** Keep lines fewer than 80 characters.
- **[MUST]** Keep lines fewer than 128 characters.

## Numbers

- **[SHOULD]** Use underscores to separate every three-digits to write numbers with many digits for readability.
  - eg: `1_000_000.001_023`
- **[SHOULD]** Use underscores to separate every four-digits to write binary and hexadecimal numbers with many digits for readability.
  - eg: `0xABCD_1234`
- **[SHOULD]** Do not mix uppercase and lowercase letters in hexadecimal numbers in a file.
- **[SHOULD]** Use `Integer#quo` method for writing fractional numbers.
  - eg: `1.quo(2) #=> (1/2)`

## Strings

- **[SHOULD]** Use `''` to write empty strings.
- **[SHOULD]** Do not use `String.new` method with no arguments without any reasons.
- **[MUST]** Use appropriate punctuation characters to minimize the number of escape sequences in string contents.
- **[SHOULD]** Use parentheses to write strings by `%` notation.  You can use any kind of parentheses.  In the following cases you can use non-parentheses characters for punctuations.

    ```ruby
    OPEN_PARENTHESES = %!({[!
    ```

- **[MUST]** Do not write only `Object#to_s` in string interpolation, such as `"#{obj.to_s}"`.
- **[SHOULD]** (Ruby 1.9+) Use `\u` notation to write Unicode characters rather than writing each bytes in `\x`, eg. `"\u{3333}"` instead of `"\xE3\x8C\xB3"`.  If you have to write a script for both ruby 1.8 and 1.9, you may write each bytes in `\x` form.
- **[SHOULD]** Do not write string literals in loops, where the loops are `while`, `until`, `for`, and iterators that.
- **[SHOULD]** Do not use `String#+` to concatenate any string objects to string literals.  Use string interpolations.
- **[MUST]** Do not use `+=` to append string to string in a destructive manner.  Use `String#<<` or `String#concat`.

## Regular expressions

- **[SHOULD]** Do not make unnecessary backref groups.  Use `(?: ... )`.
- **[SHOULD]** Use `x` option for writing complicated regular expressions.  This option allows you to use line breaks, whitespace, and comments (`(?# ... )`) in the regular expressions to improve their readability.
  - You can find the practical example in [uri/common.rb](https://github.com/ruby/ruby/blob/trunk/lib/uri/common.rb#L457).

## Arrays

- **[MUST]** If you write an array in multiple lines, insert a white space between `[` and the first item, and align the head of each item.

    ```ruby
    # good
    [ :foo,
      :bar,
      :baz
    ]

    # bad
    [:foo,
      :bar,
      :baz
    ]
    ```

- **[MUST]** If you write an array literal just after an assignment operator such as `=`, obey the following form denoted as "good".

    ```ruby
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

- **[SHOULD]** In the multi line array literal, put `,` after the last item.
- **[SHOULD]** Use general delimited input (percent) syntax `%w(...)` or `%W(...)` for word arrays.

    ```ruby
    # good
    words = %w(foo bar baz)

    # bad
    words = ['foo', 'bar', 'baz']
    ```

- **[MUST]** Use `[]` to write empty arrays.
- **[MUST]** Do not use `Array.new` without any arguments.
- **[SHOULD]** Use `Array.new(n, obj)` to generate an array with `n` same items.  Do not use `[obj] * n` to reduce object allocation.
- **[SHOULD]** Use `[*range]` form instead of `Range#to_a` to convert range literals to arrays.

    ```ruby
    # good
    [*1..10]  #=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

    # bad
    (1..10).to_a  #=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    ```

## Hashes

- **[MUST]** Put whitespaces between `{` and the first key, and between the last value and `}` when you write hash literal in multiple lines.

    ```ruby
    # good
    { hoge: 1,
      fuga: 2,
    }
    ```

- **[MUST]** Use new hash syntax in 1.9+ (`{ foo: 42 }`) if all keys can be written in that syntax:

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

- **[SHOULD]** Use hash rocket (`{ :foo => 42 }`) for all keys if any of keys can't be written in new Hash syntax (e.g. `:'foo.bar'`, `:'foo-bar'`, `if`, `unless`)

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

- **[MUST]** Use `{}` to write empty hashes.
- **[MUST]** For multiline hashes, write the first item just after `{`, increment the level of indentation after the second items, put `}` on an independent line, and align the line of `{` to the head of the line of the first item.

    ```ruby
    # good
    { first: 42,
      second: 'foo',
    }

    # bad
    {
      first: 42,
      second: 'foo',
    }

    # bad
    { first: 42,
    second: 'foo',
    }
    ```

- **[MUST]** If you write a hash literal just after an assignment operator such as `=`, obey the following form denoted as "good".

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

- **[SHOULD]** In the multi line hash literal, put `,` after the last item.
- **[SHOULD]** If Symbol literals can be used as keys of hashes, use it for faster item lookup.

  ```ruby
  # good
  { foo: 1, bar: 2 }

  # bad
  { 'foo' => 1, 'bar' => 2 }
  ```

- **[SHOULD]** (Ruby 1.9+) If all the keys of hash literals are Symbol literals, use the form of `{ key: value }`.  Put a whitespace after `:`.

## Operations

- **[SHOULD]** Put whitespaces around of operators except for `**`.
- **[MUST]** Do not use `and`, `or`, and `not`.
  - But, you can only use `or` for the case `expression or raise 'message'`.
- **[MUST]** Do not nest conditional operators.
- **[MUST]** Do not write conditional operators in multiple line.

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

## Assignments

- **[MUST]** Parallel assignments can be used only for assigning literal values or results of methods without arguments, and exchanging two variables or attributes.
- **[MUST]** Put whitespaces around of assignment operators.
- **[MUST]** Do not write assignment expressions in conditional claues.

## Control structures

- **[SHOULD]** Use `unless condition` instead of `if !condition`.
- **[SHOULD]** Use `until condition` instead of `while !condition`.
- **[SHOULD]** Do not use `else` for `unless`.
- **[MUST]** Put a line break just after the condition clause of `if`, `unless`, and `case`.  Do not follow a body code.
- **[MUST]** Do not use `then` and `:` for the condition clause of `if`, `unless`, and `case`.
- **[MUST]** Put a line break just after the condition clause of `while` and `until`.  Do not follow a body code.
- **[MUST]** Do not use `do` and `:` for the condition clause of `while` and `until`.
- **[SHOULD]** Do not write a logical expressions combined by `||` in the condition clause of `unless` and `until`.
- **[SHOULD]** Use modifier forms if conditions and bodies are short.
- **[SHOULD]** Extract conditions as predicator methods with appropriate names if the conditions are long or multiple line.
- **[MUST]** If you write a control structure just after an assignment operator such as `=`, obey the following form denoted as "good".

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

- **[MUST]** Dont put any expressions which don't have meaningful return values after return, next, and break.
   - In actions of controllers, if you write method calls of declaring continuation such as redirect_to and render, you can put them after return or next.

## Method calls

- **[MUST]** Do not omit parentheses of method calls except for the following permitted cases.
- **[MUST]** You must omit parentheses for method calls without any arguments.
- **[MUST]** For DSL-like methods (as in the examples below), you may omit parentheses when calling them.  But you should not omit parentheses for the case where the first argument is enclosed in parentheses because this case generates syntax warning.
   - Global methods such as p, print, and puts.
   - Declarative methods used in definitions of classes and modules such as private and attr_reader, including them provided by frameworks like Rails.
   - Methods for declaring continuation for actions of controllers such as redirect_to and render provided by ActionController.
   - Other, DSL-like methods.
- You may omit the parentheses of the outermost method call when nesting method calls, provided that other guidelines are not violated
- **[MUST]** Do not put whitespaces between a method name and parenthesis.

    ```ruby
    # good
    p(1 + 2)

    # bad
    p (1 + 2)
    ```

- **[SHOULD]** Omit `{ }` of a hash literal which put on the end of arguments.

    ```ruby
    # good
    foo(1, 2, foo: :bar, baz: 42)

    # bad
    foo(1, 2, { foo: :bar, baz: 42 })
    ```

- **[MUST]** Use `do`/`end` form for blocks of method calls where the return value of the block is unused. i.e. blocks executed for side-effects
- **[MUST]** Use `{ }` form (i.e. brace blocks) for blocks of method calls where the return value of the block used. e.g. as an argument of the other method call.

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
    }.each {|n|
      puts Math.sqrt(n)
    }

    # bad
    [1, 2, 3].map do |n|
      n * n
    end.each do |n|
      puts Math.sqrt(n)
    end
    ```

- **[MUST]** Use brace block of a method call wrote in one line.
- **[MUST]** Obey the following "good" form in `do`/`end` form of blocks.

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

- **[MUST]** Put a whitespace before `{` of brace blocks.
- **[MUST]** In a brace block wrote in one line, put a whitespace between `{` or a list of block parameters and a body block, and the body block and `}`.

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

- **[SHOULD]** On a method call, if a part before method parameters are long, write these arguments in mutiple lines according to the following regulations.
  - if the arguments is long, put a line break after an open parenthesis `(`, increment the level of indent from the next line and put each argument on each line, and put a close parenthesis `)` on the independent line and decrease the level of indentation of the line.

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

  - If the arguments is not long, continue the first argument after an open parenthesis `(`, increment the level of indentation from the next line and put each argument on each line, and put a close parenthesis `)` just after the last argument.

      ```ruby
      Foo.new(arg,
              long_argument,
              key: value,
              long_key: long_value)
      ```

  - For writing a DSL-like method call in multiple lines、put the first argument just after the method name, increment the level of indentation fron the next line, and put each argument on each line.

      ```ruby
      ActionMailer::Base.delivery_method :smtp,
          host: 'localhost',
          port: 25

      # you can write like that on 1.9+
      ActionMailer::Base.delivery_method :smtp,
          host: 'localhost',
          port: 25
      ```

## BEGIN and END

- **[MUST]** Do not use `BEGIN` and `END` blocks.

## Module and Class definitions

- **[MUST]** Use `alias_method` instead of `alias` to define aliases of methods.
- **[MUST]** use `attr_accessor`, `attr_reader`, and `attr_writer` to define accessors instead of `attr`.
- **[MUST]** In definitions of class methods, use `self.` prefix of method name to reduce the indentation level, but it's OK to use `class << self` when you want to define both of public & private class methods.

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

- **[MUST]** In definitions of private or protected class methods, define these methods and change these visibilities in `class << self`/`end`.

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

- **[MUST]** If you use `private`, `protected`, and `public` with method name to change the visibilities of methods after definitions of the methods, do not put empty line between the definition of the method and these visibility-change methods.

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

- **[MUST]** If you use `private`, `protected`, and `public` without any arguments, align lines of these method calls to method definitions, and put empty lines around of the line of these visibility-change methods.

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

- **[SHOULD]** Use Markdown format to write documentation comments for public interfaces of modules and/or classes.
- **[MUST]** Do not put empty lines between documentation comments and the corresponding method definitions.
- **[MUST]** Follow <a href="http://tomdoc.org/" target="_blank">TomDoc</a> style to write documentation comments.
- **[MUST]** Do not give different responsibilities for a class.

## Method definitions

- **[MUST]** Do not omit parentheses of parameter list on method definition except for one without any parameters.
- **[MUST]** Do not put whitespaces between method name and the parameter list.
- **[SHOULD]** Do not write codes that is not understandable without any comment.
  - Prefer to divide a method into smaller methods which do not need comments to understand.
  - You may write comments for supplementaly infomation such as descriptions for variables and citations for equations.
- **[MUST]** Do not give different responsibilities for a method.
- **[MUST]** Do not destructively modify arguments (i.e. cause side-effects).

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

## Variables

- **[MUST]** Do not introduce new global variables (`$foo`) for any reasons.
- **[MUST]** Do not class variables (`@@foo`) for any reasons.  Use `class_attribute` instead.
- **[MUST]** Name variables with valid English words without shortenning.  If a variable name gets too long, you can shorten the variable name with removing vowel except for the heading character, and selecting well-known abbreviations.  Additionally, you can use conventional variables such as `i` and `j` for loop counters.
- **[SHOULD]** Do not give a variable for multiple use.
- **[SHOULD]** Minimize the scope of a local variable.  Methods without any local variables are prefered.

## Misc. (difficult to categorize)

- **[MUST]** Keep the side effect of destructive method to a minimum.
