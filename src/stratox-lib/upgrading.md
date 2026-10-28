---
description: >-
  What changed from Stratox 3.x to 4.0 and how to update your code: imports, components, escaping, form templates,
  removed members and the new features.
---

# Upgrading from 3.x

Stratox 4 is safer and faster to work with: markup is escaped by default, updates keep focus and typed text, events
work without inline JavaScript, several apps can share one page, and the package ships TypeScript types. A few
changes need edits in your code. Most sites only need the first four sections.

## Checklist

1. Import from `stratox` only, never from a file inside the package.
2. Components get one object: `{ props, services, helper, context, view, update, html, raw, escape }`.
3. Return markup with `` html`...` `` (or `raw()`); a plain string is now shown as text.
4. Import `StratoxBuilder` and `StratoxTemplate` from `stratox` in custom form templates.
5. Replace removed members (table below).
6. Listeners you add to elements on every update: move them to `view.on()`, or set `morph: false`.
7. Code that relies on what `execute()` returns, with components loaded from a folder: wait for `view.ready()`.
8. `context.bind()` callbacks: `(props, view, item, event)` instead of `(event, data, name)`.

## 1. Install and import

The package now ships a build in `dist/`, and only its entry can be imported. Paths inside the package fail.

{% code title="3.x" %}
```js
import { Stratox } from 'stratox';
import StratoxTemplate from 'stratox/src/StratoxTemplate.js';
```
{% endcode %}

{% code title="4.0" %}
```js
import { Stratox, StratoxTemplate, StratoxBuilder } from 'stratox';
```
{% endcode %}

New named exports in 4.0: `StratoxApp`, `StratoxBuilder`, `html`, `raw`, `escape` and `SafeHtml`, next to `Stratox`,
`StratoxContainer`, `StratoxObserver` and `StratoxTemplate`.

From a CDN, load the build: `https://cdn.jsdelivr.net/npm/stratox@4/dist/index.js`.

## 2. Components get one object

In 3.x a component got positional arguments, unless its source showed one destructured parameter. In 4.0 every
component gets the same object, whatever it looks like and however it was minified.

{% code title="3.x" %}
```js
function Card(props, container, helper, builder) {
  return `<h2>${props.title}</h2>`;
}
```
{% endcode %}

{% code title="4.0" %}
```js
import { Stratox } from 'stratox';

function Card({ props, services, helper, context, html }) {
  return html`<h2>${props.title}</h2>`;
}

const cards = new Stratox('#cards');
cards.view(Card, { title: 'Lamp' });
cards.execute();
```
{% endcode %}

{% code title="Result" %}
```html
<div id="cards">
  <h2>Lamp</h2>
</div>
```
{% endcode %}

| 3.x argument | 4.0 key |
| --- | --- |
| `props` (1st) | `props` |
| `container` (2nd) | `services` |
| `helper` (3rd) | `helper` |
| `builder` (4th) | `context` |

Also in the object: `view` (the view rendering the component, also `this`), `update`, `html`, `raw`, `escape`, and
every service of the app's container by its own name. Built-in keys win over a service with the same name.

## 3. Markup is escaped by default

{% hint style="warning" %}
A component that returns a plain template string shows its HTML as text in 4.0.
{% endhint %}

This 3.x component:

{% code title="3.x component in 4.0" %}
```js
import { Stratox } from 'stratox';

Stratox.setComponent('Greeting', ({ props }) => `<h1>${props.name}</h1>`);

const greeting = new Stratox('#greeting');
greeting.view('Greeting', { name: 'Ada' });
greeting.execute();
```
{% endcode %}

shows its tags as text:

{% code title="Result" %}
```html
<div id="greeting">&lt;h1&gt;Ada&lt;/h1&gt;</div>
```
{% endcode %}

Use the `html` tag. It escapes every value you put into it, so data from users and servers cannot inject markup:

{% code title="4.0" %}
```js
import { Stratox } from 'stratox';

Stratox.setComponent('Greeting', ({ props, html }) => html`<h1>${props.name}</h1>`);

const greeting = new Stratox('#greeting');
greeting.view('Greeting', { name: 'Ada' });
greeting.execute();
```
{% endcode %}

{% code title="Result" %}
```html
<div id="greeting">
  <h1>Ada</h1>
</div>
```
{% endcode %}

