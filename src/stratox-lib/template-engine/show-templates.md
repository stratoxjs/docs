---
description: >-
  Show components on your page: one Stratox instance per element, several views in one element, the same component
  more than once, inline components, and HTML without an element.
---

# Showing views

A view is a component with its data, shown in an element of your page. You make a Stratox instance for the element,
add views to it, and call `execute()`.

## The element

`new Stratox()` takes a CSS selector, an element, or a list of elements:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Badge', ({ props, html }) => html`<span class="badge">${props.text}</span>`);

const bySelector = new Stratox('#status');
bySelector.view('Badge', { text: 'Open' });
bySelector.execute();

const byElement = new Stratox(document.getElementById('stock'));
byElement.view('Badge', { text: 'In stock' });
byElement.execute();
```

{% code title="Result" %}
```html
<div id="status">
  <span class="badge">Open</span>
</div>
<div id="stock">
  <span class="badge">In stock</span>
</div>
```
{% endcode %}

Stratox replaces the content of the element; the element itself stays. A page can have as many instances as it has
elements to fill.

## Several views in one element

Views of one instance render in the order you add them:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Heading', ({ props, html }) => html`<h2>${props.text}</h2>`);
Stratox.setComponent('Paragraph', ({ props, html }) => html`<p>${props.text}</p>`);

const article = new Stratox('#article');
article.view('Heading', { text: 'Opening hours' });
article.view('Paragraph', { text: 'Monday to Friday, 9 to 17.' });
article.execute();
```

{% code title="Result" %}
```html
<div id="article">
  <h2>Opening hours</h2>
  <p>Monday to Friday, 9 to 17.</p>
</div>
```
{% endcode %}

## The same component more than once

Each view in an instance has a name, and a name is used once. To show one component several times, give each view
its own key after a `#`:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Product', ({ props, html }) => html`<h3>${props.title}</h3>`);

const products = new Stratox('#products');
products.view('Product#lamp', { title: 'Desk lamp' });
products.view('Product#chair', { title: 'Office chair' });
products.execute();
```

{% code title="Result" %}
```html
<div id="products">
  <h3>Desk lamp</h3>
  <h3>Office chair</h3>
</div>
```
{% endcode %}

## Showing a view again

Calling `view()` again with the same name after `execute()` merges the new data into the view. It renders on the
next `update()` or `execute()`:

```js
products.view('Product#lamp', { title: 'Desk lamp, white' });
products.update();
```

{% code title="Result" %}
```html
<div id="products">
  <h3>Desk lamp, white</h3>
  <h3>Office chair</h3>
</div>
```
{% endcode %}

With an imported component function, write the key as an object, `{ key: Component }`:

{% code title="src/main.js" %}
```js
import { Stratox } from 'stratox';
import Product from './components/product.js';

const offers = new Stratox('#offers');
offers.view({ lamp: Product }, { title: 'Desk lamp' });
offers.view({ chair: Product }, { title: 'Office chair' });
offers.execute();
```
{% endcode %}

{% code title="src/components/product.js" %}
```js
export default function Product({ props, html }) {
  return html`<h3>${props.title}</h3>`;
}
```
{% endcode %}

{% code title="Result" %}
```html
<div id="offers">
  <h3>Desk lamp</h3>
  <h3>Office chair</h3>
</div>
```
{% endcode %}

The views are named `Product#lamp` and `Product#chair`: that is the name you give `update()`.

## Inline components

A component can also be written right in the call. This suits small views:

```js
import { Stratox } from 'stratox';

const notice = new Stratox('#notice');
const item = notice.view(({ props, html }) => html`<p class="notice">${props.text}</p>`, {
  text: 'We are closed on Friday.',
});
notice.execute();
```

{% code title="Result" %}
```html
<div id="notice">
  <p class="notice">We are closed on Friday.</p>
</div>
```
{% endcode %}

An inline function has no name to update it by, so keep what `view()` returns. It is the view's item, and
`item.update()` changes it:

```js
item.update({ text: 'We are open again on Monday.' });
```

{% code title="Result" %}
```html
<div id="notice">
  <p class="notice">We are open again on Monday.</p>
</div>
```
{% endcode %}

## HTML without an element

`execute()` returns the HTML it rendered. Without an element, that is all it does, so you can use Stratox to build
HTML for something else:

```js
import { Stratox } from 'stratox';

const mail = new Stratox();
mail.view(({ props, html }) => html`<p>Hello ${props.name}, your order has shipped.</p>`, { name: 'Ada' });
console.log(mail.execute());
```

{% code title="Result" %}
```
<p>Hello Ada, your order has shipped.</p>
```
{% endcode %}

`execute()` returns `''` while components are still being loaded from the `directory` setting; then wait for
`await view.ready()` and read `view.getResponse()`.
