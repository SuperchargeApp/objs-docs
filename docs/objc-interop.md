# Objective-C Interoperability

Several features exist to enable seamless interoperability between Objective-C and ObjectiveScript code.

## Global Class Resolution

It's extremely easy to access an Objective-C class in ObjectiveScript. Just access the class name in the global scope. E.g. `NSString` (or `global.NSString`) will yield the `NSString` class.

## Objects

Objective-C objects and classes are wrapped in `JXObject` when being passed to ObjectiveScript code. This wrapper adds a few bits of functionality.

### Method Calling

Methods can be called on `JXObject` using Objective-C like syntax. E.g. `[tableView reloadData]` or `[NSString new]`.

### Dot Syntax

Just like in Objective-C, methods can also be called using dot syntax, for example `NSString.new` (note the lack of parentheses).

In addition, properties can be read and written to using dot syntax as well.

```js
if (!window.rootViewController) {
    window.rootViewController = [UIViewController new];
}
```

### Boxing

When passing a pure JavaScript type to [Objective-]C code that expects an `id`, the following automatic boxing occurs

- `null`: converted to `nil`
- `string`: converted to `NSString`
- `number`, `boolean`: converted to `NSNumber`
- `Date`: converted to `NSDate`
- `Array`: deep-converted to `NSArray` (i.e. elements are also boxed)
- `Object`: deep-converted to `NSDictionary` (only enumerable keys)

It's possible to manually box a JavaScript type into its Objective-C equivalent using `@(<value>)`. It's also possible to use `@<literal>` in the case of string, numeric, boolean, array, and object literals.

### Unboxing

The ObjectiveScript runtime attempts to respect the type system's requests to convert Objective-C objects to strings and numbers via `Symbol.toPrimitive`, by returning the type's `description`. Additionally, it is possible to manually unbox an Objective-C object into a high fidelity JavaScript counterpart using `unbox`.

```ts
function unbox(object: JXObject): any
```

The following rules are applied:

- `NSString`: converted to `string`
- `NSNumber`: converted to `number` or `boolean`
- `NSDate`: converted to `Date`
- `NSArray`: deep-converted to `Array`
- `NSDictionary`: deep-converted to `Object`

Other classes are simply passed through.

## Blocks

It's possible to declare a block in ObjectiveScript using an Objective-C like block syntax.
```objc
^<return-type>(params) { /* code */ };
```

For example
```objc
[UIAlertAction actionWithTitle:"Confirm" style:1 handler:^(id action) {
    // do stuff
}];
```

## Defining Classes

One can define classes purely in ObjectiveScript. The syntax for this is

```objc
@class <class name> : <superclass name>
// methods
@end
```

It's also possible to register instance variables. These are internally implemented as associated objects at the moment, so they must be `id`s for now.

```objc
@class <class name> : <superclass name> {
    id <name>;
    ...
}
// methods
@end
```

Furthermore, a class can declare conformance to protocols if necessary, using Objective-C like syntax. For example

```objc
@class MyViewController : UIViewController <UITableViewDelegate, UITableViewDataSource> {
	id _objects;
}
// methods
@end
```

## Defining Methods

Methods are defined inside classes, using Objective-C like syntax.

```objc
- (<return type>)methodWithNoArgs { /* code */ }
- (<return type>)methodWithOneArg:(<type>)argOne { /* code */ }
- (<return type>)methodWithFirstArg:(<type>)argOne 
                          secondArg:(<type>)argTwo /* and so on */ { /* code */ }
```

During a method call, the runtime gives the code access to the callee `JXObject` via `self`. It's also possible to call superclass methods using the `super` keyword. For example,

```objc
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    // do stuff
}
```

A gotcha of the way `self` is implemented is that it's set globally upon entering a user-defined method, and unset upon exiting it. Therefore asynchronous blocks executed in the method won't be able to access `self` even though it looks like it's inside the correct scope. To work around this, save `self` to a local variable, which can later be accessed inside the block.

## Hooking Classes

One can swizzle methods of an existing class using syntax similar to that used to create a class. 

Similar to [Logos](https://github.com/theos/logos), one first sets up a `%hook` for the class.

```objc
%hook <class name> /* { id _associatedObj; } */
// methods
%end
```

Any methods inside the hook matching an existing method on the class will be replaced. Methods that do not match an existing one will be added to the class.

### %orig

It's possible to call `%orig` from within a user-defined method hook to invoke the original method. When `%orig` is called without parentheses (`%orig;`), all arguments are passed through to the method. This doesn't mean that `%orig;` disallows changing the arguments' values though; if you change an argument variable inside the method, it'll reflect in the value that `%orig;` passes through. For example, the following snippet will make it so that `-[UILabel setText:]` always receives the string "hi".

```objc
%hook UILabel
- (void)setText:(id)text {
    text = "hi";
    %orig;
}
%end
```

If you wish to pass values other than `arguments` to the original method, invoke `%orig` with parentheses, passing in the desired values. For example, the following is functionally equivalent to the previous example.

```objc
%hook UILabel
- (void)setText:(id)text {
    %orig("hi");
}
%end
```

It's also possible to not call `%orig` at all, which makes it so that the original method is never called.

### orig

`orig` is the function which `%orig` uses as its underlying implementation. The difference between the two is that `orig` expects an _array_ of arguments, while `%orig` expects them to be supplied in a variadic manner.

```ts
function orig(args: any[]): any
```

In asynchronous code, `%orig` faces a similar issue to `self`. In such a situation, save `orig` to a local variable and pass it an array of the desired arguments inside the block. To replicate the behavior of `%orig;`, use `orig(arguments)`.
