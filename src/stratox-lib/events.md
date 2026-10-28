---
description: >-
  Handle clicks, typing and other events in Stratox components with view.on(), without inline JavaScript; the older
  view.bind(); and bindEvent() for elements you select yourself.
---

# Events

## view.on()

`view.on(eventName, callback)` returns an attribute. Write it inside the tag that should react. When the event
happens, the callback gets the view's data, changes it, and the view renders again:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Likes', ({ props, view, html }) => html`
  <button type="button" ${view.on('click', (data) => { data.likes += 1; })}>
    Like (${props.likes})
  </button>
`);

const likes = new Stratox('#likes');
likes.view('Likes', { likes: 0 });
likes.execute();
```

{% code title="Result after two clicks" %}
```html
<div id="likes">
  <button type="button" data-stratox-click="3">Like (2)</button>
</div>
```
{% endcode %}

* The callback gets `(props, view, item, event)`, and `this` is the view.
* Any event works whose name is in lowercase letters: `click`, `input`, `change`, `submit`, `keydown`, `focus` and
  so on.
* The default action is not prevented. Call `event.preventDefault()` in the callback when you need to, for example
  for `submit`.
* Pass `false` as the third argument to change data without rendering: `view.on('click', callback, false)`.
* There is no inline JavaScript in the markup, so `on()` works on sites with a strict Content Security Policy.
  Stratox listens on the document and finds the handler through the `data-stratox-...` attribute.

## Typing

Updates change the page in place, so a field keeps its focus and its text while the view around it renders. This
filters a list while you type:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Search', ({ props, view, html }) => {
  const found = props.cities.filter((city) => city.toLowerCase().includes(props.query.toLowerCase()));
  return html`
    <input type="search" value="${props.query}" ${view.on('input', (data, view, item, event) => {
      data.query = event.target.value;
    })}>
    <ul>${found.map((city) => html`<li>${city}</li>`)}</ul>
  `;
});

const search = new Stratox('#search');
search.view('Search', { query: '', cities: ['Malmö', 'Stockholm', 'Göteborg', 'Umeå'] });
search.execute();
```

After typing `m` in the field:

{% code title="Result after typing" %}
```html
<div id="search">
  <input type="search" value="m" data-stratox-input="2">
  <ul>
    <li>Malmö</li>
    <li>Stockholm</li>
    <li>Umeå</li>
  </ul>
</div>
```
{% endcode %}

## Several views in one instance

{% hint style="warning" %}
The callback of `on()` and `bind()` runs once for **every** view of the instance, each time with that view's data
and item. With one view per instance it runs once.
{% endhint %}

The simplest way to keep events apart is one view per instance, each with its own element. When views share an
instance, change only the data that is the component's own `props`:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Counter', ({ props, view, html }) => html`
  <p>${props.label}: ${props.count}</p>
  <button type="button" ${view.on('click', (data) => {
    if (data === props) {
      data.count += 1;
    }
  })}>Add one</button>
`);

const counters = new Stratox('#counters');
counters.view('Counter#apples', { label: 'Apples', count: 0 });
counters.view('Counter#pears', { label: 'Pears', count: 0 });
counters.execute();
```

{% code title="Result after a click on the first button" %}
```html
<div id="counters">
  <p>Apples: 1</p>
  <button type="button" data-stratox-click="3">Add one</button>
  <p>Pears: 0</p>
  <button type="button" data-stratox-click="4">Add one</button>
</div>
```
{% endcode %}

## view.bind()

`view.bind(callback)` is the older way. It returns JavaScript for an inline attribute such as `onclick`, and it
calls `event.preventDefault()` for you. The callback is the same as for `on()`:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('More', ({ props, view, html }) => html`
  <p>${props.text}</p>
  <a href="/news" onclick="${view.bind((data) => { data.text = 'Loaded more news.'; })}">More news</a>
`);

const more = new Stratox('#more');
more.view('More', { text: 'Latest news.' });
more.execute();
```

{% code title="Result after a click" %}
```html
<div id="more">
  <p>Loaded more news.</p>
  <a href="/news" onclick="stratoxHandler(event, '2')">More news</a>
</div>
```
{% endcode %}

Inline handlers need a Content Security Policy that allows inline script. Prefer `on()` in new code.

## bindEvent() and off()

`view.bindEvent()` adds a listener to elements you choose: a selector or a list of elements. With a selector as the
third argument, the listener reacts to events inside the elements that match it, also elements a later render adds.
The callback gets the event and the matching element, which is also `this`:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Tabs', ({ props, html }) => html`
  ${props.tabs.map((tab) => html`<button type="button" data-tab="${tab}">${tab}</button>`)}
  <p>Showing: ${props.active}</p>
`);

const tabs = new Stratox('#tabs');
tabs.view('Tabs', { tabs: ['Info', 'Reviews'], active: 'Info' });
tabs.execute();

tabs.bindEvent('#tabs', 'click', 'button[data-tab]', (event, button) => {
  tabs.update('Tabs', { active: button.dataset.tab });
});
```

{% code title="Result after a click on Reviews" %}
```html
<div id="tabs">
  <button type="button" data-tab="Info">Info</button>
  <button type="button" data-tab="Reviews">Reviews</button>
  <p>Showing: Reviews</p>
</div>
```
{% endcode %}

`bindEvent()` gives each element an `off()` method that removes every listener `bindEvent()` added to it:

```js
document.getElementById('tabs').off();
```
