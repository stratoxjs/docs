---
description: >-
  It´s super easy to create template views and components and further more add
  your own custom functionality to them.
---

# Create views

**I don't skip sections of the guide. Please keep in mind that the guide is designed to be read linearly, so try to avoid jumping through it on the first read through.**

### Create template

You can easily create your own templates if you know some HTML and JavaScript. I will show you a couple of templates below to get you started.

Create a template file named "ingress.js" and add the boilerplate code below to it.

```javascript
export function ingressComponent(data, container, helper, builder)
{
    let out = `
    <header class="mb-50 align-center">
        <h1>${data.headline}</h1>
        <p>${data.content}</p>
    </header>
    `;
    return out;
}
```

Let's start breaking down the example above. First of all, you will need to create a function that is exported. You can name it whatever you want. The function can utilize four arguments, which I have named: **data, container, helper** and **builder**.

#### The function arguments

* `data`**:** This is the object data passed to your template file.
* `container`**:** The container can be used to communicate with your template and the outside.
* `helper`**:** Your own possible helper libraries, objects, and functions you passed in the configuration.
* `builder`**:** Access the Stratox builder library (you can manage without, only for advanced users; more on this later on).

#### The content

What you place inside the function (component) doesn't actually matter much. Most of the time, you would probably want to create some kind of view, as I have done above. However, you can place function withing the function to which is a recommended way to organize a more advanced component view. You could also for example use it to create a component that will trigger some kind of function. The only thing that sets the boundaries is your creativity. I will show you a little more advance template bellow.

#### The Return

It is not required to return an output, but if you do, it is expected to return a string value that will automatically be appended to a DOM element if you have specified an element in the Stratox class initialization. If you do not return a value, then your component could handle the appending to DOM part, for example, dynamically append Modals and so on.

### Show the template

Now we only need to use the template, as we already discussed on the previous page. It is not harder than this:

```javascript
import { ingressComponent } from './path/to/ingress.js';

Stratox.setComponent("ingress", ingressComponent);

let stratox = new Stratox("#app");

stratox.view("ingress", {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

stratox.execute();
```

#### The result:

{% embed url="https://codepen.io/wazabii8/pen/bGZgPNo" %}

### Template table example

Let's create a sortable table template view component.

```javascript
export function tableComponent(data, container, helper, builder) {

    let inst = this;
    if(!data.sort) data.sort = {};

    let out = `
    <table cellpadding="0" cellpadding="0">
        <thead>${thead()}</thead>
        <tbody>${tbody()}</tbody>
    </table>
    `;
    
    // Execute after the component has been executed
    this.eventOnload(() => {
        // Click event (bindEvent is Startox eventlistener)
        inst.bindEvent(inst.setSelector(".sort"), "click", (e, target) => {
            e.preventDefault();
            let name = target.dataset['name'];
            if(typeof name === "string") sort(name);
        });
    });

    /**
     * Render content for the thead
     * @return {string}
     */
    function thead() {
        let out = "";
        if(data?.thead?.length > 0) data.thead.forEach(function(v, k) {
            let key, val;
            if(typeof v === "object") {
                let keys = Object.keys(v);
                if(keys.length > 0) {
                    key = keys[0];
                    val = v[key];
                }
            } else {
                val = v;
            }
            out += `<th${key ? ' class="sort" data-name="'+key+'"' : ''}>${val}</th>`;
        });
        return out;
    }

    /**
     * Render content for the tbody
     * @return {string}
     */
    function tbody() {
        let out = "";
        data.feed.forEach(function(row, k) {
            out += "<tr>";
            out += inst.renderMustache(cells(), row);
            out += "</tr>";
        });
        return out;
    }

    /**
     * Build all the tbody cells
     * @return {string}
     */
    function cells() {
        let out = "";
        data.tbody.forEach(function(val, k) {
            out += "<td>";
            out += val;
            out += "</td>";
        });
        return out;
    }

    /**
     * Sort function to sort object cells
     * @param  {string} name Column name
     * @return {void}
     */
    function sort(name) {
        data.sort[name] = (!data.sort[name]) ? 1 : 0;
        if(data?.sort?.[name]) {
            data.feed.sort((a, b) => (b[name] ?? "").localeCompare(a[name] ?? ""));
        } else {
            data.feed.sort((a, b) => (a[name] ?? "").localeCompare(b[name] ?? ""));
        }
        inst.update();
    }
    
    // Return empty output if table data feed is empty
    if(!data.feed || data.feed.length <= 0) {
        return "";
    }
    return out;
}
```

The are a couple of new things you go through from above table component

#### eventOnload

```javascript
this.eventOnload(() => {
    // Your code here
});
```

This will ensure that the script inside the `eventOnload` will execute after the component has been executed.

#### update

```javascript
export function tableComponent(data, container, helper, builder) {
    
    const inst = this;
    
    function sort(name) {
        ...
        inst.update();
    }
...
```

The `sort` function will modify the data object feed, then execute `inst.update()` function, which will automatically tell Stratox to the component in the DOM element.
