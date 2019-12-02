# Usage

## In Existing Apps

### Supercharge

The Supercharge app allows you to modify other apps by injecting ObjectiveScript code into them. You can get it from <https://supercharge.app>.

## In Your Own App

### Overview

ObjectiveScript has two main components: the **transpiler** and the **runtime**. The transpiler allows the user to benefit from ObjectiveScript's syntactic sugar, converting their code into pure JavaScript which is compatible with the runtime.

The transpiler and runtime are provided as separate components. This is because baking the transpiler into the runtime would entail re-transpilation every time the code is executed. It is often beneficial to process the code with the transpiler once, and then execute the transpiled code whenever necessary.

### Transpiler

Transpilation is done using [objs-transpiler](https://github.com/kabiroberai/objs-transpiler). See the `README` for details.

### Runtime

The ObjectiveScript runtime can be found in the [ObjectiveScript](https://github.com/kabiroberai/ObjectiveScript) repository. See the `README` for details.

### Syntax Highlighting

In addition, ObjectiveScript offers basic support for [highlight.js](https://highlightjs.org). See [objs-highlight](https://github.com/kabiroberai/objs-highlight) for details.

Alternatively, you can use `OBJSTranspiler.tokenize` to produce tokens from the passed in source code. See [objs-transpiler](https://github.com/kabiroberai/objs-transpiler) for more info.
