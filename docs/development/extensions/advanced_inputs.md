---
sidebar_position: 4
---

# Advanced Fields

Now that you know how to implement basic fields, let's go a step further!

All of the snippets below are pieces that belong inside the `blocks` array of a descriptor, with a matching entry in the `code` object.

## Menus

In one of your blocks, you might want to allow the user to only select specific options (example: the wait block). You can do this using a **menu field**.

```js
blocks: [
  {
    type: Rarry.BlockType.STATEMENT,
    id: "menu",
    text: "menu [hi]",
    fields: {
      hi: {
        kind: Rarry.InputType.MENU,
        items: [
          "normal",
          // shows up as "ABC display" for the user, but in the code it will be "abc"
          { text: "ABC display", value: "abc" },
        ],
        default: "abc",
      },
    },
  },
],
```

### Getting the value

```js
code: {
  menu: inputs => {
    window.alert(inputs.hi);
  },
},
```

The dropdown starts on the first item unless a `default` value is provided.

## Statement Fields

Statement fields allow you to **attach blocks inside other blocks** (like loops, if statements, etc).

```js
blocks: [
  {
    type: Rarry.BlockType.CAP,
    id: "statement",
    fields: {
      code: { kind: Rarry.InputType.STATEMENT },
    },
    text: "i want statement [code]",
  },
],
```

### Running the attached code

Statement inputs rely on generator functions (their code can `yield`, e.g. `wait`, `say`, motion). They are passed to your `code` function as **generator functions**, so you must run them with `yield*`, and the handler must be a generator function:

```js
code: {
  statement: function* (inputs) {
    if (inputs.code) yield* inputs.code(); // executes attached blocks
  },
},
```

:::tip
Only handlers that actually run attached statements need to be generators. Handlers that don't can stay plain functions.
:::

## Restricting Statement Connections

You can restrict what blocks can be placed inside a statement input.

### Define a custom statement type

```js
blocks: [
  {
    type: Rarry.BlockType.STATEMENT,
    id: "statementA",
    text: "type statement A",
    statementType: "myExtension_statementA",
  },
],
```

### Restrict input to that type

```js
blocks: [
  {
    type: Rarry.BlockType.STATEMENT,
    id: "onlyStatementA",
    fields: {
      code: {
        kind: Rarry.InputType.STATEMENT,
        accepts: "myExtension_statementA",
      },
    },
    text: "only statement A [code]",
  },
],
```

Now only blocks with `statementType: "myExtension_statementA"` can be placed inside.

:::tip
`statementType` and `accepts` are matched exactly as written, so prefix them with your extension id to avoid clashing with other extensions.
:::

## Conditional Blocks

You can combine **value inputs** and **statement inputs** to create logic blocks.

### If block

```js
blocks: [
  {
    type: Rarry.BlockType.STATEMENT,
    id: "if",
    fields: {
      bool: { kind: Rarry.InputType.VALUE, type: "Boolean", default: true },
      code: { kind: Rarry.InputType.STATEMENT },
    },
    text: "if [bool] then [code]",
  },
],
```

```js
code: {
  if: function* (inputs) {
    if (inputs.bool) yield* inputs.code();
  },
},
```
