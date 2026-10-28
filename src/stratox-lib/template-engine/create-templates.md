---
description: >-
  A Stratox component is a function that gets one object and returns markup. What is in the object, how to
  register a component, and what it can return.
---

# Components

A component is a function. Stratox calls it with one object and shows what it returns. Take what you need from the
object:

{% code title="src/components/product.js" %}
```js
export default function Product({ props, html }) {
  return html`
    <article class="product">
      <h2>${props.title}</h2>
      <p>${props.price} kr</p>
    </article>
  `;
}
```
{% endcode %}

{% code title="src/main.js" %}
```js
import { Stratox } from 'stratox';
import Product from './components/product.js';

const shop = new Stratox('#shop');
shop.view(Product, { title: 'Desk lamp', price: 349 });
shop.execute();
```
{% endcode %}

{% code title="Result" %}
```html
<div id="shop">
  <article class="product">
    <h2>Desk lamp</h2>
    <p>349 kr</p>
  </article>
</div>
```
{% endcode %}

## What the object holds

| Key | What it is |
| --- | --- |
| `props` | The view's data: the object you pass to `view()`. Change it, then render again, to update the view. |
| `html` | The tag for markup. It escapes every value you put into it. See [Safe markup](../safe-markup.md). |
| `raw` | Marks markup you trust, so `html` inserts it as it is. |
| `escape` | Escapes one value to text. |
| `view` | The Stratox instance that renders the component. Also `this` in a `function` component. |
| `update` | Renders again. `update({ count: 2 })` merges the data into this component's `props` first. |
| `services` | The app's container of services. Every service is also in the object under its own name. |
| `helper` | What the `handlers.helper` setting gives. |
| `context` | The form builder that renders the component. Used by components that act as form fields. |

The names are fixed: write `({ props, html })`, not `({ data, tag })`. The order does not matter, and you can
leave out what you do not use.

## Register a component by name

Pass the function to `view()`, as above, and its function name becomes the view's name. Or register it once under a
name of your choice and use that name everywhere:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Price', ({ props, html }) => html`<span class="price">${props.amount} kr</span>`);

const total = new Stratox('#total');
total.view('Price', { amount: 1290 });
total.execute();
```

{% code title="Result" %}
```html
<div id="total">
  <span class="price">1290 kr</span>
</div>
```
{% endcode %}

`Stratox.setComponent()` registers in the default app, which every view uses unless you give it an app of its own.
`view.getComponent(name)` returns a registered component, or `null`.

## What a component returns

| It returns | Stratox shows |
| --- | --- |
| `` html`...` `` | The markup, with every value in it escaped. |
| `raw(markup)` | The markup as it is. Only for markup you trust. |
| a string or a number | Text: `'<b>Hi</b>'` shows the tags as text. |
| `null`, `undefined`, `false` or `''` | Nothing. |

A plain string is text, not markup:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Note', () => '<b>Saved</b>');

const note = new Stratox('#note');
note.view('Note');
note.execute();
```

{% code title="Result" %}
```html
<div id="note">&lt;b&gt;Saved&lt;/b&gt;</div>
```
{% endcode %}

## Lists and conditions

Inside `html`, an array is joined, and `null`, `undefined` and `false` render nothing. A list is a `map()`, a
condition is `&&` or a ternary:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Cart', ({ props, html }) => html`
  <ul>
    ${props.items.map((item) => html`<li>${item.name}: ${item.quantity}</li>`)}
  </ul>
  ${props.items.length === 0 && html`<p>Your cart is empty.</p>`}
  ${props.coupon ? html`<p>Coupon: ${props.coupon}</p>` : null}
`);

const cart = new Stratox('#cart');
cart.view('Cart', { items: [{ name: 'Lamp', quantity: 1 }, { name: 'Bulb', quantity: 4 }], coupon: null });
cart.execute();
```

{% code title="Result" %}
```html
<div id="cart">
  <ul>
    <li>Lamp: 1</li>
    <li>Bulb: 4</li>
  </ul>
</div>
```
{% endcode %}

Put every nested piece of markup in its own `` html`...` ``. A plain template string inside `html` is a value, so it
is escaped like any other.

## Updating from inside

`update` renders the component again. Called with an object, it first merges that object into the component's
`props`:

```js
import { Stratox } from 'stratox';

// In a real page this is a fetch() to your server
const loadTemperature = async () => 21;

Stratox.setComponent('Weather', ({ props, update, html }) => {
  if (props.temperature === undefined) {
    loadTemperature().then((temperature) => update({ temperature }));
    return html`<p>Loading the weather…</p>`;
  }
  return html`<p>${props.temperature} °C</p>`;
});

const weather = new Stratox('#weather');
weather.view('Weather');
weather.execute();
```

{% code title="Result" %}
```html
<div id="weather">
  <p>21 °C</p>
</div>
```
{% endcode %}

Most updates come from events or from your own code; see [Events](../events.md) and [Updating
views](updating-views.md).

## Errors

A component that throws is logged with `console.error`, and the rest of the page keeps working. `view.ready()`
rejects with the error, so you can handle it:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Broken', () => {
  throw new Error('No data');
});

const broken = new Stratox('#broken');
broken.view('Broken');
broken.execute();

try {
  await broken.ready();
} catch (error) {
  console.log(`Could not render: ${error.message}`);
}
```

{% code title="Result" %}
```
Could not render: No data
```
{% endcode %}

## TypeScript

Type a component by its props with `Component`. `props` then has your type, and every other key is typed too:

```ts
import { Stratox } from 'stratox';
import type { Component } from 'stratox';

interface ProductProps {
  title: string;
  price: number;
}

const ProductCard: Component<ProductProps> = ({ props, html }) => html`
  <h2>${props.title}</h2>
  <p>${props.price.toFixed(2)} kr</p>
`;

Stratox.setComponent('ProductCard', ProductCard);
```
