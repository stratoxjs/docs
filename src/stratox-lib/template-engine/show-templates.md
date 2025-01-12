---
description: >-
  Stratox.js offers remarkable flexibility. You can use the same component view
  multiple times in various locations and with varying content.
---

# Show views

### How it works

#### 1. Import

Let's start with importing a template view/component. I will begin with the easiest one: the ingress component.

```javascript
import StratoxIngress from './path/to/view/StratoxIngress';
```

#### 2. Initialize instance

Create a class instance and pass a DOM element where you wish to show your template.

```javascript
const stratox = new Stratox("#app");
```

#### 3. Add view

Now, add the view to the class instance and pass in the expected content object data to the view.

```javascript
stratox.view(StratoxIngress, {
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
import StratoxIngress from './path/to/view/StratoxIngress';
import StratoxTable from './path/to/view/StratoxTable';

let stratox = new Stratox("#app");

// Add Ingress view: Welcome message
stratox.view(StratoxIngress, {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

// Add table view A
stratox.view({ tbViewA: StratoxTable }, {
  feed: [
    {
      firstname: "John",
      lastname: "Doe",
      email: "john@gmail.com",
      status: "1",
    },
    {
      firstname: "Fredrik",
      lastname: "Doe",
      email: "fredrik@gmail.com",
      status: "0",
    },
    {
      firstname: "Dave",
      lastname: "Doe",
      email: "dave@gmail.com",
      status: "1",
    },
  ]
});

// Add table view B
stratox.view({ tbViewB: StratoxTable }, {
  feed: [
    {
      firstname: "Jane",
      lastname: "Doe",
      email: "jane@gmail.com",
      status: "1",
    },
    {
      firstname: "Sophia",
      lastname: "Doe",
      email: "sophia@gmail.com",
      status: "0",
    },
    {
      firstname: "Ava",
      lastname: "Doe",
      email: "ava@gmail.com",
      status: "1",
    },
  ]
});

stratox.execute();
```

### Quick create and set component

Can also quick create and set component in one. The setComponent, argument 2 will also take a anonymous function like demonstrated below.

```javascript

const stratox = new Stratox("#app");

stratox.view(({ props }) => `
    <header class="mb-50 align-center">
        <h1>${props.headline}</h1>
        <p>${props.content}</p>
    </header>
`, {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

stratox.execute();
```

Now let's move on and take a look on how you can build custom templates.
