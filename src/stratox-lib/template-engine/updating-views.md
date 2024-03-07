---
description: You can update views inside of a component and outside.
---

# Updating views

### Update view 1

Below, I will show you some ways you can update view content outside of the view.

```javascript
let stratox = new Stratox("#app");

const itemA = stratox.view("ingress#itemA", {
    headline: "Lorem ipsum dolor 1",
    content: "Lorem ipsum dolor sit amet",
});

const itemB = stratox.view("ingress#itemB", {
    headline: "Lorem ipsum dolor 2",
    content: "Lorem ipsum dolor sit amet",
});

stratox.execute();

const myBtn = document.getElementById("update-headline-btn");
myBtn.addEventListener("click", function (e) {
    e.preventDefault();
    itemA.set({ headline: "Headline 1 been updated!" }).update();
    itemB.set({ headline: "Headline 2 been updated!" }).update();
});
```

#### Result:

{% embed url="https://codepen.io/wazabii8/pen/gOEgNrW" %}

#### Update example 1

The above example is utilizing the components item variable to tell Stratox which component you want to update, e.g.:

```javascript
itemA.set({ headline: "Headline 1 been updated!" }).update();
```

There are tho 2 other ways you can also update the component with.

#### Update example 2

Utilize the view component setter again, but with updated values, and trigger the `stratox.update()` function to push changes to the view.

```javascript
stratox.view("ingress", { headline: "Headline been twice!" });
stratox.update();
```

#### Update example 3

Utilize the `stratox.update()` function and set argument 1 to the expected component name and argument 2 to an anonymous function. The anonymous function, in turn, has one expected object argument where you can access the component's data and modify it.

```javascript
stratox.update("ingress", function(obj) {
    obj.data.headline = "Headline updated thrice!";
});
```

### Update view 2

Below, I will show you some ways you can update view content **inside** of the view.

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
        });
    });

    return out;
}
```

When inside a component you only need to access the `this.update` function to update the component view.&#x20;

```javascript
this.update();
```

#### Result:

{% embed url="https://codepen.io/wazabii8/pen/OJqgLWY" %}

And that is that. Let's move on on how you can build to some forms
