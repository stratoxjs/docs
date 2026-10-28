---
description: >-
  Build forms with the Stratox form builder: fields from a few lines of JavaScript, values in and out with
  setValues() and getValues(), and inputs named so your backend gets clean data.
---

# Form builder

The form builder renders form fields from plain descriptions. You say which fields you want; Stratox writes the
labels and inputs, fills in saved values, keeps the values up to date while the user types, and names every input so
the form posts the data your backend expects. Repeating rows are one setting away; see [Repeaters](repeaters.md).

## Turn it on

The built-in field types come from the `StratoxTemplate` class. Set it once, before your first form:

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({ handlers: { fields: StratoxTemplate } });
```

## Your first form

Stratox renders fields, not the `<form>` tag, so the form stays yours: its `action`, its `method`, its submit
button. Give it an element for the fields:

```html
<form method="post" action="/contact">
  <div id="contact"></div>
  <button type="submit">Send</button>
</form>
```

Add a field with `form(name)`, and describe it with setters:

```js
const contact = new Stratox('#contact');

contact.form('name').setLabel('Your name');
contact.form('email').setLabel('Email').setAttr({ type: 'email', required: true });
contact.form('message').setType('textarea').setLabel('Message');

contact.execute();
```

{% code title="Result" %}
```html
<div id="contact">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-name w-full">
    <label>Your name<div class="message hide"></div></label>
    <input type="text" name="name" value="" data-index="0" data-name="name">
  </div>
  <div id="wa-fi-view-1" data-index="1" class="mb-15 field-email w-full">
    <label>Email<div class="message hide"></div></label>
    <input type="email" name="email" value="" data-index="1" required data-name="email">
  </div>
  <div id="wa-fi-view-2" data-index="2" class="mb-15 field-message w-full">
    <label>Message<div class="message hide"></div></label>
    <textarea name="message" data-index="2" data-name="message"></textarea>
  </div>
</div>
```
{% endcode %}

The name you give `form()` is the input's `name`. A field without `setType()` is a text input. Each field sits in a
`<div>` with its label; the classes are for layout and can be changed, see [Styling forms](styling.md).

## Setters, or one object

Each setter returns the field, so they chain. You can also give everything at once as the second argument of
`form()`. These two fields are the same:

```js
const order = new Stratox('#order');

order.form('size')
  .setType('select')
  .setLabel('Size')
  .setItems({ s: 'Small', m: 'Medium', l: 'Large' })
  .setValue('m');

order.form('size-again', {
  type: 'select',
  label: 'Size',
  items: { s: 'Small', m: 'Medium', l: 'Large' },
  value: 'm',
});

order.execute();
```

| Setter | Key | What it sets |
| --- | --- | --- |
| `setType(type)` | `type` | The field type, see [Field types](field-types.md), or a component. Default `text`. |
| `setLabel(text)` | `label` | The label. Inserted as HTML. |
| `setDescription(text)` | `description` | A text under the label. Inserted as HTML. |
| `setAttr(object)` | `attr` | Attributes of the input. `true` writes a bare attribute, `false` leaves it out. |
| `setItems(object)` | `items` | The options of `select`, `radio` and `checkbox`: `{ value: title }`. |
| `setValue(value)` | `value` | The value, unless `setValues()` gives one. |
| `setConfig(object)` | `config` | Settings of the field type, such as a repeater's `nestedNames` and `controls`. |
| `setFields(object)` | `fields` | The fields of a repeater. |
| `setData(object)` | `data` | The props of a component used as a field. |
| `setName(name)` | `name` | A name for the input other than the one given to `form()`. |

Container attributes go in `conAttr`, in the object form only: `{ conAttr: { class: 'half' } }`.

## Values in: setValues()

Saved answers usually come from your server as one object. `setValues()` fills every field from it by name. It wins
over `setValue()`:

```js
const profile = new Stratox('#profile');

profile.form('name').setLabel('Name');
profile.form('newsletter', { type: 'checkbox', items: { yes: 'Send me the newsletter' } });

profile.setValues({ name: 'Ada Lovelace', newsletter: 'yes' });
profile.execute();
```

{% code title="Result" %}
```html
<div id="profile">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-name w-full">
    <label>Name<div class="message hide"></div></label>
    <input type="text" name="name" value="Ada Lovelace" data-index="0" data-name="name">
  </div>
  <div id="wa-fi-view-1" data-index="1" class="mb-15 field-newsletter w-full">
    <label class="checkbox items small">
      <input type="checkbox" name="newsletter" data-index="1" data-name="newsletter" value="yes" checked="checked">
      <span class="title">Send me the newsletter</span>
    </label>
  </div>
</div>
```
{% endcode %}

Numbers work too: `1` checks the option `"1"`. `null` gives an empty field.

## Values out: getValues()

`getValues()` returns the values object, the same object you gave `setValues()`:

```js
console.log(profile.getValues());
```

{% code title="Result" %}
```json
{ "name": "Ada Lovelace", "newsletter": "yes" }
```
{% endcode %}

In a form with a [repeater](repeaters.md), Stratox writes every change into this object while the user types, checks
and adds or removes rows, so `getValues()` always gives the current answers, ready for
`JSON.stringify()`. In a form without a repeater, it keeps the values you set; read what the user typed from the
form itself, for example with `new FormData(form)`.

To show new values after `execute()`, call `setValues()` again and render with `update()`:

```js
profile.setValues({ name: 'Grace Hopper', newsletter: 0 });
profile.update();
console.log(document.querySelector('#profile input[name="name"]').value);
```

{% code title="Result" %}
```
Grace Hopper
```
{% endcode %}

## What the form posts

Every input is named, so the form posts like any hand-written form. For the contact form above:

{% code title="Result: what the form posts" %}
```
name =
email =
message =
```
{% endcode %}

## Fields and views together

One instance can hold views and fields. They render in the order you add them, so a heading component can sit above
the fields:

```js
Stratox.setComponent('Intro', ({ props, html }) => html`<p class="intro">${props.text}</p>`);

const signup = new Stratox('#signup');
signup.view('Intro', { text: 'It takes a minute.' });
signup.form('email').setLabel('Email');
signup.execute();
```

{% code title="Result" %}
```html
<div id="signup">
  <p class="intro">It takes a minute.</p>
  <div id="wa-fi-view-1" data-index="1" class="mb-15 field-email w-full">
    <label>Email<div class="message hide"></div></label>
    <input type="text" name="email" value="" data-index="1" data-name="email">
  </div>
</div>
```
{% endcode %}

## Next

* [Field types](field-types.md): every built-in field.
* [Repeaters](repeaters.md): rows the user can add and remove, to any depth.
* [Forms from JSON](forms-from-json.md): a whole form from your server.
* [Styling forms](styling.md): the markup's classes, and how to change them.
