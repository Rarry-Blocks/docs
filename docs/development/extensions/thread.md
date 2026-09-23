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

## Generator and async handlers

Trusted handlers can be written three ways:

| Handler                 | How the VM runs it                                                                  |
| ----------------------- | ----------------------------------------------------------------------------------- |
| Plain function          | Called and its return value is used immediately.                                    |
| `async` function        | Set `promise: true` on the block and the VM waits for the returned promise.         |
| Generator (`function*`) | Run with `yield*`, so it can `yield` to pause for a frame and run statement inputs. |

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
