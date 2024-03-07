---
description: >-
  Stratox also comes with a specialized JavaScript container library designed
  for seamless communication between template views and the application. It
  allows for efficient data exchange.
---

# Container

### Access container

A new container instance will be binded to each Stratox class instance. You can then access the container instance both inside a component and outside of it.

#### 1. Inside component

```javascript
export function custom(data, container, helper, builder)
{
    if (container.has("fallback")) {
        container.get("fallback");
    }
    ...
```

#### 2. Outside of component

```javascript
const stratox = new Stratox("#ingress");
const container = stratox.container();
```

### Container usage

Bellow is some quick example on different ways you can use the container.

```javascript
// Example 1
container.set("someObject", { test: "Container 1" });

console.log(container.get("someObject").test);
// Log response: Container 1

// Example 2
container.set("passingAFunction", function(arg1, arg2) {
	alert(arg1+" "+arg2);
});

container.get("passingAFunction", "Hello", "world!");
// Alert response: Hello world!
```

### Working example

Let's make a real working example to show to use `container` and why they will make your life way easier.

#### 1. Create template

```javascript
export function custom(data, container, helper, builder)
{
    const inst = this;

    let out = `
    <article>
        <section class="mb-30">
            <h2 class="title">${data.headline}</h2>
            <p>${data.content}</p>
        </section>
        <a id="my-view-btn" class="button" href="#">Update headline</a>
    <article>
    `;

    this.eventOnload(() => {
        // Add Click event
        const btn = document.getElementById("my-view-btn");
        btn.addEventListener("click", function(e) {
            e.preventDefault();
            
            data.headline = "Headline updated";
            inst.update();

            // Let's use the container to create a fallback
            if (container.has("fallback")) {
                container.get("fallback");
            }
        });
    });

    return out;
}
```

#### 2. Show the template

Let's show the template and set the fallback container .

```javascript
let stratox = new Stratox("#app");

stratox.view("custom", {
    headline: "Lorem ipsum dolor",
    content: "Lorem ipsum dolor sit amet",
});

stratox.execute();

stratox.container().set("fallback", function () {
    // Callback, modal has been confirmed
    alert("The headline has been updated!");
});

```

#### Result:

{% embed url="https://codepen.io/wazabii8/pen/OJqgLWY" %}

### Stand alone

You can also use the container in other projects.

```javascript
import { StratoxContainer } from './node_modules/stratox/src/StratoxContainer.js';
const container = new StratoxContainer();
```

### Method list

**Set a container or factory**

```
set(key, value, overwrite);
```

* **key:** Unique container key/string identifier
* **value:** Mixed value of whatever you want to share, e.g. String, number, object, function.&#x20;
* **overwrite:** Attempting to set a container multiple times will trigger a warning unless manual consent is provided by setting "overwrite" to true.

**Set factory only**

```
setFactory(key, value, overwrite);
```

* **key:** Unique factory key/string identifier&#x20;
* **value:** callable
* **overwrite:** Attempting to set a container multiple times will trigger a warning unless manual consent is provided by setting "overwrite" to true.

**Get a container or factory**

```
get(key, ...args);
```

* **key:** Unique container key/string identifier
* **args:** pass arguments to possible factory/function

**Check if container/factory exists**

```
has(key);
```

* **key:** Unique container key/string identifier

**Check if is strict a "container".**

```
isContainer(key);
```

* **key:** Unique container key/string identifier

**Check if is strict a "factory/function".**

```
isFactory(key);
```

* **key:** Unique factory key/string identifier
