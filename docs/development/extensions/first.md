---
sidebar_position: 2
---

# Your First Extension

Let's start by creating a simple extension to learn the syntax and how to add stuff:

```js
/* the main structure of your extension */

Rarry.registerExtension({
  id: "myFirstExtension", // unique id for your extension

  category: {
    name: "My Extension", // the name shown in the toolbox
  },

  blocks: [
    {
      /* create an output block */
      type: Rarry.BlockType.OUTPUT,
      id: "helloBlock",
      text: "hello",
    },
  ],

  code: {
    /* the code executed by your block */
    helloBlock: () => {
      return "block"; // since it's an output, it needs to return something
    },
  },
});
```

`Rarry.registerExtension()` is available globally, so you don't need to import anything. Paste the code into the editor's **Custom Extensions** popup (or ship it as a built-in extension file) and it will register immediately.

After importing this extension, you will see a new category called "My Extension" appear. That's your extension.

![Image showing a category called "My Extension"](/img/extensions-myextension.png)

Inside, you will find a "hello" block which returns "block" as specified in the extension's code.

![Image showing a block with text saying "hello"](/img/extensions-hello.png)

## The Extension Descriptor

`Rarry.registerExtension()` takes a single **extension descriptor object**. These are the top-level keys:

| Key        | Type     | Description                                                                             |
| ---------- | -------- | --------------------------------------------------------------------------------------- |
| `id`       | `string` | Unique identifier for the extension. Required.                                          |
| `category` | `object` | The toolbox category: `{ name, color, iconURI }`.                                       |
| `blocks`   | `array`  | The block definitions added by this extension.                                          |
| `code`     | `object` | Maps each block's `id` to the function that runs it.                                    |
| `shapes`   | `object` | Custom connection shapes, keyed by shape name. See [Custom Shapes](./custom_shapes.md). |
| `notches`  | `object` | Custom statement notch shapes, keyed by connection check type.                          |

You can use plain string values (`"output"`, `"value"`, `"menu"`, ...) or the constants exposed on the global `Rarry` object (recommended):

- `Rarry.BlockType`: `STATEMENT`, `CAP`, `OUTPUT`
- `Rarry.InputType`: `VALUE`, `STATEMENT`, `MENU`
- `Rarry.BlockShape`: `NUMBER`, `STRING`, `ARGUMENT`, `ARRAY`, `OBJECT`, `SET`