* Markup you trust, such as HTML from your own CMS, goes through `raw()`: `` html`<div>${raw(post.body)}</div>` ``.
* Lists work as they are: `` html`<ul>${items.map((item) => html`<li>${item}</li>`)}</ul>` ``.
* `null`, `undefined` and `false` render nothing.
* The results of `view.partial()` and `view.block()` are markup and are inserted as they are.
* A `javascript:` or `vbscript:` URL put into `href`, `src`, `action`, `formaction` or `xlink:href` gets the prefix
  `unsafe:`.
* In the form builder, values and attributes are escaped too. Labels, descriptions and option titles are still
  inserted as HTML, so they must come from a source you trust.

## 4. Custom form templates

Extend the classes from the package entry. Everything a template uses (`this.container()`, `this.input()`,
`this.getAttr()`, `this.label`, `this.value` and so on) is unchanged.

{% code title="4.0" %}
```js
import { Stratox, StratoxTemplate } from 'stratox';

class MyFields extends StratoxTemplate {
  rating(helper) {
    return this.container(() => this.input(helper, { type: 'range', min: 1, max: 5 }));
  }
}

Stratox.setConfigs({ handlers: { fields: MyFields } });

const review = new Stratox('#review');
review.form('score', { type: 'rating', label: 'Your rating' });
review.execute();
```
{% endcode %}

{% code title="Result" %}
```html
<div id="review">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-score w-full">
    <label>Your rating<div class="message hide"></div></label>
    <input type="range" name="score" value="" data-index="0" min="1" max="5" data-name="score">
  </div>
</div>
```
{% endcode %}

`setConfigs()` now merges `handlers`, `classes` and `icons` one level deep, so you no longer have to repeat
`handlers.helper` when you set `handlers.fields`. A form handler must be `StratoxBuilder` or a class that extends it.

## 5. Removed members

None of these were documented. If your code uses one, here is the replacement:

| 3.x | 4.0 |
| --- | --- |
| `Stratox.container` | `Stratox.app.container()`, or `view.container()` |
| `Stratox.viewCount`, `Stratox.funcIndex` | removed; `view.getID()` gives unique ids |
| `StratoxBuilder.setComponent(name, fn)` | `Stratox.setComponent(name, fn)`, or `app.setComponent(name, fn)` |
| `StratoxBuilder.factory`, `StratoxBuilder.funcIndex` | removed |
| `Stratox.create()`, `Stratox.withObserver()`, `view.observer()` | removed; `StratoxObserver.notified(fn)` is the global hook |
| `view.read()` | `view.getItem()`, or `view.update(name, (data, item) => ...)` |
| `view.isArray(value)` | `Array.isArray(value)` |
| `view.getUniqueElem()` | `view.getID()` |
| `StratoxContainer.open()` | the app's container: `Stratox.app.container()` |

These internal methods can no longer be called: `build`, `insertHtml`, `html`, `viewEngine`, `partialEngine`,
`attachViewToEl`, `clone`, `setSelector`, `modifyValue`, `addGroupField`, `deleteGroupField`, `editFieldValue`,
`startFormEvents`, `bindGroupEvents`, `genRandStr`, `getViewCount`. Use the public API around them: `view()`,
`partial()`, `block()`, `update()`, `execute()`, `getElement()`, `setValues()` and `getValues()`.

## 6. Updates change the page in place

When a view renders again, Stratox 4 changes only what differs instead of replacing the whole content. Focus, the
cursor, typed text and scroll positions survive. Two things behave differently:

* **Listeners you add yourself:** if your code adds a listener to an element inside a view after every update (in
  `view.done()`), the element is often kept now, so the listeners add up. Use `view.on()` in the markup, or
  `view.bindEvent()` on the view's element, which both work across updates.
* **During an event,** a listener that renders at once changes the elements that later listeners see.

To get the 3.x behaviour back: `Stratox.setConfigs({ morph: false })`.

## 7. Rendering and components loaded from a folder

* `execute(callback)` calls the callback once per render, after every component is there, also those imported from
  the `directory` setting. In 3.x it could run once per imported module.
* With components that still have to be imported, `execute()` returns `''` until they are loaded. Wait for them with
  `await view.ready()`, which also rejects with a component's error.
* A component that throws is logged with `console.error` instead of ending in an unhandled promise rejection, and
  the rest of the view keeps updating.
* A form field whose `type` names a component that is not registered is imported from the `directory` too, at any
  depth. Such a form renders once the import is done.
* With `cache: false`, the query string that stops the browser cache is the same for every import of one app during
  a page load.

