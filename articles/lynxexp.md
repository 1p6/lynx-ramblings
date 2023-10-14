# LynxExp Overview

This doc will serve as an overview of the language I'll use throughout this repo for notating various ideas. It is essentially a functional programming language with a syntax similar to C. As of now I'm calling it LynxExp (short for Lynx Expressions), though I may come up with a better name later.

TODO: As of now this article lacks an overall structure, and is mostly just me fleshing out various ideas for the language.

## Function Notation

Functions are defined like so:
```
f(x, y, z) = x+y+z;
g(a) = 2;
```
All functions actually take a single argument; functions that take more than one are automatically curried. So the following are equivalent:
```
f(x, y, z) = x+y+z;
f(x)(y)(z) = x+y+z;
```
The assignment operator looks at the innermost variable name nested inside function calls to determine what variable will be modified in the surrounding scope. Other expressions besides function calls can occur to the left of the assignment operator, such as type annotations.

## The Semantics Of Functions And Records
All non-type values in the language are actually records, which are mappings from keys (strings) to other values and types. Records can match the same string to multiple different values, in which case the order matters, and the most recent definition will take precedence and override previous ones. However, if a type error occurs when using the first match, previous matches will be tried in order until either one type checks or they all fail. This also applies to variables themselves, because there is an implicit "global" record that all variables are stored in. The order of which records are checked when a type error occurs is well-defined. All together, this allows for overloaded functions in the language, similar to in C++.

`f = x` erases all previous values associated with `f`, while `f += x` adds an additional value without erasing the previous ones. `f += x` still works even if there were no previous matches for `f`, in which case `x` gets added as the sole current match.

The notation extends for defining functions:
```
f(x, y, z) = 4;
// This overrides the definition of f while
f(x, y, z) += apple;
/* just adds an additional entry. At this point, f can be used either as a function returning numbers, or as a function returning fruits, and the language automatically selects which one based on what satisfies the needed type. */
```

## Records
Records are accessed similar to how struct's work in C, how objects work in JavaScript, etc. `f.x = 3` sets the value of the `x` member of `f` to `3`, while `f.x += 3` retains previous matches, adding a new match to `f`'s `x` entry.

## Type System
Type annotations work can appear in two different ways:
```
x: int;
// and
y :: int;
```
The difference between them is that `::` works similar to `=` in the sense of allowing for function types to be defined. The left hand of `::` can be exactly the same expressions as the left hand of `=`. The overall value of the `::` expression is the type of the innermost variable on the left hand side, while the right side gives the return type. For example:
```
// This expression's value is the type of functions from ints to ints:
(f(x: int) :: int);

// The innermost variable name does not matter in (::) expressions
```
In addition, one of the built-in function-like operators, `ignore`, allows one to type check an expression without evaluating it, it has no return value, and as such must occur in a spot where an expressions value is ignored. (The reason why this is needed is because of side-effects.)

The types of records are also records, with each of the elements of the record replaced by its type. This includes all possible entries under the same string key.

In addition, there is a notion of subtyping: `f(x: A) :: B` is a subtype of `g(x: C) :: D` iff `B` is a subtype of `D` and `C` is a subtype of `A`. (This is the same as how function subtypes are usually defined.)

TODO: Figure out how record subtyping should work, since ideally union types are allowed too.