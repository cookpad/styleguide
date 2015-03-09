# Table of Contents

- [Style for Java](#style-for-java)
 - [Imports](#imports)
 - [Indentation](#indentation)
 - [Whitespaces](#whitespaces)
 - [Where to break](#where-to-break)
 - [Braces](#braces)
 - [Acronyms](#acronyms)
 - [TODO](#todo)
 - [Arrays](#arrays)
- [Style for Android](#style-for-android)
 - [Field names](#field-names)
 - [Resources](#resources)

# Style for Java

## Introduction

This document defines the conventions for writing Java code at Cookpad Inc.
These regulations are formed with respect to readability and maintainability, such that developers familiar with Java can easily read the code.
To ensure readability and consistency within the code, the guide presents a number of rules to follow. Some of these rules must be followed, while others are simply a recommendation for the developer.

## Imports

- **[MUST]** Remove unused imports.
- **[MUST]** Don't use wildcard imports except test.

## Indentation

- **[MUST]** Use 4 space indents for blocks. Never use tabs.
- **[MUST]** Use 8 space indents for line wraps, including function calls and assignments.

```java
// good
Instrument i
        = someLongExpression(that, wouldNotFit, on, one, line);

// bad
Instrument i
    = someLongExpression(that, wouldNotFit, on, one, line);
```

## Where to break

- **[MUST]** When a line is broken at a non-assignment operator the break comes before the symbol.

```java
Map<long parameterization> map
        = new HashMap<ditto>();

String longString = "some long text"
        + " some more long text";
```

## Braces

- **[MUST]** Don't start line with braces. They must at the end of their previous line.

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
    int func()
    {
        if (something)
        {
            // ...
        }
        else if (somethingElse)
        {
            // ...
        }
        else
        {
            // ...
        }
    }
}
```

- **[MUST]** Don't omit braces from conditional statements.

```java
// good
if (condition) {
    body();
}

// bad
if (condition)
    body();
```

## Acronyms

- **[MUST]** Treat acronyms and abbreviations as words in naming variables, methods, and classes.

```java
// good
XmlHttpRequest
getCustomerId

// bad
XMLHTTPRequest
getCustomerID
```

## TODO

- **[MUST]** Use TODO comments for code that is temporary, a short-term solution, or good-enough but not perfect.
- **[MUST]** TODOs must include the string TODO in all caps, followed by a colon

```java
// TODO: Remove this code after the UrlTable2 has been checked in.
// TODO: Change this to use a flag instead of a constant.
```

## Arrays

- **[MUST]** Square brackets form a part of type, not a variable.

```java
// good
int[] array = new int[10];
int[][] array = new int[10][10];

// bad
int array[] = new int[10];
int array[][] = new int[10][10];
int[] array[] = new int[10][10];
```

# Style for Android

## Field names

- **[MUST]** Field names don't need prefix.

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

## Resources

- **[MUST]** Resource file names and resources ids should be snake case.
 - Although Android style forces these prefixes, we don't take it because (1) Oracle standard doesn't have these conventions, and (2) we prefer simpler one if we must chose one of things.
