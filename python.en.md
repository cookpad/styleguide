# Python coding style

## Introduction

This guide defines the conventions for writing Python code at Cookpad Inc.

## General rules:

 - value readability and maintainability
 - keep it simple
 - be consistent in your style
 - for things not in this guide, be consistent with the style of the project you're working on

## Python version

 - **[SHOULD]** When possible, projects should use Python 3 syntax. Use of Python 2 should be motivated by external reasons, like dependencies or heavy use of legacy code.

## Language Features

🙌Avoid:

 - global variables

👋Use for simple cases (one line):

 - lambda functions
 - list comprehensions `[ingredient.name for ingredients in recipe]`
 - conditional expressions `sugar = 1 if 'milk' in coffee else 0`
 - nested/local/inner classes and functions

👍Use freely:

 - generators
 - default iterators and operators for types that support them

## Code style

### Lint

Run `pep8` on your code. In the project root, create `setup.cfg` with the following section:

```
[pep8]
max-line-length = 160
```

Lint your code before sending a pull request.

### Comments and docstrings

Comment and document your code for others to understand. Use best judgement when commenting: if anything may be hard to understand for someone reading the code for the first time, add a comment.

 - **[MUST]** Use simple, easy to understand English
 - **[SHOULD]** Use docstrings for modules
 - **[SHOULD]** Use docstrings for classes and methods that are accessible outside your module

### Quotation marks

Use double `"` quotation marks.

### Naming

 - `module_name`, `package_name`, `ClassName`, `method_name`, `ExceptionName`, `function_name`, `GLOBAL_CONSTANT_NAME`, `global_var_name`, `instance_var_name`, `function_parameter_name`, `local_var_name`.

### Imports

 - **[SHOULD]** `import`s should be in separate lines
 - **[SHOULD]** `import`s are always put at the top of the file, just after any module comments and doc strings and before module globals and constants. Imports should be grouped with the order being most generic to least generic.

