---
sidebar_position: 8
---

# Thread API

When an extension runs as **trusted** (the "Run as trusted extension" checkbox, or a built-in extension), its `code` handlers run directly on the page and are called as `handler(inputs, thread)`. The second argument is the current VM **Thread** that is executing the script.

Untrusted extensions (see [The Sandbox API](./sandbox_api.md)) don't get a `thread` because they run in a separate worker.

## What is a Thread?

Rarry runs each script (e.g. one `when flag clicked` stack, a key event, a clone) as its own `Thread`. The `thread` gives you access to **temporary variables** that live for the duration of that thread and are isolated from everything else.

| Method                     | Description                                              |
| -------------------------- | -------------------------------------------------------- |
| `thread.getVar(name)`      | Read a temporary variable. Returns `undefined` if unset. |
| `thread.setVar(name, val)` | Set a temporary variable.                                |
| `thread.hasVar(name)`      | Check whether a temporary variable is set.               |
| `thread.deleteVar(name)`   | Delete a temporary variable.                             |
| `thread.clearVars()`       | Delete every temporary variable on the thread.           |

Variables are lost when the thread stops, and each thread starts empty, so they never leak between scripts.

## Example

```js
Rarry.registerExtension({
  id: "tempCounter",

  category: {
    name: "Temp Counter",
  },

  blocks: [
    {
      type: Rarry.BlockType.STATEMENT,
      id: "increment",
      text: "increment counter",
    },
    {
      type: Rarry.BlockType.OUTPUT,
      id: "counterValue",
      text: "counter value",
    },
  ],

  code: {
    increment: (inputs, currentThread) => {
      const current = Number(currentThread?.getVar("count")) || 0;
      currentThread?.setVar("count", current + 1);
    },
    counterValue: (inputs, currentThread) => {
      return currentThread?.getVar("count") ?? 0;
    },
  },
});
```
