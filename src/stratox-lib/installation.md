---
description: >-
  Install Stratox with npm and use it with your bundler, or load it from a CDN on a page without a build.
---

# Installation

Stratox is a set of ES modules with no dependencies. It runs in current Chrome, Edge, Firefox and Safari.

## With npm and a bundler

Install the package:

```bash
npm install stratox
```

Import what you need from `stratox` in your code. Your page needs an element to show the view in, here
`<div id="app"></div>`:

{% code title="src/main.js" %}
```js
import { Stratox } from 'stratox';

Stratox.setComponent('Hello', ({ html }) => html`<p>Stratox is running.</p>`);

const app = new Stratox('#app');
app.view('Hello');
app.execute();
```
{% endcode %}

{% code title="Result" %}
```html
<div id="app">
  <p>Stratox is running.</p>
</div>
```
{% endcode %}

Any bundler that reads the `exports` of a package works, for example [Vite](https://vite.dev). To start a new site
with Vite:

```bash
npm create vite@latest my-site -- --template vanilla
cd my-site
npm install stratox
```

{% hint style="info" %}
Always import from `stratox`. The package lets you import only its entry, so a path inside it, such as
`stratox/dist/Stratox.js`, fails. Everything you need is exported from the entry: `Stratox`, `StratoxApp`,
`StratoxBuilder`, `StratoxTemplate`, `StratoxContainer`, `StratoxObserver`, `html`, `raw`, `escape` and `SafeHtml`.
{% endhint %}

## From a CDN, without a build

On a page without a build step, import Stratox from a CDN inside a module script:

```html
<div id="app"></div>

<script type="module">
  import { Stratox } from 'https://cdn.jsdelivr.net/npm/stratox@4/dist/index.js';

  Stratox.setComponent('Hello', ({ html }) => html`<p>Stratox is running.</p>`);

  const app = new Stratox('#app');
  app.view('Hello');
  app.execute();
</script>
```

{% code title="Result" %}
```html
<div id="app">
  <p>Stratox is running.</p>
</div>
```
{% endcode %}

`stratox@4` loads the newest 4.x release. Write the full version, such as `stratox@4.0.0`, to load the same files
every time.

You can also host the files yourself: copy the `dist` folder of the package to your site and import its `index.js`.

## TypeScript

Stratox is written in TypeScript and ships its types, so there is nothing more to install. Type a component by its
props:

{% code title="src/hello.ts" %}
```ts
import { Stratox } from 'stratox';
import type { Component } from 'stratox';

const Hello: Component<{ name: string }> = ({ props, html }) => html`<p>Hello ${props.name}</p>`;

Stratox.setComponent('Hello', Hello);

const app = new Stratox('#app');
app.view('Hello', { name: 'Ada' });
app.execute();
```
{% endcode %}

{% code title="Result" %}
```html
<div id="app">
  <p>Hello Ada</p>
</div>
```
{% endcode %}

The types work with the `moduleResolution` settings `bundler` and `nodenext`.

## Settings

Stratox works without any settings. To change one, call `Stratox.setConfigs()` once, before your first view. This
turns on the built-in form fields:

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({
  handlers: { fields: StratoxTemplate },
});
```

Every setting, with its default and examples, is on the [Configuration](configuration.md) page.
