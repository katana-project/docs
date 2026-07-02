---
title: Transformers
description: Reference of analysis transformers.
---

Transformers are a convenient way to _transform_ class files before they're analyzed (just-in-time).
They can be applied in the `Analysis` -> `Transformers` menu.

slicer includes several options that may improve the chances of successful disassembly when dealing with obfuscated code.

:::note

The scripting API `preload`/`transform` [event](/slicer/script/event) is also implemented as a transformer, and it is run as the _last_ transformer in the pipeline.

:::

## Readability

Readability transformers perform **destructive** transformations, which may help with readability of decompiled output in particular.

| Name                      | Description                                                                                                        |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Strip annotations         | Removes annotation-related attributes (`*Annotations`, `AnnotationDefault`), useful for mitigating "ASM crashers". |
| Strip try-catches         | Removes exception table entries in `Code` attributes, useful when dealing with flow obfuscation.                   |
| Strip local variables     | Removes `LocalVariable(Type)Table` and `MethodParameters` attributes, useful when dealing with name obfuscation.   |
| Strip synchronized blocks | Replaces all `monitorenter` and `monitorexit` instructions with `pop` instructions.                                |
| Strip generic signatures  | Removes `Signature` attributes, useful when dealing with name obfuscation.                                         |
| Strip debug information   | Removes `Deprecated`, `SourceFile`, `SourceDebugExtension` and `LineNumberTable` attributes.                       |

## Normalization

Normalization transformers perform functionally equivalent transformations, which mitigate common obfuscation techniques.

| Name                           | Description                                                                                                               |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| Verify attributes              | Attempts to selectively strip attributes containing garbled data, mainly intended to mitigate "ASM crashers".             |
| Remove unnecessary modifiers   | Removes `ACC_SYNTHETIC` and `ACC_BRIDGE` access modifiers where appropriate, useful when dealing with access obfuscation. |
| Remove unnecessary try-catches | Removes exception table entries with nonsense ranges and/or handlers that only rethrow the caught exception.              |
| Remove unused local variables  | Replaces local variable store instructions with `pop` instructions if the variables are never read.                       |
| No-op unreachable code         | Replaces unreachable code with `nop` instructions.                                                                        |

### Verification

The verification algorithm used in the `Verify attributes` transformer modifies the class file in an attempt to make it
readable by bytecode libraries that were not designed with JVMS violations in mind.

Violations of the class file specification are usually a product of an obfuscator attempting to thwart analysis, as in
many cases, these class files are still readable by JVMs.

The transformer does the following:

- remove attributes that couldn't be parsed
- remove attributes defined in an invalid context (e.g. `Code` attribute on an abstract method)
- remove attributes with invalid constant pool references (index out of bounds, wrong entry type)
