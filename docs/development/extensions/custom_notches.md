---
sidebar_position: 6
---

# Custom Notches

Notches are the connectors on the top and bottom of statement blocks. Just like [Custom Shapes](./custom_shapes.md), you can build your own so that statement stacks with a custom `statementType` look distinct.

If you haven't already, read [Custom Shapes](./custom_shapes.md) first. Notches use the same SVG path helpers, so most of what you learned there applies here too.

:::note
Like custom shapes, custom notches are only registered for **trusted** extensions. Untrusted extensions' `notches` are ignored.
:::

## The `notches` Key

Add a `notches` key to your descriptor. Each entry is **keyed by the statement check type** you use in `statementType` / `accepts`, and provides a shape for both sides of the connection:

```js
Rarry.registerExtension({
  id: "myNotchExtension",

  notches: {
    myNotchExtension_statementA: {
      pathLeft: (width, height, svgPaths) => {
        return svgPaths.line([
          svgPaths.point(width / 2, height),
          svgPaths.point(width / 2, -height),
        ]);
      },
      pathRight: (width, height, svgPaths) => {
        return svgPaths.line([
          svgPaths.point(-width / 2, height),
          svgPaths.point(-width / 2, -height),
        ]);
      },
    },
  },
});
```

### Parameters

| Parameter  | Type     | Description                                                      |
| ---------- | -------- | ---------------------------------------------------------------- |
| `width`    | `number` | The nominal width of the notch, provided by the renderer.        |
| `height`   | `number` | The nominal height of the notch, provided by the renderer.       |
| `svgPaths` | `object` | Utility object containing SVG path helpers used by the renderer. |

Unlike shape functions, notch functions return a **path object** (e.g. the result of `svgPaths.line([...])`), not a plain string. Use `svgPaths.point(x, y)` to define waypoints relative to the connection and `svgPaths.line()` to join them.

- `pathLeft` draws the side that connects into the previous block.
- `pathRight` draws the side that connects into the next block.
- Both are called with the same `width`, `height` and `svgPaths`.

## Using the Notch

A notch only shows up on blocks whose statement check matches the key. Create a statement type and a block that accepts it:

```js
Rarry.registerExtension({
  id: "myNotchExtension",

  category: {
    name: "My Notch Extension",
  },

  notches: {
    myNotchExtension_statementA: {
      pathLeft: (width, height, svgPaths) => {
        return svgPaths.line([
          svgPaths.point(width / 2, height),
          svgPaths.point(width / 2, -height),
        ]);
      },
      pathRight: (width, height, svgPaths) => {
        return svgPaths.line([
          svgPaths.point(-width / 2, height),
          svgPaths.point(-width / 2, -height),
        ]);
      },
    },
  },

  blocks: [
    {
      type: Rarry.BlockType.STATEMENT,
      id: "myAction",
      text: "do my action",
      statementType: "myNotchExtension_statementA",
    },
    {
      type: Rarry.BlockType.STATEMENT,
      id: "repeatMyAction",
      fields: {
        code: {
          kind: Rarry.InputType.STATEMENT,
          accepts: "myNotchExtension_statementA",
        },
      },
      text: "repeat my action [code]",
    },
  ],

  code: {
    myAction: () => {
      console.log("action!");
    },
    repeatMyAction: function* (inputs) {
      if (inputs.code) yield* inputs.code();
    },
  },
});
```

Now the stack of `myAction` blocks, and the statement slot on `repeatMyAction`, render with your custom notch.

:::note
The notch id (`myNotchExtension_statementA` above) is matched exactly against `statementType` and `accepts`, the same way custom shapes are matched against output types. Prefix it with your extension id so it doesn't clash with another extension.
:::