## 8. Events

New: `view.on(eventName, fn)` returns an attribute to write inside a tag. It needs no inline JavaScript, so it works
with a strict Content Security Policy. The callback is the same as `bind()`'s, and it does not call
`preventDefault()`.

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Counter', ({ props, view, html }) => html`
  <p>Count: ${props.count}</p>
  <button type="button" ${view.on('click', (data) => { data.count += 1; })}>Add one</button>
`);

const counter = new Stratox('#counter');
counter.view('Counter', { count: 0 });
counter.execute();
```

{% code title="Result after a click" %}
```html
<div id="counter">
  <p>Count: 1</p>
  <button type="button" data-stratox-click="2">Add one</button>
</div>
```
{% endcode %}

`view.bind()` works as before (`onclick="${view.bind(fn)}"`). Its handlers are replaced on every render instead of
piling up as window functions.

The form builder's `context.bind()` now calls back like `view.bind()`, with `this` the view:

{% code title="3.x" %}
```js
context.bind((event, data, name) => { /* ... */ });
```
{% endcode %}

{% code title="4.0" %}
```js
context.bind((props, view, item, event) => { /* ... */ });
```
{% endcode %}

## 9. New: several apps on one page

Settings, components and services used to be global for the whole page. They now belong to an app. The static API
(`Stratox.setConfigs()`, `Stratox.setComponent()`, `Stratox.getConfigs()`) works on the default app, so existing
pages need no change. To keep two parts of a page apart, give one its own app:

```js
import { Stratox } from 'stratox';

const shop = Stratox.createApp({ directory: '/shop/components/' });
shop.setComponent('Cart', ({ props, html }) => html`<p>Items in your cart: ${props.items}</p>`);

const cart = new Stratox('#cart', shop);
cart.view('Cart', { items: 2 });
cart.execute();
```

{% code title="Result" %}
```html
<div id="cart">
  <p>Items in your cart: 2</p>
</div>
```
{% endcode %}

`Cart` is only known to the `shop` app, so a view of the default app does not find it. `view.getApp()` returns a view's
app; `view.getComponent(name)` returns a registered component, or `null`.

## 10. Form builder changes you may see

These are fixes, but a page that relied on the old output may look different:

* An attribute set to `false`, `null` or `undefined` is left out, and `true` writes the bare attribute. In 3.x
  `disabled: false` disabled the field.
* `null` from `setValues()` renders an empty field instead of the text "null"; numbers render as text.
* Numeric values select and check their options (`1` checks the item `"1"`), also inside arrays.
* A checkbox with several items keeps an array of the checked values while the user types.
* With `nestedNames`, a child field with its own `name` keeps the row path: `rows[0][custom]`.
* `conAttr.id` replaces the generated container id instead of adding a second one, and a group's wrapper gets its
  own id (`...-group`). Field ids stay the same across renders.
* A form without a root element marks its field containers with `data-stratox`, and only handles its own fields.
* A `form()` item passed to `setFields()` renders only inside the group.
* A `checkbox` in a group's fields without `items` logs a warning instead of throwing.
* The show-password button that `attr.type: 'password'` added (it had no script) is gone.
* New settings `classes` and `icons` replace the built-in layout classes and button icons.
* `setValues()` after `execute()` shows the new values on the next render; `getValues()` returns the live values.
* `context.setDefault()` works in a component used as a group field without `data`.

## 11. Other fixes you may notice

* `view(name, data)` again after `execute()` updates that view with the new data.
* `update()` and `updateAll()` before `execute()` change the data; the first `execute()` renders it.
* `update(name, data)` also finds form items by name, and a numeric view key works.
* `new Stratox(element)` and `setElement(element)` take a DOM element or a list of elements, not only a selector.
* Two components with the same function name, or anonymous ones, no longer render each other.
* An unknown component logs its error; later updates keep working.
* The container stores `false`, `null` and `undefined` values: `has()` is `true` for them.
* `off()` removes every listener that `bindEvent()` added to an element.
* `StratoxObserver.notified(fn)` registers a hook without replacing the method.

## TypeScript

Stratox 4 is written in TypeScript and ships its types. No `@types` package is needed. Type a component by its props:

```ts
import { Stratox } from 'stratox';
import type { Component } from 'stratox';

const Title: Component<{ title: string }> = ({ props, html }) => html`<h2>${props.title}</h2>`;
Stratox.setComponent('Title', Title);
```
