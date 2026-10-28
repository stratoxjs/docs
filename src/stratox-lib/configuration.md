---
description: >-
  Every Stratox setting: the form handler, the helper, the components folder, caching, updates in place, form
  classes and icons, and settings of your own. How to set and read them, for all views or for one app.
---

# Configuration

Stratox works without settings. To change one, call `Stratox.setConfigs()` before your first view. It changes the
default app, which every view uses unless you give it [an app of its own](apps.md):

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({
  directory: '/components/',
  handlers: { fields: StratoxTemplate },
});

console.log(Stratox.getConfigs('directory'));
```

{% code title="Result" %}
```
/components/
```
{% endcode %}

`setConfigs()` merges: settings you leave out keep their value. `handlers`, `classes` and `icons` are merged one level
deeper, so `{ handlers: { fields } }` keeps the default `helper`.

## The settings

| Setting | Default | What it does |
| --- | --- | --- |
| `handlers.fields` | `null` | The form field class. `null` is `StratoxBuilder`, which has no field types; `StratoxTemplate` has the built-in ones; see [Custom form templates](form-builder/custom-form-template.md). |
| `handlers.helper` | a function | Given to every component as `helper`. A function is called with the form builder, and its result is given. |
| `directory` | `''` | The folder that components which are not registered are imported from. See [Components on demand](components-on-demand.md). |
| `cache` | `false` | With `false`, those imports get a query string, so the browser loads the files again on each page load. |
| `morph` | `true` | Updates change only what differs, so focus and typed text stay. With `false`, an update replaces the element's content. |
| `classes` | `{}` | Layout classes of the form builder's markup. See [Styling forms](form-builder/styling.md). |
| `icons` | `{}` | The icons of the repeater buttons: `add` and `delete`, as HTML. |
| `propagation` | `true` | Some renders are written to the page on a short timer, so that many renders in a row are written once. See below. |

## handlers.helper

The helper is for things every component needs, such as a formatter or your own small library:

```js
Stratox.setConfigs({
  handlers: {
    helper: () => ({ money: (amount) => `${amount.toFixed(2)} kr` }),
  },
});

Stratox.setComponent('Total', ({ props, helper, html }) => html`<p>Total: ${helper.money(props.sum)}</p>`);

const total = new Stratox('#total');
total.view('Total', { sum: 1290 });
total.execute();
```

{% code title="Result" %}
```html
<div id="total">
  <p>Total: 1290.00 kr</p>
</div>
```
{% endcode %}

For shared objects that you look up by name, [services](advanced-features/container.md) are often clearer.

## morph

With the default `morph: true`, an update compares the new HTML with the page and changes only what differs. Elements
that stay keep their state: focus, the cursor, typed text, scroll positions and listeners added to them.

Set `morph: false` to replace the whole content of the element on every update, as Stratox 3 did. Use it when other
code of yours expects new elements after each update.

## propagation

With the default `propagation: true`, a render that comes after one that was written at once waits for a short timer
(`setTimeout` with 0), and renders that pile up in the meantime are written to the page once. That keeps a loop of
updates from writing the page again and again. It also means that the page is not always up to date right after an
`update()`: here every second update shows a moment later.

```js
Stratox.setComponent('Step', ({ props, html }) => html`<p>Step ${props.step}</p>`);

const steps = new Stratox('#steps');
steps.view('Step', { step: 0 });
steps.execute();

const shown = [];
for (const step of [1, 2, 3, 4]) {
  steps.update('Step', { step });
  shown.push(document.querySelector('#steps p').textContent);
  await new Promise((resolve) => setTimeout(resolve, 20));
}
console.log(shown.join(', '));
```

{% code title="Result" %}
```
Step 1, Step 1, Step 3, Step 3
```
{% endcode %}

Code that reads the page right after an update should wait a moment first. With `propagation: false`, every render is
written at once, and the same loop shows `Step 1, Step 2, Step 3, Step 4`.

## Settings of your own

Any other key is kept as it is, so a setting of your own can live next to Stratox's. A component reads it from its
view:

```js
Stratox.setConfigs({ currency: 'SEK' });

Stratox.setComponent('Currency', ({ view, html }) => html`<p>Prices in ${view.getConfig('currency')}</p>`);

const currency = new Stratox('#currency');
currency.view('Currency');
currency.execute();
```

{% code title="Result" %}
```html
<div id="currency">
  <p>Prices in SEK</p>
</div>
```
{% endcode %}

## Reading settings

* `Stratox.getConfigs()` gives all settings of the default app; `Stratox.getConfigs(key)` one of them.
* `view.getConfig(key)` gives a setting of the view's app.
* `app.getConfigs()` and `app.getConfigs(key)` do the same for an app from `Stratox.createApp()`.
