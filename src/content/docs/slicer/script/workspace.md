---
title: Workspace
description: Overview of the workspace manipulation API.
tableOfContents: false
---

The workspace API allows you to manipulate file entries that slicer is currently holding onto (i.e. files in the `Project` tab).

Entries can be looked up and CRDed using the `WorkspaceContext` API available in [the context](/slicer/script/#context).

```js
context.workspace.entries(); // lists all entries in the workspace
context.workspace.find("..."); // tries to find an entry by its name, returns null if not found
context.workspace.remove("..."); // removes an entry by its name
context.workspace.clear(); // removes all entries in the workspace

// adds an entry with a specified name
// the data must be either an Uint8Array or a Blob
// returns the created entry or an already-existing entry with the same name
await context.workspace.add("...", data);
```

Each entry has an associated unique name, an internal type and data reading functions:

```js
{
    type: "file", // "file", "class", "archive", "memory", ...
    name: "sample/math/BinarySearch.class", // the entry name
    meta: { /* ... */ }, // non-essential metadata
    bytes: async function () { /* ... */ }, // gets the entry data as an Uint8Array
    blob: async function () { /* ... */ }, // gets the entry data as a Blob
}
```

## Metadata

The `meta` object provides you with non-essential metadata, such as the last modification date, entry size
and information about the entry if it comes from an archive (e.g. a ZIP/JAR; uncompressed size, compression method, CRC, ...).

There are different types of metadata objects, delimited by a `type` identifier; their structure can be viewed in the
[TypeScript type definitions](https://github.com/katana-project/slicer/blob/main/script/index.d.ts) for the scripting API.
