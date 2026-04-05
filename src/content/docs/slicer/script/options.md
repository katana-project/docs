---
title: Options
description: Overview of the options API.
---

Scripts are supposed to be pretty simple, but sometimes you want the user to configure them, for example to turn on/off specific abilities of the script instead of just enabling/disabling it entirely.

You can do this using the options API, which allows you to add arbitrary entries into the script menu.

:::note

The options API goes hand in hand with [the event bus API](/slicer/script/event), so make sure to give that a read before attempting options.

:::

Each option has common properties, like a `type`, `id`, `position`, an `icon` and an optional `label`, which defaults to the ID.

```js
const option /*: Option */ = {
  type: "...", // one of "button", "checkbox", "radio", "group" or "separator"; check the OptionType type
  id: "my-script-option",
  label: "My amazing option", // optional
  position: "menu.file", // optional; e.g. "menu.root", "menu.file", etc.
  icon: { type: "html", value: "<svg>...</svg>" }, // optional
  // type-specific properties, ...
};

export default {
  // ...
  options: [option],
  load(context /*: ScriptContext */) {
    // this is where the event bus comes in
    context.addEventListener("option_change", (e /*: OptionChangeEvent */) => {
      console.log("An option has been changed: ", e.option.id);
      console.log("Is it our option?", e.option === option);
    });
  },
  unload() {},
};
```

## Buttons

The most ubiquitous of them all, the button. It does exactly what one would expect it to do - an action is triggered when you press it.

```js
const button /*: ButtonOption */ = {
  type: "button",
  id: "my-script-button",
  label: "My amazing button",
};

// ...
```

## Checkboxes

An obvious solution for boolean configuration options, a checkbox!

_Checkbox checked state is not persisted by slicer and **will be RESET** on refresh._

```js
const checkbox /*: CheckboxOption */ = {
  type: "checkbox",
  id: "my-script-checkbox",
  label: "My amazing checkbox",
  checked: false, // not checked by default
};

// ...
```

## Radio buttons

For whenever you need to represent an enum-like value, there's always a radio button.

_Radio button selection is not persisted by slicer and **will be RESET** on refresh._

```js
const radio /*: RadioOption */ = {
  type: "radio",
  id: "my-script-radio",
  label: "My amazing radio button",
  items: [
    {
      id: "apple",
      label: "Apples", // optional, defaults to the ID
    },
    {
      id: "orange",
      label: "Oranges",
    },
  ],
  selected: "apple", // a selected item ID, apples are default!!
};

// ...
```

## Groups

Last but not least, there's the group option for cases where you need to group related options together into one slick menu.

```js
const group /*: GroupOption */ = {
  type: "group",
  id: "my-script-group",
  label: "My amazing group",
  options: [
    // options declared like normal
    {
      type: "button",
      id: "my-script-button",
      label: "My amazing button",
    },
    {
      type: "checkbox",
      id: "my-script-checkbox",
      label: "My amazing checkbox",
      checked: false,
    },
  ],
};

// ...
```

## Separators

If you want to logically separate some options from others, you can use a separator.

```js
const separator /*: Option */ = {
  type: "separator",
  id: "my-script-separator",
};

// ...
```
