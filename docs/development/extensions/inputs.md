---
sidebar_position: 3
---

# Dealing With Fields

In most of the cases, you will want blocks that can accept fields (example: the "say" block).

To implement a field in a block, you can pass a `fields` key:

```js
registerBlocks() {
  return [
    {
      type: "statement", // allows for connection on the top and bottom
      id: "sayHiWithvalue",
      fields: {
        abc: {
          kind: "value", // allows an output block to be connected
          type: "String",
          default: "default"
        },
      },
      text: "say 'hi' with [abc]",
    },
  ];
}
```

To get a field's value in the block's code, you can do this:

```js
registerCode() {
  return {
    sayHiWithvalue: (inputs) => {
      const value = inputs.abc; // here, we are getting the value of the input
      console.log("hi", value); // example functionality
    }
  };
}
```

## Example Result

![A block with text saying "say 'hi' with [abc]"](/img/extensions-blockinput.png)

When run, the console should show:

```
hi default
```

---

## Block Definition Reference

Here's a full list of available properties you can use when defining blocks inside `registerBlocks()`:

| Property                | Type                                   | Description                                                                                                  | Example                                      |
| ----------------------- | -------------------------------------- | ------------------------------------------------------------------------------------------------------------ | -------------------------------------------- |
| `id`                    | `string`                               | Unique identifier for the block (must be unique within the extension).                                       | `"sayHiWithvalue"`                           |
| `type`                  | `"statement"` \| `"cap"` \| `"output"` | Determines the block's connection type: statement (top/bottom), cap (top only), or output (returns a value). | `"statement"`                                |
| `text`                  | `string`                               | The visual label of the block. Use `[name]` to mark fields/inputs.                                           | `"say 'hi' with [abc]"`                      |
| `fields`                | `object`                               | Defines inputs, menus, or statements that appear in the block.                                               | `{ abc: { kind: "value", type: "String" } }` |
| `tooltip`               | `string`                               | Tooltip text shown when hovering over the block.                                                             | `"Makes the character say something."`       |
| `color`                 | `string`                               | Custom block color (defaults to category color).                                                             | `"#FFAA00"`                                  |
| `statementType`         | `string`                               | Optional custom connection check type for statements.                                                        | `"action"`                                   |
| `outputType`            | `string`                               | Output type for output blocks.                                                                               | `"Number"`                                   |
| `outputShape`           | `number`                               | Optional shape override for output blocks (see table below).                                                 | `1`                                          |
| `promise`               | `boolean`                              | If `true`, code generation will include `await` when calling the extension function.                         | `true`                                       |
| `fields.<name>.kind`    | `"value"` \| `"statement"` \| `"menu"` | Defines the type of field.                                                                                   | `"value"`                                    |
| `fields.<name>.type`    | `string` \| `string[]`                 | Type of input (for "value" fields).                                                                          | `"String"`                                   |
| `fields.<name>.default` | `string`                               | Default value shown in the block's shadow input.                                                             | `"default"`                                  |
| `fields.<name>.items`   | `Array`                                | Menu items for dropdown menus. Can be strings or `{text, value}` objects.                                    | `["left", "right"]`                          |
| `fields.<name>.accepts` | `string` \| `string[]`                 | (For statement fields) defines which statement types can connect.                                            | `"event"`                                    |

### Available Output Shapes

You can control how an output block looks using the `outputShape` property.  
These are the supported values:

| Value | Shape     | Description                                    |
| ----- | --------- | ---------------------------------------------- |
| `1`   | Hexagonal | Used for Booleans (true/false)                 |
| `2`   | Round     | Default shape, used for Numbers, Strings, etc. |
| `3`   | Square    | Used for custom data types                     |
| `4`   | Bowl      | Used for Arrays (lists)                        |
| `5`   | Pillow    | Used for Objects                               |

Each of these shapes is purely visual, they don't change how code generation works, but they help distinguish block types or categories visually.
