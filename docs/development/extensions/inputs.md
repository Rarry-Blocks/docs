---
sidebar_position: 3
---

# Dealing With Fields

In most of the cases, you will want blocks that can accept fields (example: the "say" block).

To implement a field in a block, you can pass a `fields` key:

```js
Rarry.registerExtension({
  id: "myInputExtension",

  category: {
    name: "My Input Extension",
  },

  blocks: [
    {
      type: Rarry.BlockType.STATEMENT, // allows for connection on the top and bottom
      id: "sayHiWithValue",
      fields: {
        abc: {
          kind: Rarry.InputType.VALUE, // allows an output block to be connected
          type: "String",
          default: "default",
        },
      },
      text: "say 'hi' with [abc]",
    },
  ],

  code: {
    sayHiWithValue: (inputs) => {
      const value = inputs.abc; // here, we are getting the value of the input
      console.log("hi", value); // example functionality
    },
  },
});
```

In the `text`, every `[name]` placeholder is replaced by the field with that name from `fields`. If a placeholder has no matching field, it is shown as literal text.

## Example Result

![A block with text saying "say 'hi' with [abc]"](/img/extensions-blockinput.png)

When run, the console should show:

```
hi default
```

---

## Block Definition Reference

Here's a full list of available properties you can use when defining a block inside the extension's `blocks` array:

| Property                | Type                                   | Description                                                                                                                                           | Example                                      |
| ----------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| `id`                    | `string`                               | Unique identifier for the block (must be unique within the extension).                                                                                | `"sayHiWithValue"`                           |
| `type`                  | `"statement"` \| `"cap"` \| `"output"` | Determines the block's connection type: statement (top/bottom), cap (top only), or output (returns a value).                                          | `"statement"`                                |
| `text`                  | `string`                               | The visual label of the block. Use `[name]` to mark fields/inputs.                                                                                    | `"say 'hi' with [abc]"`                      |
| `fields`                | `object`                               | Defines inputs, menus, or statements that appear in the block.                                                                                        | `{ abc: { kind: "value", type: "String" } }` |
| `tooltip`               | `string`                               | Tooltip text shown when hovering over the block.                                                                                                      | `"Makes the character say something."`       |
| `color`                 | `string`                               | Custom block color (defaults to the category color).                                                                                                  | `"#FFAA00"`                                  |
| `statementType`         | `string`                               | Optional custom connection check type for statements.                                                                                                 | `"action"`                                   |
| `outputType`            | `string`                               | Output connection check for output blocks. Also used to match a custom shape name.                                                                    | `"Number"`                                   |
| `outputShape`           | `number`                               | Optional built-in shape override for output blocks (see table below).                                                                                 | `1`                                          |
| `promise`               | `boolean`                              | If `true`, the block waits for the value returned by the extension function to resolve.                                                               | `true`                                       |
| `duplicateOnDrag`       | `boolean`                              | If `true`, dragging the block out of a stack clones it instead of moving it.                                                                          | `true`                                       |
| `inlineInputs`          | `boolean`                              | Whether inputs are laid out inline. Defaults to `true`.                                                                                               | `false`                                      |
| `fields.<name>.kind`    | `"value"` \| `"statement"` \| `"menu"` | Defines the type of field.                                                                                                                            | `"value"`                                    |
| `fields.<name>.type`    | `string` \| `string[]`                 | Connection check for "value" fields (e.g. `"String"`, `"Number"`, `"Boolean"`, or a custom shape name).                                               | `"String"`                                   |
| `fields.<name>.default` | `number` \| `string` \| `boolean`      | Default value shown in the block's shadow input, or the initially selected menu item. Shadow defaults only work for `Number`, `String` and `Boolean`. | `"default"`                                  |
| `fields.<name>.items`   | `Array`                                | Menu items for dropdown menus. Can be strings or `{text, value}` objects.                                                                             | `["left", "right"]`                          |
| `fields.<name>.accepts` | `string` \| `string[]`                 | (For statement fields) defines which statement types can connect.                                                                                     | `"event"`                                    |

:::note
`statementType` and `accepts` are used exactly as written (they are **not** automatically namespaced). To avoid clashes with other extensions, prefix custom types with your extension id, e.g. `"myInputExtension_action"`.
:::

### Available Output Shapes

You can control how an output block looks using the `outputShape` property.  
These are the supported values:

| Value | Shape     | Description                                    |
| ----- | --------- | ---------------------------------------------- |
| `1`   | Hexagonal | Used for Booleans (true/false)                 |
| `2`   | Round     | Default shape, used for Numbers, Strings, etc. |
| `3`   | Square    | Used for custom data types                     |
| `4`   | Pillow    | Used for Objects                               |
| `5`   | Bowl      | Used for Arrays (lists)                        |
| `6`   | Spikey    | Used for Sets                                  |

Each of these shapes is purely visual, they don't change how code generation works, but they help distinguish block types or categories visually.

The `Rarry.BlockShape` constants map to these values: `NUMBER` and `STRING` are `2` (round), `ARGUMENT` is `3` (square), `OBJECT` is `4` (pillow), `ARRAY` is `5` (bowl) and `SET` is `6` (spikey).

:::tip
To render a **custom** shape instead of a built-in one, set `outputType` (or the field's `type`) to the shape name. See [Custom Shapes](./custom_shapes.md).
:::
