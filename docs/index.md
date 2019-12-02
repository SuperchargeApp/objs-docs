# Overview

## Introduction

**ObjectiveScript** is a powerful superset of JavaScript which enables extensive interaction with C and Objective-C code.

Amongst the various features supported, a few highlights are

- **Objective-C**
    - First-class swizzling support similar to that of [Logos](https://github.com/theos/logos/wiki)
    - Method calling using Objective-C syntax
    - Creating new Objective-C classes
- **C**
    - Support for calling pure C functions, including variadic ones
    - Built-in interaction with C structures
    - Pointer support
- **General**
    - Powerful type inference and hinting system
    - Support for declaring and calling blocks

## Motivation

At first glance, it might seem redundant to create a new programming language to interact with C and Objective-C; what's wrong with directly writing a program in Objective-C?

The primary motivation boils down to a simple fact: **JavaScript can be interpreted at runtime**.

This opens up a wide range of possibilities, such as

- Situations where bundling a full-fledged compiler would be too bulky or slow
- Avoiding the need for code-signing while executing code on systems where it's traditionally mandatory
- Dynamic code execution in cases where remapping pages as executable is disallowed

Example use cases include

- An app which allows users to build runtime modifications (tweaks) for other iOS apps without needing to [jailbreak](https://en.wikipedia.org/wiki/IOS_jailbreaking) or use a computer
- An on-device IDE for coding native iOS apps
- A REPL for debugging apps at runtime in a syntax more familiar to developers than that of LLDB.

The initial motivation for building ObjectiveScript was the first of these examples, and you can download the preview version right now: [Supercharge](https://supercharge.app).
