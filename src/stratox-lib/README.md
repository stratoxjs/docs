---
description: >-
  Stratox is a tiny JavaScript library that makes any website dynamic: components, live updates and a complete
  form builder for the pages you already have.
---

# Introduction

**Make any website dynamic. Build complex forms in minutes.**

Stratox is a tiny JavaScript library that brings components, live updates and a complete form builder to the sites
you already have. Keep your backend, your HTML and your CSS: point Stratox at an element, and that part of the page
comes alive.

```bash
npm install stratox
```

## Why Stratox

* **A form builder that does the hard part.** Repeating rows, rows inside rows, selects, radios, checkboxes and your
  own field types. Describe the form in a few lines, or send it from the server as JSON. Stratox renders it, keeps
  every value in sync, and names every input so your backend receives clean, nested data.
* **Dynamic, without a rewrite.** No single-page app, no takeover. Stratox only fills the elements you give it, so a
  PHP, Python, Ruby, .NET or CMS page stays exactly what it is, with interactive parts where you want them.
* **Tiny.** About 9 kB minified and gzipped, form builder included. Zero dependencies.
* **Easy.** A component is a function that returns HTML. If you can write a template string, you can write Stratox.
* **Safe by default.** Every value you put into markup is escaped, so data from users and servers cannot inject HTML.
* **Smooth updates.** On every change Stratox updates only what differs, so focus, the cursor and typed text stay
  where they are.
* **TypeScript included.** Written in TypeScript, with types for components, props and forms.

## Forms in minutes

Your page has a form with an empty element in it:

```html
<form method="post" action="/events">
  <div id="event-form"></div>
  <button type="submit">Save</button>
</form>
```

Stratox fills it:

```js
import { Stratox, StratoxTemplate } from 'stratox';

// Turn on the built-in field types
Stratox.setConfigs({ handlers: { fields: StratoxTemplate } });

const form = new Stratox('#event-form');

form.form('name').setLabel('Event name');
form.form('category', {
  type: 'select',
  label: 'Category',
  items: { tech: 'Technology', design: 'Design' },
});

// Sessions the user can add and remove, each with speakers they can add and remove
form.form('sessions', { type: 'group', label: 'Sessions' })
  .setConfig({ nestedNames: true, controls: true })
  .setFields({
    title: { type: 'text', label: 'Session title' },
    speakers: {
      type: 'group',
      label: 'Speakers',
      config: { nestedNames: true, controls: true },
      fields: { name: { type: 'text', label: 'Speaker' } },
    },
  });

// Saved answers fill the form, rows included
form.setValues({
  name: 'Static Sites Day',
  category: 'tech',
  sessions: [{ title: 'Forms from JSON', speakers: [{ name: 'Ada' }, { name: 'Linus' }] }],
});

form.execute();
```

That is the whole form: labels, inputs, and buttons to add and remove rows at both levels. It submits like any other
form, and every input already has the name your backend needs:

{% code title="Result: what the form posts" %}
```
name = Static Sites Day
category = tech
sessions[0][title] = Forms from JSON
sessions[0][speakers][0][name] = Ada
sessions[0][speakers][1][name] = Linus
```
{% endcode %}

PHP, for example, reads it as nested arrays: `$_POST['sessions'][0]['speakers'][1]['name']` is `"Linus"`.

`form.getValues()` gives you the same answers as data at any moment, kept up to date while the user types and adds
or removes rows:

```js
console.log(form.getValues());
```

{% code title="Result" %}
```json
{
  "name": "Static Sites Day",
  "category": "tech",
  "sessions": [
    {
      "title": "Forms from JSON",
      "speakers": [{ "name": "Ada" }, { "name": "Linus" }]
    }
  ]
}
```
{% endcode %}

Every field is plain data, so a whole form can also come from your database as JSON. The [form
builder](form-builder/form-builder.md) section shows it all.

## Make a static page dynamic

Your page stays server-rendered. Stratox takes over one element:

```html
<h1>Our products</h1>
<p>Everything here is plain HTML from your server.</p>

<div id="cart"></div>
```

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Cart', ({ props, view, html }) => html`
  <p>Items in your cart: ${props.items}</p>
  <button type="button" ${view.on('click', (data) => { data.items += 1; })}>Add to cart</button>
`);

const cart = new Stratox('#cart');
cart.view('Cart', { items: 0 });
cart.execute();
```

{% code title="Result" %}
```html
<div id="cart">
  <p>Items in your cart: 0</p>
  <button type="button" data-stratox-click="1">Add to cart</button>
</div>
```
{% endcode %}

Click the button and the cart renders again:

{% code title="Result after a click" %}
```html
<div id="cart">
  <p>Items in your cart: 1</p>
  <button type="button" data-stratox-click="2">Add to cart</button>
</div>
```
{% endcode %}

Change it from anywhere else in your script:

```js
cart.update('Cart', { items: 5 });
```

{% code title="Result" %}
```html
<div id="cart">
  <p>Items in your cart: 5</p>
  <button type="button" data-stratox-click="3">Add to cart</button>
</div>
```
{% endcode %}

Add as many of these islands to one page as you like.

## Where to go next

* [Installation](installation.md): npm with a bundler, or a CDN for a page without a build.
* [Quick start](template-engine/basic-example.md): your first component, step by step.
* [Upgrading from 3.x](upgrading.md): what changed in Stratox 4.
* Need routing? [Stratox Pilot](https://stratox.wazabii.se/stratox-router) is the router made for Stratox: it maps
  URLs to components and changes pages without a reload. About 4 kB more.
