---
title: Disassembler
description: Overview of the disassembler API.
tableOfContents: false
---

The disassembler API allows you to introspect and manipulate disassemblers in slicer. slicer offers several disassemblers/decompilers [out of the box](/slicer/reference/disasm), but this API makes it possible to add custom ones via a script.

All disassembler operations are done via a `DisassemblerContext`, which you can once again access on [the context](/slicer/script/#context).

```js wrap
context.disasm.all(); // lists all disassemblers
context.disasm.find("vf"); // finds a disassembler by its ID, returns null if not found; this example returns the Vineflower decompiler

// these operations do NOT persist and work only within the current instance of slicer
context.disasm.add({ ... }); // adds a custom disassembler
context.disasm.remove("vf"); // removes a disassembler by its ID
```

Every disassembler needs an ID and a `class` function that disassembles a class file byte array into a string representation.
Optionally, a `method` function for disassembling a single method and human-readable label can also be supplied.

A disassembler can request class files from the workspace based on their internal names using the supplied `source` function.

:::tip

If your disassembler outputs Java source code, add a `language: "java"` property to enable Java syntax highlighting!

_This property directly maps to slicer's internal language ID representation, possible values can be found [here](https://github.com/katana-project/slicer/blob/main/src/lib/lang/index.ts#L3)._

:::

```js wrap
const myDisasm /*: Disassembler */ = {
  id: "my-disasm",
  label: "My disassembler", // optional
  version: "1.0.0", // optional
  language: "java", // optional
  options: {}, // a string-to-string mapping of applied disassembler options, modified by slicer and scripts, optional

  async class(
    name /*: string */, // an internal name of the disassembled class, i.e. com/example/Main
    source /*: (name: string) => (Uint8Array | null) | Promise<Uint8Array | null> */,
    resources /*: string[] */ // an array of fully qualified class names that may be requested from the source
  ) /*: string | Promise<string> */ {
    const data /*: Uint8Array | null */ = await source(name);
    if (!data) return ""; // this should never happen

    const myOption = this.options?.["my-option"];
    // disassembler logic goes here
  },
  // optional
  async method(
    name /*: string */, // an internal name of the disassembled class, i.e. com/example/Main
    signature /*: string */, // a method name and descriptor joined together, i.e. main([Ljava/lang/String;)V
    source /*: (name: string) => (Uint8Array | null) | Promise<Uint8Array | null> */,
    resources /*: string[] */ // an array of fully qualified class names that may be requested from the source
  ) /*: string | Promise<string> */ {
    const data /*: Uint8Array | null */ = await source(name);
    if (!data) return ""; // this should never happen

    const myOption = this.options?.["my-option"];
    // disassembler logic goes here
  },
};

export default {
  // ...
  load(context /*: ScriptContext */) {
    context.disasm.add(myDisasm);
  },
  unload(context /*: ScriptContext */) {
    context.disasm.remove(myDisasm.id);
  },
};
```

:::danger

Disassemblers are run on the main thread; it is **the script's responsibility** to make sure the process doesn't have a measurable impact on the responsiveness of the UI.

If it does, it is recommended to off-load blocking calls to a [web worker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers).

:::
