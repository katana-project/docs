---
title: Introduction
description: Introduction to the slicer scripting API.
---

<style>
  table td {
    vertical-align: middle;
  }

  /* force first column to max badge width */
  table tr > :first-child {
    width: 200px;
  }
</style>

slicer features a simple JavaScript scripting API, useful for doing preprocessing on class files, e.g. deobfuscation.

Every script must default export two essential properties, `load` and `unload` functions, however it can also expose metadata to display to the user.

```js
export default {
  name: "example script", // optional
  description: "An example slicer script in JS.", // optional
  version: "1.0.0-SNAPSHOT", // optional
  load() /*: void | Promise<void> */ {
    console.log("Loaded!");
  },
  unload() /*: void | Promise<void> */ {
    console.log("Unloaded!");
  },
} /* satisfies Script */;
```

:::tip

TypeScript type definitions for the API are available [here](https://github.com/katana-project/script).

Scripts are meant to be fairly simple, but they can also be pretty extensive, so for convenience, a TypeScript+Rollup example project is provided [here](https://github.com/katana-project/script-template).

:::

:::tip

Scripts can be shared using a `script` URL parameter, the user will be prompted about importing when slicer loads.

For example, `https://slicer.run?script=https://a-cdn.com/my-slicer-script/index.js` imports a script from `https://a-cdn.com/my-slicer-script/index.js`.

A set of stylized buttons is provided for linking to scripts, akin to GitHub repository badges or the Vercel deploy button.

| Button                                          | URL                                          | Markdown                                                                           |
| ----------------------------------------------- | -------------------------------------------- | ---------------------------------------------------------------------------------- |
| ![](https://slicer.run/button-import-small.svg) | `https://slicer.run/button-import-small.svg` | `[![](https://slicer.run/button-import-small.svg)](https://slicer.run?script=...)` |
| ![](https://slicer.run/button-import-large.svg) | `https://slicer.run/button-import-large.svg` | `[![](https://slicer.run/button-import-large.svg)](https://slicer.run?script=...)` |

:::

## Context

Every script is assigned a "script context", which provides them with the power to manipulate with slicer itself - you will need it for basically everything.

This context is always passed into `load` and `unload`, where you can access it:

```js
export default {
  name: "example script",
  // ...
  load(context /*: ScriptContext */) {
    console.log("Loaded!");
    console.log("That's us: ", context.script.name);
  },
  unload(context /*: ScriptContext */) {
    console.log("Unloaded!");
  },
};
```

Each context also has a parent context (`context.parent`), in most cases it will be the "root context".

The root context represents the slicer scripting engine itself, you can identify it by the script name (`rootContext.script.name === "slicer"`) or the fact that it has no parent - it is the root after all (`rootContext.parent === null`).

From the context, a myriad of different APIs are accessible for interacting with slicer and its interface:

- [`context.editor`](/slicer/script/editor) for interacting with or spawning editor tabs
- [`context.workspace`](/slicer/script/workspace) for modifying entries inside slicer's workspace
- [`context.disasm`](/slicer/script/disasm) for invoking or registering decompilers/disassemblers
- [`context.mapping`](/slicer/script/mapping) for loading, applying, and un-applying memory obfuscation mappings
- [`context.i18n`](/slicer/script/i18n) for localizing your scripts with native formatting options
- [`context.dispatchEvent`, `context.addEventListener`, ...](/slicer/script/event) for participating in slicer's event bus machinery
