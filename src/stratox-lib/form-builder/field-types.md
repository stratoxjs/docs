---
description: >-
  Every built-in field type of the Stratox form builder, what it renders, how its options and attributes work, and
  what it posts.
---

# Field types

Set a field's type with `setType()` or the `type` key. These are built in, from `StratoxTemplate`:

| Type | Renders |
| --- | --- |
| `text` | `<input type="text">`, the default. Any other input type through `attr.type`: `email`, `tel`, `number`, `url`, `color` and so on. |
| `password` | `<input type="password">` |
| `date` | `<input type="date">` |
| `datetime` | `<input type="datetime-local">` |
| `hidden` | `<input type="hidden">`, without label or container |
| `textarea` | `<textarea>` |
| `select` | `<select>` with an `<option>` per item |
| `radio` | one radio button per item |
| `checkbox` | one checkbox per item |
| `submit` | `<input type="submit">` |
| `group` | a [repeater](repeaters.md): rows of fields |

A registered component works as a type too; see [Components as fields](components-as-fields.md). To add your own
types, see [Custom form templates](custom-form-template.md).

## Text inputs

`text` takes its input type from `attr`, so one type covers every single-line input:

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({ handlers: { fields: StratoxTemplate } });

const account = new Stratox('#account');
account.form('email').setLabel('Email').setAttr({ type: 'email', autocomplete: 'email' });
account.form('age').setLabel('Age').setAttr({ type: 'number', min: 18 });
account.form('password', { type: 'password', label: 'Password' });
account.form('token', { type: 'hidden', value: 'a1b2c3' });
account.execute();
```

{% code title="Result" %}
```html
<div id="account">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-email w-full">
    <label>Email<div class="message hide"></div></label>
    <input type="email" name="email" value="" data-index="0" autocomplete="email" data-name="email">
  </div>
  <div id="wa-fi-view-1" data-index="1" class="mb-15 field-age w-full">
    <label>Age<div class="message hide"></div></label>
    <input type="number" name="age" value="" data-index="1" min="18" data-name="age">
  </div>
  <div id="wa-fi-view-2" data-index="2" class="mb-15 field-password w-full">
    <label>Password<div class="message hide"></div></label>
    <input type="password" name="password" value="" data-index="2" data-name="password">
  </div>
  <input type="hidden" name="token" value="a1b2c3" data-index="3" data-name="token">
</div>
```
{% endcode %}

## Attributes

`attr` sets any attribute of the input and replaces the defaults. `true` writes the attribute without a value,
`false`, `null` and `undefined` leave it out, so you can switch one on and off with a condition:

```js
const locked = true;

const settings = new Stratox('#settings');
settings.form('username', { label: 'Username', attr: { disabled: locked, readonly: false, placeholder: 'ada' } });
settings.execute();
```

{% code title="Result" %}
```html
<div id="settings">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-username w-full">
    <label>Username<div class="message hide"></div></label>
    <input type="text" name="username" value="" data-index="0" disabled placeholder="ada" data-name="username">
  </div>
