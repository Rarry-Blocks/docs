---
sidebar_position: 2
---

# Your First Extension

Let's start by creating a simple extension to learn the syntax and how to add stuff:

```js
/* the main structure of your extension */

class Extension {
  id = "myFirstExtension"; // unique id for your extension

  registerCategory() {
    return {
      name: "My Extension", // the name of your extension
    };
  }

  registerBlocks() {
    return [
      {
        /* create an output block */
        type: "output",
        id: "helloBlock",
        text: "hello",
      },
    ];
  }

  registerCode() {
    return {
      /* the code executed by your block */
      helloBlock: () => {
        return "block"; // since it's an output, it needs to return something
      },
    };
  }
}

registerExtension(Extension); // add your extension onto the editor
```

After importing this extension, you will see a new category called "My Extension" appear. That's your extension.

![Image showing a category called "My Extension"](/img/extensions-myextension.png)

Inside, you will find a "hello" block which returns "block" as specified in the extension's code.

![Image showing a block with text saying "hello"](/img/extensions-hello.png)