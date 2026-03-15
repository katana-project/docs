---
title: Mapping
description: Reference of mapping capabilities.
sidebar:
  badge:
    text: Beta
    variant: note
---

slicer allows you to load obfuscation mappings in Tiny v1/v2, (T/C)SRG and ProGuard formats, which are then applied to all relevant parts of the UI, such as the class view and disassembly.
You can also remove loaded mappings to revert the changes, or load a different mapping to replace the previous one.

Some mapping formats also include local variable mappings and comments, these are not supported and will be ignored when loading.

:::note

Mappings do not change the actual file structure, so the "Files and directories" viewing mode will not be affected by them. If you want to traverse the workspace with mappings applied, use the "Classes and packages" mode or the command palette search instead.

**This requires background analysis to be enabled**, as the mappings are applied during analysis, so make sure to have it enabled if you want to see mappings outside a disassembly.

:::

## Loading mappings

Mappings can be loaded via the `Mapping` -> `Load` menu option, which accepts a file or clipboard data.

slicer always uses the first namespace in the mapping file as the source namespace, and the second namespace as the target namespace (this is the "automatic" behavior, you can also choose the target), so make sure to order them correctly when loading.

## Additional considerations

- When loading a mapping, the entire workspace is re-analyzed with the new names, so it may take some time for the changes to apply.
- If you load multiple mappings:
  - the last loaded mapping will take precedence in case of conflicts, so be mindful of the loading order
  - the mappings will not be merged, so you can only have a single source and target namespace across all loaded mappings, which is usually not an issue since most mappings only have one of each, but it's something to keep in mind if you plan on loading multiple mappings with different namespaces.
