---
description: Here will be an over view list over all template views that you can use.
---

# Template view functions

**This guide is not complete and more will come.**

#### eventOnload

This will ensure that the script inside the `eventOnload` will execute after the component has been executed.

```javascript
this.eventOnload(() => {
    // Your code here
});
```

#### update

You can trigger `this.update` function inside a view to update the component views content.&#x20;

```javascript
this.update();
```

#### setElement

You can set a new or change the current expected **Main** element inside your template view.

```javascript
this.setElement("#your-element");
```

#### withView

Add a view inside a view.

```javascript
this.withView("tooltip", {
    headline: "Lorem",
    message: "Lorem ipsum dolor"
}).getResponse()
```

**renderMustache**

Render object data where expected Mustache brackets exist.

```javascript
let output = this.renderMustache("<h2>{{headline}}</h2>", {
    headline: "Lorem",
    message: "Lorem ipsum dolor"
});
console.log(output);
// Result: <h2>Lorem</h2>
```
