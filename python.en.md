# Python coding style

## Introduction

This guide defines the conventions for writing Python code at Cookpad Inc.

## Python version

- **[SHOULD]** When possible, projects should use Python 3 syntax. Use of Python 2 should be motivated by extenrnal reasons, like dependencies or heavy use of legacy code.

## Language Features

🙌Avoid:

- global variables

👋Use for simple cases (one line):

- lambda functions
- list comprehensions `[ingredient.name for ingredients in recipe]`
- conditional expressions `sugar = 1 if 'milk' in coffee else 0`

👍Use freely:

- generators
- default iterators and operators for types that support them

## Code style

### Lint

Run `pep8` on your code.

### Indentation

Indent code blocks with 4 spaces.

### White space

- **[MUST]** Spaces before and after operators, except in keyword arguments or parameter values.
- **[MUST]** Single space after comma, no space before

```python
def sample_function(x, y=0):
    y = y + x
    if x == 4:
        print(x, y)
```

- **[SHOULD]** Two blank lines between top level definitions, one blank line between method definitions
- **[SHOULD]** Avoid semicolons
- **[SHOULD]** Keep line length up to 80 characters
- **[SHOULD]** Two spaces before `#` character in in-line comments, one space after
- **[SHOULD]** end your files with a single newline
- **[SHOULD]** Avoid using whitespace to vertically align tokens: it makes the code hard to maintain

```python
foo = 1000  # comment
long_name = 2  # comment that should not be aligned

dictionary = {
    'foo': 1,
    'long_name': 2
}
```

### Naming

- `module_name`, `package_name`, `ClassName`, `method_name`, `ExceptionName`, `function_name`, `GLOBAL_CONSTANT_NAME`, `global_var_name`, `instance_var_name`, `function_parameter_name`, `local_var_name`.

### Imports

- **[SHOULD]** imports should be in separate lines
- **[SHOULD]** imports are always put at the top of the file, just after any module comments and doc strings and before module globals and constants. Imports should be grouped with the order being most generic to least generic:

