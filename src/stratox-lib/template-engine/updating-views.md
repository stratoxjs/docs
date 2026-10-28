---
description: >-
  Change a view's data and render it again: update() by name, with a callback or for every view, through the view's
  item, or from inside the component. Updates change only what differs.
---

# Updating views

A view renders from its data. To change what it shows, change the data and render again. Stratox then changes only
the parts of the page that differ.

The examples on this page use this component:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Order', ({ props, html }) => html`
  <p>Order ${props.id}: ${props.status}</p>
`);

const orders = new Stratox('#orders');
const first = orders.view('Order#first', { id: 101, status: 'received' });
orders.view('Order#second', { id: 102, status: 'received' });
orders.execute();
```

{% code title="Result" %}
```html
<div id="orders">
  <p>Order 101: received</p>
  <p>Order 102: received</p>
</div>
```
{% endcode %}

## update() by name

`update(name, data)` merges the data into the view with that name and renders:

```js
orders.update('Order#second', { status: 'packed' });
```

{% code title="Result" %}
```html
<div id="orders">
  <p>Order 101: received</p>
  <p>Order 102: packed</p>
</div>
```
{% endcode %}

A view added without a key, such as `view('Order', ...)`, is updated by its plain name: `update('Order', data)`.

## update() with a callback

Give a callback instead of data to change the data yourself. It gets the view's data and its item:

```js
orders.update('Order#first', (data) => {
  data.status = `${data.status}, paid`;
});
```

{% code title="Result" %}
```html
<div id="orders">
  <p>Order 101: received, paid</p>
  <p>Order 102: packed</p>
</div>
```
{% endcode %}

Without a name, the callback runs once for every view of the instance, then everything renders once:

```js
orders.update((data) => {
  data.status = 'shipped';
});
```

{% code title="Result" %}
```html
<div id="orders">
  <p>Order 101: shipped</p>
  <p>Order 102: shipped</p>
</div>
```
{% endcode %}

`orders.updateAll(callback)` does the same. `updateAll(callback, false)` changes the data without rendering, for
when you render later yourself.

## Through the view's item

`view()` returns the view's item. Its `update()` changes that view, without a name:

```js
first.update({ status: 'delivered' });
```

{% code title="Result" %}
```html
<div id="orders">
  <p>Order 101: delivered</p>
  <p>Order 102: shipped</p>
</div>
```
{% endcode %}

`item.set(data)` merges data without rendering, and returns the item, so `first.set({ status: 'returned' }).update()`
does both.

## Render again as it is

`update()` without arguments renders every view of the instance again, for example after you changed the data
objects yourself:

```js
first.data.status = 'returned';
orders.update();
```

{% code title="Result" %}
```html
<div id="orders">
  <p>Order 101: returned</p>
  <p>Order 102: shipped</p>
</div>
```
{% endcode %}

## From inside the component

A component gets `update` in its object. `update(data)` merges into its own data and renders; `update(name, data)`
works like the instance's `update()`. Updates from events are on the [Events](../events.md) page.

## Before execute()

`update()` before `execute()` only changes the data. The first `execute()` renders it:

```js
const queue = new Stratox('#queue');
queue.view('Order', { id: 103, status: 'received' });
queue.update('Order', { status: 'on hold' });
queue.execute();
```

{% code title="Result" %}
```html
<div id="queue">
  <p>Order 103: on hold</p>
</div>
```
{% endcode %}

## Updates change the page in place

When a view renders again, Stratox compares the new HTML with the page and changes only what differs. Elements that
stay keep their state: focus, the cursor and typed text in a field, scroll positions, and listeners you added to them.

This is the `morph` setting, on by default. With `Stratox.setConfigs({ morph: false })`, every render replaces the
whole content of the element instead, as Stratox 3 did.

## After each render

`view.done(callback)` is called after the first render with `'load'` as its third argument, and after every later
render with `'update'`. `this` is the view:

```js
const log = new Stratox('#log');
log.view('Order', { id: 104, status: 'received' });
log.done((context, observer, reason) => {
  console.log(`rendered: ${reason}`);
});
log.execute();
```

{% code title="Result" %}
```
rendered: load
```
{% endcode %}

Later, an update:

```js
log.update('Order', { status: 'packed' });
```

{% code title="Result" %}
```
rendered: update
```
{% endcode %}

The first render calls `done` on a timer, after `execute()` has returned. To work with the rendered elements, use
`view.getElement()`, which returns the instance's elements.
