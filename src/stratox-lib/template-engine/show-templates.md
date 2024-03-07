---
description: >-
  Stratox.js offers remarkable flexibility. You can use the same component view
  multiple times in various locations and with varying content.
---

# Show views

I will install some **pre-made views/components** that you are also free to use. Currently, there are only three (ingress, table, and modals), but more will certainly come (read more under Plugins). In the next chapter, I will show you how to build a template view. However, in this chapter, I will focus on how to display views.

### Install Startox views/components

```
npm i stratoxcomponents
```

### How it works

**I assume that you have read the installation section and that you have initialized and configured Startox.**

#### 1. Import

Let's start with importing a template view/component. I will begin with the easiest one: the ingress component.

```javascript
import { StratoxIngress } from './node_modules/stratoxcomponents/src/StratoxIngress.js';
```

#### 2. Set component

Pass the component/view to Stratox; this should be done before the Stratox classes are initialized. Once the component is set, every new Stratox instance will have access to the component.

<pre class="language-javascript"><code class="lang-javascript"><strong>Stratox.setComponent("ingress", StratoxIngress);
</strong></code></pre>

_The setComponent, argument 2 will also take a anonymous function._

#### 3. Initialize instance

Create a class instance and pass a DOM element where you wish to show your template.

```javascript
const stratox = new Stratox("#app");
```

#### 4. Add view

Now, add the view to the class instance and pass in the expected content object data to the view.

```javascript
stratox.view("ingress", {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet",
});
```

#### 5. Execute

Execute and display the views/components in the expected DOM element.

```javascript
stratox.execute();
```

#### 5. The HTML

The HTML really just need a starting point but that depends completely on your need, building a app or enhancing a regular page.

```html
<html>
  <head>
    <title>Example</title>
    <meta charset="UTF-8" />
  </head>
  <body>
      <div id="app"></div>
  </body>
</html>
```

#### 6. The result

{% embed url="https://codepen.io/wazabii8/pen/bGZgPNo" %}

### Combining multiple views

You can combine multiple views, both the same and different, in the same instance. Let's build upon the above example and add the ingress twice, combining them with a new table view:

```javascript
Stratox.setComponent("ingress", StratoxIngress);
Stratox.setComponent("table", StratoxTable);

let stratox = new Stratox("#app");

// Add Ingress 1 view: Welcome message
stratox.view("ingress#welcome", {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

// Add table view
stratox.view("table", {
  feed: [
    {
      firstname: "John",
      lastname: "Doe",
      email: "john@gmail.com",
      status: "1",
    },
    {
      firstname: "Jane",
      lastname: "Doe",
      email: "jane@gmail.com",
      status: "0",
    },
    {
      firstname: "Dave",
      lastname: "Doe",
      email: "dave@gmail.com",
      status: "1",
    },
  ],
  thead: [{ firstname: "Name" }, { email: "Email" }, { status: "Status" }],
  tbody: [
    "{{firstname}} {{lastname}}",
    '<a href="mailto:{{email}}">{{email}}</a>',
    "{{status}}",
  ],
});

// Add Ingress 2 view: Footer message
stratox.view("ingress#footer", {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

stratox.execute();
```

#### The result:

{% embed url="https://codepen.io/wazabii8/pen/oNVZEgM" %}

### Quick create and set component

Can also quick create and set component in one. The setComponent, argument 2 will also take a anonymous function like demonstrated below.

```javascript
Stratox.setComponent("ingress", function(data, container, helper, builder) {
    let out = `
    <header class="mb-50 align-center">
        <h1>${data.headline}</h1>
        <p>${data.content}</p>
    </header>
    `;
    return out;
});

const stratox = new Stratox("#app");

stratox.view("ingress", {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

stratox.execute();
```

Now let's move on and take a look on how you can build custom templates.
