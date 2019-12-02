# Environment

The ObjectiveScript execution environment is built on top of JavaScriptCore. 

## Global Variables

### global

This is the global object, similar to `window` in a DOM context.

### NSLog

```js
function NSLog(message: string): void
```

A wrapper around Foundation's NSLog function to enable simple logging.

## Special Properties

While the global environment behaves mostly like a regular JavaScript environment, there are a few special getter and setter cases. These are explained in later sections.
