# Basic Types

ObjectiveScript has high-fidelity support for runtime interoperability between JavaScript types and [Objective-]C types.

When a value is passed from C or Objective-C code to ObjectiveScript code, the runtime necessarily knows its C or Objective-C type. The value is then converted to a JavaScript representation based on various rules, while attempting to maintain maximum fidelity.

When a value is passed from ObjectiveScript code to C or Objective-C, the runtime similarly converts the type to the expected [Objective-]C type.

## Numbers

Numbers passed from Objective-C or C code into ObjectiveScript are converted to the JavaScript `number` type. When passing a JavaScript `number` from ObjectiveScript to Objective-C or C, the exact numeric type is inferred contextually.

The supported numeric types are:

- `[unsigned] char`
- `[unsigned] int`
- `[unsigned] short`
- `[unsigned] long [long]`
- `float`
- `double`

## Booleans

The Objective-C `BOOL` type is converted to a JavaScript `boolean`, and vice-versa.

## Strings

The Objective-C `SEL` type and C `char *` converted to JavaScript's `string` type, and vice-versa.

## Pointers, C Arrays, and Structs

See [Advanced Types](/advanced-types).

## Objective-C Objects

See [Objective-C Interoperability](/objc-interop).
