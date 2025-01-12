---
description: >-
  This is only a quick and basic example to showcase the Stratox template
  engine. In the next section, I will go in-depth on how it really works.
---

# Basic example

**I assume that you have read the installation section and that you have initialized and configured Startox.**

### Create component view

Create a template file named "ingress.js" and add the code below.

```javascript
export default function ingressComponent({ props })
{
    return `
    <header class="mb-50 align-center">
        <h1>${props.headline}</h1>
        <p>${props.content}</p>
    </header>
    `;
}
```

### Show component view

I will break down and explain the example in the next page.

```javascript
import ingressComponent from './path/to/ingress.js';

const stratox = new Stratox("#app");

stratox.view(ingressComponent, {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

stratox.execute();
```

### The result

{% embed url="https://codepen.io/wazabii8/pen/bGZgPNo" %}

Let's move on to the next section, where I will go in-depth on how it really works.
