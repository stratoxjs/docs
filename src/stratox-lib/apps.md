---
description: >-
  Keep parts of one page apart with Stratox apps: each app has its own settings, components and services, so two
  islands on a page, or two scripts, cannot affect each other.
---

# Apps on one page

Settings, components and services belong to an app. Every view uses the default app unless you give it another one,
and the static API (`Stratox.setConfigs()`, `Stratox.setComponent()`, `Stratox.getConfigs()`) works on the default
app. For one script on a page, that is all you need.

When a page has parts that should not share anything, such as a shop and a newsletter form written by different
teams, give each part an app of its own.

## Make an app

`Stratox.createApp(settings)` makes an app. Register its components on it, and pass it as the second argument of
`new Stratox()`:

```js
import { Stratox } from 'stratox';

const shop = Stratox.createApp({ directory: '/shop/components/' });
shop.setComponent('Item', ({ props, html }) => html`<p>${props.name}: ${props.price} kr</p>`);

const blog = Stratox.createApp();
blog.setComponent('Item', ({ props, html }) => html`<p><a href="${props.url}">${props.name}</a></p>`);

const cart = new Stratox('#cart', shop);
cart.view('Item', { name: 'Desk lamp', price: 349 });
cart.execute();

const posts = new Stratox('#posts', blog);
posts.view('Item', { name: 'Opening hours', url: '/news/opening-hours' });
posts.execute();
```

{% code title="Result" %}
```html
<div id="cart">
  <p>Desk lamp: 349 kr</p>
</div>
<div id="posts">
  <p><a href="/news/opening-hours">Opening hours</a></p>
</div>
```
{% endcode %}

Both apps have a component named `Item`, and each view renders its own app's. A component registered on an app is
not known to the default app or to other apps.

## What an app holds

| | Default app | Your app |
| --- | --- | --- |
| Settings | `Stratox.setConfigs()`, `Stratox.getConfigs()` | `app.setConfigs()`, `app.getConfigs()`, or `Stratox.createApp(settings)` |
| Components | `Stratox.setComponent()` | `app.setComponent()`, `app.getComponent()` |
| Services | `Stratox.app.container()` | `app.container()` |

`Stratox.app` is the default app itself. A new app starts with the default settings, not with the default app's
current ones.

## From a view

A view knows its app:

* `view.getApp()` returns it.
* `view.getConfig(key)` reads one of its settings.
* `view.getComponent(name)` returns one of its components, or `null`.
* `view.withComponent(name, component)` registers a component in it.
* `view.container()` returns its services.

```js
console.log(cart.getApp() === shop, cart.getConfig('directory'), posts.getComponent('Missing'));
```

{% code title="Result" %}
```
true /shop/components/ null
```
{% endcode %}

Partials and blocks use the app of the view they are in.
