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
export default function ingressComponent({ 
    props, update, view, context, services, helper 
}) {
    return `
    <header class="mb-50 align-center">
        <h1>${props.headline}</h1>
        <p>${props.content}</p>
    </header>
    `;
}
```

Let's start breaking down the example above. First of all, you will need to create a function that is exported. You can name it whatever you want. The function can utilize four arguments, which I have named: **data, container, helper** and **builder**.

#### The function arguments

* **props:** This is the object data passed to your template file.
* **view:** The main Stratox view instance.
* **update:** can be used to update view, e.g. `update({ headline: "Headline has been updated!" });`
* **context:** Access the Stratox builder library (you can manage without, only for advanced users; more on this later on).
* **services:** The services container where you can add services and communicate with your nested partial views.
* **helper:** Your own possible helper libraries, objects, and functions you passed in the configuration.

#### The content

What you place inside the function (component) doesn't actually matter much. Most of the time, you would probably want to create some kind of view, as I have done above. However, you can place function withing the function to which is a recommended way to organize a more advanced component view. You could also for example use it to create a component that will trigger some kind of function. The only thing that sets the boundaries is your creativity. I will show you a little more advance template bellow.

#### The Return

It is not required to return an output, but if you do, it is expected to return a string value that will automatically be appended to a DOM element if you have specified an element in the Stratox class initialization. If you do not return a value, then your component could handle the appending to DOM part, for example, dynamically append Modals and so on.

### Show the template

Now we only need to use the template, as we already discussed on the previous page. It is not harder than this:

```javascript
import ingressComponent from './path/to/ingressComponent';

let stratox = new Stratox("#app");

stratox.view(ingressComponent", {
  headline: "Lorem ipsum dolor",
  content: "Lorem ipsum dolor sit amet"
});

stratox.execute();
```

#### The result:

{% embed url="https://codepen.io/wazabii8/pen/bGZgPNo" %}
