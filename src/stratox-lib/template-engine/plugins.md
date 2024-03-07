---
description: >-
  As I have already mentioned in the first chapters you can install some
  plugins/pre-made components (ingress, table, and modals), more will certainly
  come.
---

# Plugins

As I have already mention previous in the guide that you can install some components that I have already made. Currently, there are only three (ingress, table, and modals), but more will certainly come and I will list them here, at this section of the guide.&#x20;

### Install Startox views/components

```
npm i stratoxcomponents
```

_Every plugin component below you are able to build yourself, and I do recommend that if you're interested, at least take a look at how they are built by navigating to the "./node\_modules/stratoxcomponents/src/" directory and inspecting every component there._

### Ingress

Add a simple ingress component.

```javascript
import { StratoxIngress } from './node_modules/stratoxcomponents/src/StratoxIngress.js';

Stratox.setComponent("ingress", StratoxIngress);

const stratox = new Stratox("#app");

stratox.view("ingress", {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet",
});

stratox.execute();
```

#### Result:

{% embed url="https://codepen.io/wazabii8/pen/bGZgPNo" %}

### Table

Add a sortable table component.

```javascript
import { StratoxTable } from './node_modules/stratoxcomponents/src/StratoxTable.js';

Stratox.setComponent("table", StratoxTable);

const stratox = new Stratox("#app");

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

stratox.execute();
```

#### Result:

{% embed url="https://codepen.io/wazabii8/pen/Babpgzw" %}

### Modals

Bellow I will show 5 different modal components.

```javascript
import { StratoxModal } from './node_modules/stratoxcomponents/src/StratoxModal.js';
import { StratoxIngress } from './node_modules/stratoxcomponents/src/StratoxIngress.js';

Stratox.setComponent("modal", StratoxModal);
Stratox.setComponent("ingress", StratoxIngress);

// Show message modal on click
const myBtnMessage = document.getElementById("message-btn");
myBtnMessage.addEventListener("click", function (e) {
    e.preventDefault();

    Stratox.create("modal", {
        headline: "Lorem ipsum dolor",
        content: "Lorem ipsum dolor sit amet"
    });
});


// Show Confirm modal on click
const myBtnConfirm = document.getElementById("confirm-btn");
myBtnConfirm.addEventListener("click", function (e) {
    e.preventDefault();
  
    Stratox.create("modal", {
        type: "confirm",
        headline: "Lorem ipsum dolor",
        content: "Lorem ipsum dolor sit amet"
    }).container().set("confirm", function () {
        // Callback, modal has been confirmed
        alert("Confirmed..");
    });
});

// Show OK modal on click
const myBtnOk = document.getElementById("ok-btn");
myBtnOk.addEventListener("click", function (e) {
    e.preventDefault();

    Stratox.create("modal", {
        type: "ok",
        headline: "Lorem ipsum dolor",
        content: "Lorem ipsum dolor sit amet"
    }).container().set("confirm", function () {
        // Callback, modal has been confirmed
        alert("Confirmed..");
    });
});

// Show opener modal on click
const myBtnOpener = document.getElementById("opener-btn");
myBtnOpener.addEventListener("click", function (e) {
    e.preventDefault();

    Stratox.create("modal", {
        type: "opener",
        headline: "Lorem ipsum dolor",
        content: "Lorem ipsum dolor sit amet"
    });
});

// Show Custom modal on click.
// It is SUPER EASY to add custom content to Modal by adding
// your own compoents to the modal to customize.
const myBtnOpenerCustom = document.getElementById("custom-btn");
myBtnOpenerCustom.addEventListener("click", function (e) {
    e.preventDefault();

    let stratox = new Stratox();
    const modal = stratox.view("modal", {
        type: "opener",
    });

    const ingressA = stratox.view("ingress#ingressA", {
        headline: "Lorem ipsum dolor",
        content: "Lorem ipsum dolor sit amet",
    });

    const ingressB = stratox.view("ingress#ingressB", {
        headline: "Lorem ipsum dolor",
        content: "Lorem ipsum dolor sit amet",
    });

    stratox.execute();
});
```

#### Result:

{% embed url="https://codepen.io/wazabii8/pen/bGZgPwa" %}

More component will come in the future.
