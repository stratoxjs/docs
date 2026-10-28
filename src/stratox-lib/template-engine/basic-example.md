---
description: >-
  Your first Stratox component, step by step: write it, show it on a page, make it react to a click and update it
  from your own code.
---

# Quick start

This page builds one small component and puts it on a page. It assumes a project with a bundler and Stratox
installed; see [Installation](../installation.md).

## 1. Give Stratox an element

Stratox shows views inside elements of your page. Add an empty one where the component goes:

{% code title="index.html" %}
```html
<div id="app"></div>
<script type="module" src="/src/main.js"></script>
```
{% endcode %}

## 2. Write a component

A component is a function. It gets one object, takes what it needs from it, and returns markup written with the
`html` tag:

{% code title="src/components/greeting.js" %}
```js
export default function Greeting({ props, html }) {
  return html`
    <h1>Hello ${props.name}</h1>
    <p>You have ${props.messages} new messages.</p>
  `;
}
```
{% endcode %}

`props` is the data you give the view. `html` escapes every value you put into it, so a name such as
`<b>Ada</b>` is shown as text, never as markup.

## 3. Show it

Make a Stratox instance for the element, add the component as a view with its data, and run it:

{% code title="src/main.js" %}
```js
import { Stratox } from 'stratox';
import Greeting from './components/greeting.js';

const app = new Stratox('#app');
app.view(Greeting, { name: 'Ada', messages: 3 });
app.execute();
```
{% endcode %}

{% code title="Result" %}
```html
<div id="app">
  <h1>Hello Ada</h1>
  <p>You have 3 new messages.</p>
</div>
```
{% endcode %}

* `new Stratox('#app')` takes a CSS selector or an element.
* `app.view()` adds a view. The component's function name, `Greeting`, becomes the view's name.
* `app.execute()` renders every view of the instance into the element.

## 4. Update it

Change the view's data with `update()`, by the view's name. Stratox renders it again and changes only what differs on
the page:

```js
app.update('Greeting', { messages: 4 });
```

{% code title="Result" %}
```html
<div id="app">
  <h1>Hello Ada</h1>
  <p>You have 4 new messages.</p>
</div>
```
{% endcode %}

`update()` merges the new values into the data, so `name` stays `Ada`.

## 5. React to a click

`view` in the component's object is the view that renders it. `view.on()` writes an event handler into a tag. The
callback gets the view's data; change it, and the view renders again:

{% code title="src/components/greeting.js" %}
```js
export default function Greeting({ props, view, html }) {
  return html`
    <h1>Hello ${props.name}</h1>
    <p>You have ${props.messages} new messages.</p>
    <button type="button" ${view.on('click', (data) => { data.messages = 0; })}>Mark all as read</button>
  `;
}
```
{% endcode %}

{% code title="Result" %}
```html
<div id="app">
  <h1>Hello Ada</h1>
  <p>You have 3 new messages.</p>
  <button type="button" data-stratox-click="1">Mark all as read</button>
</div>
```
{% endcode %}

After a click on the button:

{% code title="Result after a click" %}
```html
<div id="app">
  <h1>Hello Ada</h1>
  <p>You have 0 new messages.</p>
  <button type="button" data-stratox-click="2">Mark all as read</button>
</div>
```
{% endcode %}

The `data-stratox-click` attribute is how Stratox finds the handler. It needs no inline JavaScript, so it also works
on sites with a strict Content Security Policy.

## Next

* [Components](create-templates.md): everything a component gets, and what it can return.
* [Showing views](show-templates.md): several views in one element, and the same component more than once.
* [Form builder](../form-builder/form-builder.md): forms with repeating rows, from a few lines or from JSON.
