---
description: >-
  Load Stratox components from their own files the first time a page uses them, with the directory setting:
  views and form fields by name, waiting with ready(), and the cache setting.
---

# Components on demand

A component does not have to be registered before you use it. Set a `directory`, and a view or a form field whose
component is not registered is imported from that folder the first time it is used: `Card` loads `Card.js`. Each
page then loads only the components it shows.

## A component in its own file

The file exports one component function. Default or named, the export is what Stratox uses:

{% code title="/components/Card.js" %}
```js
export default function Card({ props, html }) {
  return html`
    <article class="card">
      <h2>${props.title}</h2>
      <p>${props.text}</p>
    </article>
  `;
}
```
{% endcode %}

Point `directory` at the folder, and show the view by name:

```js
import { Stratox } from 'stratox';

Stratox.setConfigs({ directory: '/components/' });

const news = new Stratox('#news');
news.view('Card', { title: 'New opening hours', text: 'From Monday we open at 8.' });
news.execute();
await news.ready();
```

{% code title="Result" %}
```html
<div id="news">
  <article class="card">
    <h2>New opening hours</h2>
    <p>From Monday we open at 8.</p>
  </article>
</div>
```
{% endcode %}

* The name before a `#` is the file: `Card#first` and `Card#second` both load `Card.js`, once.
* Write `directory` as a path from the root of your site, such as `/components/`, or as a full URL. A relative path
  is resolved from the Stratox file, which moves when a bundler builds your code.
* A file should export only the component. Every export is registered under the view's name, so with several, the
  last one wins.

## Waiting for it

An import takes a moment. `execute()` renders as soon as every component is there, and until then it returns `''`.
`ready()` returns a promise that settles when the view has rendered:

```js
const offers = new Stratox('#offers');
offers.view('Card#offer', { title: 'Two for one', text: 'All week.' });
console.log(JSON.stringify(offers.execute()));
await offers.ready();
console.log(offers.getResponse().includes('Two for one'));
```

{% code title="Result" %}
```
""
true
```
{% endcode %}

You only need `ready()` when your code works with the rendered view right away. The page updates by itself.

## When a file is missing

A view whose component cannot be loaded logs the error with `console.error`, and `ready()` rejects with it:

```js
const missing = new Stratox('#missing');
missing.view('Banner', { text: 'Sale' });
missing.execute();

try {
  await missing.ready();
} catch (error) {
  console.log('Banner could not be loaded');
}
```

{% code title="Result" %}
```
Banner could not be loaded
```
{% endcode %}

## Form fields from files

A form field whose `type` is not a field type and not a registered component is imported the same way, also in
repeaters at any depth. The form renders once every import is done. A type that cannot be loaded leaves out only that
field and logs why:

{% code title="/components/Stars.js" %}
```js
export default function Stars({ props, context, html }) {
  return html`<p>${props.label}: <input type="number" name="${context.name}" value="${context.value}" min="1" max="5"></p>`;
}
```
{% endcode %}

```js
import { StratoxTemplate } from 'stratox';

const review = new Stratox('#review', Stratox.createApp({
  directory: '/components/',
  handlers: { fields: StratoxTemplate },
}));
review.form('score', { type: 'Stars', data: { label: 'Your rating' } });
review.setValues({ score: 4 });
review.execute();
await review.ready();
```

{% code title="Result" %}
```html
<div id="review">
  <p>Your rating: <input type="number" name="score" value="4" min="1" max="5"></p>
</div>
```
{% endcode %}

## The cache setting

With the default `cache: false`, Stratox adds a query string with the time of the page load to every import, such as
`Card.js?v=1730106000000`. The browser then loads the files again on every page load, so a change to a component
shows at once. Within one page load each file is loaded once.

Set `cache: true` when your files are versioned or your server sends cache headers, and the browser may keep them:

```js
Stratox.setConfigs({ directory: '/components/', cache: true });
```

## With a bundler

A bundler does not see these imports, so it leaves the component files out of your build. Put them in a folder your
site serves as it is, such as Vite's `public/` folder. Components you import in your code are bundled as usual; use
the `directory` only for the ones you want to load on demand.
