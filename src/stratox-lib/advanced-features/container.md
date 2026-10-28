---
description: >-
  Share objects and functions between Stratox components with the app's service container: set values and
  factories once, and every component gets them by name.
---

# Services

Each app has a container of services: values and functions you set once and every component of the app can use. Use
it for things several views share, such as a cart, an API client or the signed-in user.

## Set a service, use it in a component

Get the default app's container with `Stratox.app.container()`, and set services on it. A component gets every service
in its object, by name:

```js
import { Stratox } from 'stratox';

const services = Stratox.app.container();
services.set('user', { name: 'Ada' });

Stratox.setComponent('Greeting', ({ user, html }) => html`<p>Welcome back, ${user.name}</p>`);

const greeting = new Stratox('#greeting');
greeting.view('Greeting');
greeting.execute();
```

{% code title="Result" %}
```html
<div id="greeting">
  <p>Welcome back, Ada</p>
</div>
```
{% endcode %}

The component's `services` is the container itself, so `services.get('user')` gives the same object. The keys
Stratox gives every component (`props`, `view`, `html` and so on) win over a service with the same name.

For an app of your own, use `app.container()`. In a view, `view.container()` gives its app's container.

## Views that share a service

A service is one object for all views of the app, so it connects views that do not know each other. Here a product
list and a cart counter, in two places on the page, share a cart:

```js
const cart = {
  items: [],
  views: [],
  add(item) {
    this.items.push(item);
    this.views.forEach((view) => view.update());
  },
};
services.set('cart', cart);

Stratox.setComponent('Products', ({ props, cart, view, html }) => html`
  ${props.products.map((product) => html`
    <button type="button" ${view.on('click', () => cart.add(product), false)}>Add ${product}</button>
  `)}
`);

Stratox.setComponent('CartCount', ({ cart, html }) => html`<p>In your cart: ${cart.items.length}</p>`);

const products = new Stratox('#products');
products.view('Products', { products: ['Lamp', 'Chair'] });
products.execute();

const counter = new Stratox('#counter');
counter.view('CartCount');
counter.execute();
cart.views.push(counter);
```

After clicks on both buttons:

{% code title="Result after two clicks" %}
```html
<div id="counter">
  <p>In your cart: 2</p>
</div>
```
{% endcode %}

## Factories

A function set with `setFactory()` is called each time you get it, with the arguments you pass to `get()`. `this`
is the container, so a factory can use other services:

```js
services.setFactory('price', function price(amount) {
  return `${amount.toFixed(2)} ${this.get('currency')}`;
});
services.set('currency', 'kr');

console.log(services.get('price', 349));
```

{% code title="Result" %}
```
349.00 kr
```
{% endcode %}

In a component's object a factory comes as the function itself: call it there, or use `services.get('price', 349)`.

## A name is set once

Setting a name that is already set throws, so two scripts cannot replace each other's services by accident. Pass
`true` as the third argument when you mean to replace it:

```js
try {
  services.set('currency', 'SEK');
} catch (error) {
  console.log('currency is already set');
}

services.set('currency', 'SEK', true);
console.log(services.get('currency'));
```

{% code title="Result" %}
```
currency is already set
SEK
```
{% endcode %}

## Methods

| Method | What it does |
| --- | --- |
| `set(name, value, overwrite)` | Sets a service. Throws if the name is set, unless `overwrite` is `true`. A function set this way is a factory too. |
| `setFactory(name, function, overwrite)` | Sets a factory, with the same rule. |
| `get(name, ...args)` | The service; a factory is called with `args`. Throws if the name is not set. |
| `has(name)` | Whether the name is set, also when its value is `false`, `null` or `undefined`. |
| `read(name, fallback)` | The service, or `fallback` (default `''`) when the name is not set. |
| `isFactory(name)`, `isContainer(name)` | Whether the service is a function, or any other value. |
| `list()` | All services by name. |
| `resetAll(keep)` | Removes all services except the names in the `keep` array. |

## On its own

The container works without views too. Import the class and make one:

```js
import { StratoxContainer } from 'stratox';

const settings = new StratoxContainer();
settings.set('theme', 'dark');
console.log(settings.has('theme'), settings.read('language', 'en'));
```

{% code title="Result" %}
```
true en
```
{% endcode %}
