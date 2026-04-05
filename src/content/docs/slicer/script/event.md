---
title: Event bus
description: Overview of the event bus API.
---

A crucial piece of the scripting API is the event bus, which allows you to receive user-triggered events, e.g. a file being opened, and respond to them.

If you've ever written JavaScript for the web, you'll likely find it similar to the DOM event bus.

## Listening

You can listen to an event using the `addEventListener` function on a context, like so:

```js
// omitting the script body, this can be done anywhere with a context
context.addEventListener("event_type", (e /*: any */, context /*: ScriptContext */) => {
  // this is the event handler
});
```

Now, `event_type` is obviously not a real event identifier, so let's try it out with one.

```js
context.addEventListener("preload", (e /*: PreloadEvent */) => {
  console.log("We're loading a class file: ", e.name);
});
```

This example listens to the `preload` event, which is triggered every time slicer tries to read a file as a class file (`user requested file load` -> `0xcafebabe header found?` -> `trigger preload event`).

The event is mutable, so you can mutate its properties to change what gets to slicer in the end.

```js
context.addEventListener("preload", (e /*: PreloadEvent */) => {
  e.data = e.data.subarray(4); // chop off first 4 bytes
});
```

Chopping off 4 bytes (the magic header) is not exactly something you'd want to do, since it'd either make slicer not read the file as a class file at all or break the disassembler down the line, but you can do it!

## Un-listening

If you wanted to stop listening for an event, you can use the `removeEventListener` function on a context:

```js
const handler = (e /*: any */, context /*: ScriptContext */) => {
  // this is the event handler
};

context.addEventListener("event_type", handler); // listen to the event
context.removeEventListener("event_type", handler); // stop listening to the event
```

## Dispatching

You can also dispatch your own events onto the bus using the `dispatchEvent` function:

```js
context.dispatchEvent({ type: "my_custom_event" /*, myData: ... */ });
```

:::caution

If you dispatch onto the context of your own script, event handlers in other scripts will not be called.

If you wish for all scripts to handle your event, dispatch onto the root context.

:::

## Available events

:::tip

Check the [TypeScript type definitions](https://github.com/katana-project/script/blob/main/index.d.ts) for more details about the events and their properties.

:::

| Event type      | Event object type   | Description                                                                          |
| --------------- | ------------------- | ------------------------------------------------------------------------------------ |
| `preload`       | `PreloadEvent`      | Triggered when a class file is about to be loaded. You can modify the raw data here. |
| `option_change` | `OptionChangeEvent` | Triggered when [an option](/slicer/script/options) is changed.                       |
| `locale_change` | `LocaleChangeEvent` | Triggered when the UI locale changes.                                                |
