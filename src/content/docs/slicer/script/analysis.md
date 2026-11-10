---
title: Analysis
description: Overview of the AnalysisContext API.
tableOfContents: false
---

The `AnalysisContext` API allows you to adjust and complement the process of analysis.

## Mapping

The mapping API allows you to manipulate and export obfuscation mappings that slicer is currently holding onto and load new ones.

For further context on what mappings are, check out the [mapping documentation](/slicer/reference/analysis/mapping).

```js wrap
await context.analysis.mapping.load(data, src, dst); // loads mappings from the provided data (string), optionally from specific source and target namespaces (e.g. "intermediary", "named", "obfuscated", etc.); if src and dst are not provided, the same defaults as in the UI are used
await context.analysis.mapping.export(format); // exports the currently loaded mappings in a specified format (e.g. "tiny_v1", "tiny_v2, "tsrg", "proguard", etc.); returns the exported data as a string
context.analysis.mapping.clear(); // clears the currently loaded mappings, reverting all names to their original state
context.analysis.mapping.size(); // gets the number of currently loaded class mappings
```

## Characteristics

The characteristics API allows you to add custom types and groups of characteristics in addition to [the ones that slicer already has](/slicer/reference/structure/#characteristics).

```js wrap
context.analysis.ct.cts(); // gets all currently registered characteristic types
context.analysis.ct.register({
  id: "my-script/characteristic-type",
  label: "My amazing characteristic type", // optional, defaults to the ID
  description: "This is a custom characteristic type that my script has registered.", // optional
  group: "my-script/characteristic-group", // optional, defaults to the "Common characteristics" group
  icon: { type: "html", value: "<svg>...</svg>" }, // optional
});
context.analysis.ct.unregister("my-script/characteristic-type"); // unregisters a characteristic type by its ID

context.analysis.ct.groups(); // gets all currently registered characteristic groups
context.analysis.ct.registerGroup({
  id: "my-script/characteristic-group",
  label: "My amazing characteristic group", // optional, defaults to the ID
  description: "This is a custom characteristic group that my script has registered.", // optional
});
context.analysis.ct.unregisterGroup("my-script/characteristic-group"); // unregisters a characteristic group by its ID

context.analysis.ct.providers(); // gets all currently registered characteristic providers
context.analysis.ct.registerProvider({
  id: "my-script/characteristic-provider",
  async match(entry /*: Entry */) /*: string[] | Promise<string[]> */ {
    return ["my-script/characteristic-type"]; // returns an array of characteristic type IDs that this provider matched for the given entry
  },
});
context.analysis.ct.unregisterProvider("my-script/characteristic-provider"); // unregisters a characteristic provider by its ID
```
