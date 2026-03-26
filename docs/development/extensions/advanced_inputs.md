---
sidebar_position: 4
---

# Advanced Fields

Now that you know how to implement basic fields, let's go a step further!

## Menus

In one of your blocks, you might want to allow the user to only select specific options (example: the wait block). You can do this using a **menu field**.

```js
{
  type: "statement",
  id: "menu",
  text: "menu [hi]"
  fields: {
    hi: {
      kind: "menu",
      items: [
        "normal",
        // shows up as ABC display for the user, but in the code it will be abc
        { text: "ABC display", value: "abc" }
      ],
      default: "abc"
    }
  },
}
```

### Getting the value

```js
menu: inputs => {
  window.alert(inputs.hi);
};
```

## Statement Fields

Statement fields allow you to **attach blocks inside other blocks** (like loops, if statements, etc).

```js
{
  type: "cap",
  id: "statement",
  fields: {
    code: { kind: "statement" }
  },
  text: "i want statement [code]"
}
```

### Running the attached code

Statement inputs are functions, so you call them:

```js
statement: inputs => {
  inputs.code?.(); // executes attached blocks
};
```

## Restricting Statement Connections

You can restrict what blocks can be placed inside a statement input.

### Define a custom statement type

```js
{
  type: "statement",
  id: "statementA",
  text: "type statement A",
  statementType: "statementA"
}
```

### Restrict input to that type

```js
{
  type: "statement",
  id: "onlyStatementA",
  fields: {
    code: {
      kind: "statement",
      accepts: "statementA"
    }
  },
  text: "only statement A [code]"
}
```

Now only blocks with `statementType: "statementA"` can be placed inside.

## Conditional Blocks

You can combine **value inputs** and **statement inputs** to create logic blocks.

### If block

```js
{
  type: "statement",
  id: "if",
  fields: {
    bool: { kind: "value", type: "Boolean", default: true },
    code: { kind: "statement" }
  },
  text: "if [bool] then [code]"
}
```

```js
if: (inputs) => {
  if (inputs.bool) inputs.code?.();
}
```
