---
description: >-
  Send a whole Stratox form from your server as JSON: fields, repeaters and components as plain data, saved
  answers as values, and the answers back as JSON.
---

# Forms from JSON

Every field is a plain object, so a whole form is data. Keep it in your database, send it to the page as JSON, and
change the form without changing the page.

## The definition

One object per field, by name, with the same keys as the second argument of `form()`:

{% code title="/api/forms/event.json" %}
```json
{
  "fields": {
    "name": { "type": "text", "label": "Event name" },
    "category": {
      "type": "select",
      "label": "Category",
      "items": { "tech": "Technology", "design": "Design" }
    },
    "sessions": {
      "type": "group",
      "label": "Sessions",
      "config": { "nestedNames": true, "controls": true },
      "fields": {
        "title": { "type": "text", "label": "Session title" },
        "length": { "type": "radio", "label": "Length", "items": { "30": "30 min", "60": "1 hour" } }
      }
    }
  }
}
```
{% endcode %}

And the saved answers, if there are any:

{% code title="/api/answers/42.json" %}
```json
{
  "name": "Static Sites Day",
  "category": "design",
  "sessions": [
    { "title": "Forms from JSON", "length": 60 },
    { "title": "Islands", "length": 30 }
  ]
}
```
{% endcode %}

## Render it

One `form()` call per field, then the values:

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({ handlers: { fields: StratoxTemplate } });

const definition = await fetch('/api/forms/event.json').then((response) => response.json());
const answers = await fetch('/api/answers/42.json').then((response) => response.json());

const form = new Stratox('#event-form');
Object.entries(definition.fields).forEach(([name, field]) => form.form(name, field));
form.setValues(answers);
form.execute();
```

{% code title="Result: what the form posts" %}
```
name = Static Sites Day
category = design
sessions[0][title] = Forms from JSON
sessions[0][length] = 60
sessions[1][title] = Islands
sessions[1][length] = 30
```
{% endcode %}

Every key of a field works in JSON: `type`, `label`, `description`, `attr`, `conAttr`, `items`, `value`, `config`,
`fields` (with groups inside groups to any depth), `data` and `name`.

## The answers back as JSON

The values object follows the user in a form with a repeater, so sending the answers back is one line. After the
user changes the first session's title to "Forms from data":

```js
const body = JSON.stringify(form.getValues());
console.log(body);
```

{% code title="Result after typing" %}
```
{"name":"Static Sites Day","category":"design","sessions":[{"title":"Forms from data","length":60},{"title":"Islands","length":30}]}
```
{% endcode %}

Values the user did not touch keep their type, such as the number `60`. Send `body` with
`fetch(url, { method: 'POST', body })`, or let the form post as usual.

## Fields as a list

If your backend stores fields as a list, give each one a `name` and loop over the list. A list also keeps its order,
which an object does not always do: JavaScript puts keys that look like numbers (`"1"`, `"2"`) first.

{% code title="/api/forms/contact.json" %}
```json
[
  { "name": "email", "type": "text", "label": "Email", "attr": { "type": "email" } },
  { "name": "topic", "type": "select", "label": "Topic", "items": { "sales": "Sales", "support": "Support" } }
]
```
{% endcode %}

```js
const fields = await fetch('/api/forms/contact.json').then((response) => response.json());

const contact = new Stratox('#contact-form');
fields.forEach((field) => contact.form(field.name, field));
contact.execute();
console.log([...document.querySelectorAll('#contact-form [name]')].map((input) => input.name));
```

{% code title="Result" %}
```json
["email", "topic"]
```
{% endcode %}

## Components in JSON

JSON cannot hold functions, so a definition names a component by the name it is registered under, as its `type`,
and gives its props in `data`:

```js
Stratox.setComponent('Notice', ({ props, html }) => html`<p class="notice">${props.text}</p>`);

const notices = new Stratox('#notices');
notices.form('intro', JSON.parse('{ "type": "Notice", "data": { "text": "All fields are optional." } }'));
notices.execute();
```

{% code title="Result" %}
```html
<div id="notices">
  <p class="notice">All fields are optional.</p>
</div>
```
{% endcode %}

A component that is not registered is loaded from the `directory` setting, by its name: `Notice` loads
`Notice.js`. Wait for it with `await form.ready()`. See [Components as fields](components-as-fields.md).

{% hint style="warning" %}
Labels, descriptions and option titles are inserted as HTML. Send them only from a source you trust, such as your
own backend. Values and attributes are escaped.
{% endhint %}
