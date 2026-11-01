---
title: Navigation
description: Overview of the navigation API.
tableOfContents: false
---

The navigation API allows you to register and change shortcuts for your script, as well as slicer itself.
The core of this API is the `NavigationContext`, which you can get from [the context](/slicer/script/#context).

```js wrap
context.navigation.shortcuts(); // gets all currently registered shortcuts
context.navigation.registerShortcut({
  id: "my-script/shortcut",
  label: "My amazing shortcut", // optional, defaults to the ID
  binding: {
    // the default binding for the shortcut, can be changed by the user; optional, defaults to no binding
    key: "a", // the key to bind to, e.g. "a", "Enter", "ArrowUp", etc.
    modifier: 1 | 2 | 4, // a bitmask of modifier keys; check the ShortcutBinding type for more info
  },
  run(event /*: KeyboardEvent */) {
    console.log("My amazing shortcut has been triggered!");
  },
});
context.navigation.unregisterShortcut("my-script/shortcut"); // unregisters a shortcut by its ID
```
