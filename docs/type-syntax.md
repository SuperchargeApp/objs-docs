# Type Syntax

In many places throughout the ObjectiveScript documentation, you'll find mentions of `<type>`. This represents an [Objective-]C type which is understood by the ObjectiveScript transpiler. 

## Overview

The syntax for ObjectiveScript types is pretty similar to standard [Objective-]C type syntax, with a few differences.

- All Objective-C objects are represented by `id`
- There are no type annotations
- No support for unions
- Structs must be declared at the top-level with `@struct`. See [Advanced Types](/advanced-types#structs).

## Primitives

The following "primitive" types are supported verbatim

- `[unsigned] char`
- `[unsigned] int`
- `[unsigned] short`
- `[unsigned] long [long]`
- `float`
- `double`
- `BOOL`
- `SEL`
- `id`
- `Class`

## Void

`void` is supported as a return type on C functions and Objective-C methods.

## Pointers

To declare a pointer type, append `*` to the type. For example, `BOOL *`.

## Structs

Structs must first be defined at the top level with the following syntax

```
@struct <name> {
    <type> <name>;
    ...
};
```

For example,

```c
@struct CGSize {
	double width;
	double height;
}
```

Following this, they can be referenced as types using the syntax `struct <name>`, e.g. `struct CGSize`.

## Arrays

C Arrays can be declared by appending `[<length>]` to the type. For example, `int[5]`.
