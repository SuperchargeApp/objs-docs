# Advanced Types

## Pointers

When a pointer (besides `char *`) is passed to ObjectiveScript code, the runtime wraps it in a `JXPointer` type. 

### Dereferencing

A pointer's underlying value can be accessed using ObjectiveScript's **dereference operator**. The syntax for this is `*pointer`, where pointer is the `JXPointer` that you wish to dereference.

In order to correctly dereference the pointer, the runtime needs to know the type of the underlying value. In some situations (such as during some Objective-C method calls), the runtime might not be able to infer this itself. Therefore, unless you have explicitly declared a function's signature (read on for more on functions), you should use `@cast` to convert the pointer to the correct type before dereferencing it. The syntax for this is

```js
@cast(<type>)pointer
```

Refer to [Type Syntax](/type-syntax) for the syntax of `<type>`.

### pointee

```ts
JXPointer<Pointee>.pointee: Pointee
```

A property on `JXPointer` which returns the underlying value. Equivalent to dereferencing the pointer.

### advancedBy

```ts
JXPointer.advancedBy(offset: number): JXPointer
```

Advances the pointer by `offset` times the size of `pointee` (similar to `this + offset` in C).

### distanceTo

```ts
JXPointer.distanceTo(other: JXPointer): number
```

Returns the distance from `other` to this pointer (similar to `other - this` in C).

**Precondition**: `other` must have the same pointee type as the callee.

### Subscripting

A `JXPointer` can be subscripted with a numerical index. 

```js
pointer[offset] === pointer.advancedBy(offset).pointee
```

## Arrays

In most situations, C "arrays" are syntactic sugar for pointers. When an array is contained in a struct, however, `type[n]` is equivalent to `n` values of `type` stored contiguously within the struct. Accordingly, accessing an array on a `JXStruct` returns a `JXArray` type. 

`JXArray` is a subclass of `JXPointer`, and if you were to pass it directly to a function it would be equivalent to passing a pointer to the array. The key difference is that `JXArray` also has a `length` property. In combination with the subscript operator which already exists on `JXPointer`, this makes `JXArray` "Array-like".

## Structs

ObjectiveScript converts C structs to the `JXStruct` type.

**Note**: If a struct's member types are unknown while dereferencing a pointer, the runtime does not have adequate information to create the `JXStruct` and will thus crash. You can specify the struct's type by `@cast`ing the pointer.

### Accessing Fields by Index

A guaranteed way to access a `JXStruct`'s fields is via numeric subscripting. To get a field's value, simply subscript the struct with the field's index. For example, `person[0]` returns the first element of the struct referenced by the `person` variable.

### Accessing Fields by Name

If the runtime has extended type information about a struct, it's possible to subscript via the field name using simple dot syntax (e.g. `person.name`).

### Extended Type Information

The runtime tries to locate extended type information about a struct when it's passed to ObjectiveScript. There are a few ways in which this is done.

- Some Objective-C methods have the extended type information present in their encoding. The runtime attempts to locate and use this type information.
- Other times, the struct's type information has the struct's name but not the field names. In such a situation, one can use `@struct` at the top level to define the field names of a struct, and these will be looked up at runtime.
- When manually declaring an Objective-C method, C function, symbol, etc, the transpiler requires that any structs involved have their full definition provided at the top-level with `@struct`.
- When `@cast`ing to a struct, the full definition must be provided at the top-level with `@struct`.

See [Type Syntax](/type-syntax#structs) for the `@struct` syntax.

## Functions

ObjectiveScript code can call C functions. The function prototype must first be declared, however. This is done using the `@function` keyword.

```
@function <type> <name>(<type>, <type>, <more types...>);
```

Additionally, a function can be made variadic by adding `...` to the end of the argument list.

A few examples of `@function` are:

```c
@function void *calloc(unsigned long, unsigned long);
@function int strcmp(char *, char *);
@function int printf(char *, ...);
```

Functions declared with `@function` are called just like normal JavaScript functions. 

```js
strcmp("hello", "hello") === 0 // true
```

Note that when calling a variadic function, while the type system tries to infer as much type information as it can, some fidelity is lost due to JavaScript's weaker type system. This can be resolved by `@cast`ing the variable to the desired type. For example,

```js
printf("One plus one equals %u\n", @cast(unsigned int)(1 + 1));
```

## Symbols

ObjectiveScript has the ability to access symbols which are accessible to `dlsym`. Use the following syntax to declare the symbol:

```c
@extern <type> <name>;
```

For example,
```c
@extern char **environ;
```

After this, you can treat the symbol like a normal writeable JavaScript global variable.

```js
NSLog(environ[0])
```

Additionally, it is also possible to get the address of the symbol itself. This is done using the **address-of operator**, `&`. For example, `&environ` yields a `JXPointer` to `environ`. Note that this is currently the only supported use case for the address-of operator in ObjectiveScript.

## Miscellaneous Utilities

### @encode

```
@encode(<type>)
```
Returns the Objective-C type encoding for `<type>`. Similar to the `@encode` compiler directive in Objective-C.

### @sizeof

```
@sizeof(<type>)
```
Returns the size of `<type>`. Similar to `sizeof` in C.
