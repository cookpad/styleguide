java.ja.md

# 目次

- Java全般のルール
 - [インポート](#imports)
 - [インデント](#indentation)
 - [空白](#whitespace)
 - [空行](#empty_lines)
 - [改行](#new_lines)
 - [中括弧](#braces)
 - [1行の長さ](#line_columns)
 - [名前に置ける頭字語（アクロニム）](#acronyms)
 - [TODOのスタイル](#todo)
 - [配列](#arrays)
- Androidのルール
 - [フィールド名](#field_names)
 - [リソース](#resources)


# Java コーディング規約

## はじめに

本文書は、クックパッド株式会社での Java コードのスタイル規準を定めるものである。

基本はOracleのスタイルを採用する。[Code Conventions for the Java TM Programming Language](http://www.oracle.com/technetwork/java/javase/documentation/codeconvtoc-136057.html)
次いでGoogleのガイドラインも参照する。 [Code Style Guidelines for Contributors](http://source.android.com/source/code-style.html)

基本的には見やすさ、美しさ、を重視するが、パフォーマンスに影響がある場合はその限りではない。


## Java 全般のルール

<a name="imports"></a>

### インポート

- **[MUST]**不要なimport文は削除すること。
- **[MUST]**可能な限り * での import 文は書かないこと。

<a name="indentation"></a>

### インデント

- **[MUST]**ブロックのインデントにはスペース4つを使うこと。
- **[MUST]**関数呼び出しと引数を含んだ行の折り返しは、スペース8つでインデントすること。

```java
// good
Instrument i
        = someLongExpression(that, wouldNotFit, on, one, line);

// bad
Instrument i
    = someLongExpression(that, wouldNotFit, on, one, line);
```

<a name="whitespace"></a>

### 空白

- **[MUST]**行末に空白を置いてはならない。
- **[MUST]**カンマ、セミコロン、キャスト演算子の直後には空白を置くこと。

<a name="empty_lines"></a>

### 空行

- **[MUST]**ファイルの末尾に空行を置いてはならない。

<a name="new_lines"></a>

### 改行

- **[MUST]**ファイルの末尾は空白を置いてはならない。
- **[MUST]**行の途中で改行するときには演算子の直前で改行すること。

<a name="braces"></a>

### 中括弧

- **[MUST]**直前にトークンがある場合は中括弧で行を始めてならない。中括弧はそのトークンと同じ行に置くようにすること。

```java
// good
class MyClass {
    int func() {
        if (something) {
            // ...
        } else if (somethingElse) {
            // ...
        } else {
            // ...
        }
    }
}

// bad
class MyClass
{
    int func() {
        if (something) {
            // ...
        }
        else if (somethingElse) {
            // ...
        }
        else {
            // ...
        }
    }
}
```

- **[MUST]**条件文には中括弧を付けること。

```java
// good
if (condition) {
    body();
}

// bad
if (condition)
    body();
```

<a name="line_columns"></a>

### 1行の長さ

- **[MUST]**どんな事情があろうとも1行の文字数が128文字を越えてはならない。

<a name="acronyms"></a>

### 名前における頭字語 (アクロニム)

- **[MUST]**頭字語や略語は単語として扱うこと。

```java
// good
XmlHttpRequest
getCustomerId

// bad
XMLHTTPRequest
getCustomerID
```

<a name="todo"></a>

### TODOのスタイル

- **[MUST]**TODOにはすべて大文字のTODOにコロンを続けたものにすること。

```java
// TODO: Remove this code after the UrlTable2 has been checked in.
// TODO: Change this to use a flag instead of a constant.
```

<a name="arrays"></a>

### 配列

- **[MUST]**配列のブラケットは型のすぐ後ろに書かなければならない。

```java
// good
int[] array = new int[10];
int[][] array = new int[10][10];

// bad
int array[] = new int[10];
int array[][] = new int[10][10];
int[] array[] = new int[10][10];
```

## Androidのルール

<a name="field_names"></a>

### フィールド名

- **[MUST]**フィールドにプレフィックスは付けないものとする。

```java
// good
public class MyClass {
    private static MyClass singleton;
    int packagePrivate;
    private int private;
    protected int protected;
}


// bad
public class MyClass {
    private static MyClass sSingleton;
    int mPackagePrivate;
    private int mPrivate;
    protected int mProtected;
}
```

<a name="resources"></a>

### リソース

- **[MUST]**リソースのファイル名はスネークケースにすること。
