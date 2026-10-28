---
description: >-
  Put components inside components in Stratox: partials render inline as part of the parent, blocks render into
  their own element after the parent and update on their own.
---

# Partials and blocks

A component can show other components inside its markup. There are two ways, and they differ in when the inner
component renders and what renders again on an update.

| | `view.partial()` | `view.block()` |
| --- | --- | --- |
| Renders | at once, inside the parent's markup | after the parent, into an element of its own |
| An update inside it renders | the parent | only the block |
| Good for | pieces of one view: a list item, a badge, a button | independent parts: a live counter, a widget |

## Partials

`view.partial(component, props)` renders a component and returns its markup, ready to put into `html`:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Price', ({ props, html }) => html`<span class="price">${props.amount} kr</span>`);

Stratox.setComponent('Product', ({ props, view, html }) => html`
  <article>
    <h2>${props.title}</h2>
    ${view.partial('Price', { amount: props.price })}
  </article>
`);

const shop = new Stratox('#shop');
shop.view('Product', { title: 'Desk lamp', price: 349 });
shop.execute();
```

{% code title="Result" %}
```html
<div id="shop">
  <article>
    <h2>Desk lamp</h2>
    <span class="price">349 kr</span>
  </article>
</div>
```
{% endcode %}

* The first argument is a component name, a component function, or `{ key: component }`, as for `view()`.
* A partial gets its own props, the object you pass; it does not see the parent's.
* A partial's markup is inserted as it is: it was escaped when the partial rendered.
* A partial renders again each time its parent does. To change it, change the parent's data.

Partials make lists of components short:

```js
Stratox.setComponent('PriceList', ({ props, view, html }) => html`
  <ul>
    ${props.items.map((item) => html`<li>${item.name}: ${view.partial('Price', { amount: item.price })}</li>`)}
  </ul>
`);

const prices = new Stratox('#prices');
prices.view('PriceList', { items: [{ name: 'Lamp', price: 349 }, { name: 'Bulb', price: 29 }] });
prices.execute();
```

{% code title="Result" %}
```html
<div id="prices">
  <ul>
    <li>Lamp: <span class="price">349 kr</span></li>
    <li>Bulb: <span class="price">29 kr</span></li>
  </ul>
</div>
```
{% endcode %}

## Blocks

`view.block(component, props)` writes an empty element into the parent's markup. After the parent has rendered,
the block renders into that element as a view of its own. An update inside the block renders only the block:

```js
Stratox.setComponent('Visitors', ({ props, view, html }) => html`
  <p>Visitors now: ${props.count}</p>
  <button type="button" ${view.on('click', (data) => { data.count += 1; })}>Count one more</button>
`);

let pageRenders = 0;
Stratox.setComponent('Dashboard', ({ view, html }) => {
  pageRenders += 1;
  return html`
    <h1>Dashboard</h1>
    ${view.block('Visitors', { count: 12 })}
  `;
});

const dashboard = new Stratox('#dashboard');
dashboard.view('Dashboard');
dashboard.execute();
```

After a click on the block's button, the block's text and `pageRenders`:

{% code title="Result after a click" %}
```
Visitors now: 13
pageRenders: 1
```
{% endcode %}

The click changed the block's own data and rendered the block; the dashboard around it did not render again.

* The block's element is a `<div>` with a generated id.
* A block renders after its parent, on a timer for the first render, so its content is not in what `execute()`
  returns.
* When the parent renders again, it writes a new, empty element, and the block renders into it again from the props
  you give it there. What the block changed in its own data is then gone: after a parent update the counter above
  shows 12 again. Keep data that must last in the parent's props, or in a [service](advanced-features/container.md).

## After a block has rendered

The third argument is a callback, called with the block's data, its view, its item and the element's selector once
the block has rendered:

```js
Stratox.setComponent('Weather', ({ props, html }) => html`<p>${props.temperature} °C</p>`);

Stratox.setComponent('Sidebar', ({ view, html }) => html`
  <aside>
    ${view.block('Weather', { temperature: 21 }, (data, blockView) => {
      console.log(`Weather block shows ${data.temperature} °C`);
    })}
  </aside>
`);

const sidebar = new Stratox('#sidebar');
sidebar.view('Sidebar');
sidebar.execute();
```

{% code title="Result" %}
```
Weather block shows 21 °C
```
{% endcode %}

Instead of a callback you can give `{ modify, response }`: `modify(view, item, element)` runs before the block
renders, `response` is the callback above. `partial()` takes `{ modify, response }` too: `modify(item)` before the
partial renders, `response` as for `execute()`.
