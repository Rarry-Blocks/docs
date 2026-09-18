---
sidebar_position: 7
---

# The Sandbox API (untrusted extensions)

Custom extensions you add through the **Custom Extensions** popup are _untrusted_ unless you tick "Run as trusted extension". Untrusted extensions do **not** run directly on the page.

## How untrusted extensions run

1. Your code is evaluated inside a **Web Worker**, so it has no access to the page, `window`, `document`, or the running project.
2. `Rarry` is still available inside the worker, so `Rarry.registerExtension({ ... })` works the same way.
3. Blocks are executed by the worker, and block `code` handlers receive only `(inputs)` (no `thread`).
4. Anything "external" (fetch, storage) must go through the `api` object provided to your code. Each call asks the user for permission with a `confirm()` prompt.

## The `api` object

| API                           | Description                                                                              |
| ----------------------------- | ---------------------------------------------------------------------------------------- |
| `api.fetch(url, opts)`        | Performs a `fetch` on the main thread and resolves with the **response text**.           |
| `api.storage.get(key)`        | Reads from `localStorage`. Values are namespaced per extension id, e.g. `"myExtId:key"`. |
| `api.storage.set(key, value)` | Writes to `localStorage` (namespaced per extension id). Resolves with `true`.            |
| `api.log(...args)`            | Logs to the page console with an `[extId]` prefix. Resolves with `true`.                 |

All of these return promises. If the user denies the permission prompt, the call rejects with `"Permission denied"`.

## Example

```js
Rarry.registerExtension({
  id: "myWeatherExt",

  category: {
    name: "Weather",
  },

  blocks: [
    {
      type: Rarry.BlockType.OUTPUT,
      id: "temperature",
      text: "temperature in [CITY]",
      promise: true,
      fields: {
        CITY: {
          kind: Rarry.InputType.VALUE,
          type: "String",
          default: "London",
        },
      },
    },
    {
      type: Rarry.BlockType.STATEMENT,
      id: "saveNote",
      text: "save note [NOTE]",
      promise: true,
      fields: {
        NOTE: { kind: Rarry.InputType.VALUE, type: "String", default: "" },
      },
    },
  ],

  code: {
    temperature: async (inputs) => {
      const res = await api.fetch(
        `https://wttr.in/${encodeURIComponent(inputs.CITY)}?format=j1`,
      );
      const json = JSON.parse(res);
      return json.current_condition[0].temp_C;
    },

    saveNote: async (inputs) => {
      await api.storage.set("lastNote", inputs.NOTE);
      await api.log("saved note:", inputs.NOTE);
    },
  },
});
```

Because these handlers are async, both blocks set `promise: true` so the VM waits for the returned promise to resolve.

## Limitations

- **No `thread`.** The worker has no access to the running VM. For per-thread variables use the trusted path instead (see [The Thread API](./thread.md)).
- **No statement inputs.** Statement fields can't run inside the worker, so their value is left out of `inputs`. Use value, menu and output blocks only.
- Untrusted blocks always go through the worker, so there's always at least one frame of round-trip latency.

:::warning
Even in the sandbox, an untrusted extension still gets to run arbitrary JavaScript and can request `fetch` or `storage` access. Only add extensions from people you trust.
:::