</div>
```
{% endcode %}

Attribute values are escaped. To set the container's attributes instead, use `conAttr`, such as
`conAttr: { class: 'half' }`.

## Options: select, radio and checkbox

`items` lists the options as `{ value: title }`. The value is what the form posts; the title is what the user sees,
inserted as HTML. The field's value checks or selects the matching options:

```js
const pizza = new Stratox('#pizza');
pizza.form('size', {
  type: 'select',
  label: 'Size',
  items: { s: 'Small', m: 'Medium', l: 'Large' },
});
pizza.form('crust', {
  type: 'radio',
  label: 'Crust',
  items: { thin: 'Thin', thick: 'Thick' },
});
pizza.form('toppings', {
  type: 'checkbox',
  label: 'Toppings',
  items: { cheese: 'Cheese', basil: 'Basil', olives: 'Olives' },
});
pizza.setValues({ size: 'm', crust: 'thin', toppings: ['cheese', 'olives'] });
pizza.execute();
```

{% code title="Result" %}
```html
<div id="pizza">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-size w-full">
    <label>Size<div class="message hide"></div></label>
    <select name="size" data-index="0" data-name="size" autocomplete="off">
      <option value="s">Small</option>
      <option value="m" selected="selected">Medium</option>
      <option value="l">Large</option>
    </select>
  </div>
  <div id="wa-fi-view-1" data-index="1" class="mb-15 field-crust w-full">
    <label>Crust<div class="message hide"></div></label>
    <label class="radio items small">
      <input type="radio" name="crust" data-index="1" data-name="crust" value="thin" checked="checked">
      <span class="title">Thin</span>
    </label>
    <label class="radio items small">
      <input type="radio" name="crust" data-index="1" data-name="crust" value="thick">
      <span class="title">Thick</span>
    </label>
  </div>
  <div id="wa-fi-view-2" data-index="2" class="mb-15 field-toppings w-full">
    <label>Toppings<div class="message hide"></div></label>
    <label class="checkbox items small">
      <input type="checkbox" name="toppings[]" data-index="2" data-name="toppings" value="cheese" checked="checked">
      <span class="title">Cheese</span>
    </label>
    <label class="checkbox items small">
      <input type="checkbox" name="toppings[]" data-index="2" data-name="toppings" value="basil">
      <span class="title">Basil</span>
    </label>
    <label class="checkbox items small">
      <input type="checkbox" name="toppings[]" data-index="2" data-name="toppings" value="olives" checked="checked">
      <span class="title">Olives</span>
    </label>
  </div>
</div>
```
{% endcode %}

* A `checkbox` with more than one item is named `toppings[]` and takes an array of values. With one item it is a
  single checkbox named `toppings`.
* A `select` with `attr: { multiple: true }` is named `size[]` and takes an array too.
* Values are compared as text, so `1` selects the item `"1"`.
* Object keys that look like numbers (`"1"`, `"2"`) come first in JavaScript, whatever order you wrote them in.
  Use other keys when the order matters.

What this form posts:

{% code title="Result: what the form posts" %}
```
size = m
crust = thin
toppings[] = cheese
toppings[] = olives
```
{% endcode %}

## Textarea

```js
const feedback = new Stratox('#feedback');
feedback.form('comment', { type: 'textarea', label: 'Comment', attr: { rows: 4 } });
feedback.setValues({ comment: 'Great <b>service</b>!' });
feedback.execute();
```

{% code title="Result" %}
```html
<div id="feedback">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-comment w-full">
    <label>Comment<div class="message hide"></div></label>
    <textarea name="comment" data-index="0" rows="4" data-name="comment">Great &lt;b&gt;service&lt;/b&gt;!</textarea>
  </div>
</div>
```
{% endcode %}

The value is escaped, so it stays text.

## Description and required mark

`description` adds a text under the label. A field whose `validate` has a minimum length gets a `*` after its label:

```js
const shipping = new Stratox('#shipping');
shipping.form('address', {
  label: 'Address',
  description: 'Street and number',
  validate: { length: [1, 120] },
});
shipping.execute();
```

{% code title="Result" %}
```html
<div id="shipping">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-address w-full">
    <label>Address*<div class="message hide"></div></label>
    <div class="description legend">Street and number</div>
    <input type="text" name="address" value="" data-index="0" data-name="address">
  </div>
</div>
```
{% endcode %}

The `*` is all `validate` does in the form builder: check the values on your server, or with the browser's own
attributes such as `required`.

## Submit

```js
const done = new Stratox('#done');
done.form('save', { type: 'submit', value: 'Save' });
done.execute();
```

{% code title="Result" %}
```html
<div id="done">
  <div class="submit grow flex justify-end">
    <input type="submit" class="button bg-primary" name="save" value="Save" data-name="save">
  </div>
</div>
```
{% endcode %}
