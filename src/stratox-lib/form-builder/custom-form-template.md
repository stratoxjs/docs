---
description: >-
  Add field types to the Stratox form builder, or change the built-in ones, with a class that extends
  StratoxTemplate. The helpers and properties a field method can use.
---

# Custom form templates

Each field type is a method of the form handler class. `StratoxTemplate` has the built-in ones. Extend it, and a new
method is a new field type; a method with a built-in name replaces that type.

## Add a field type

```js
import { Stratox, StratoxTemplate } from 'stratox';

class MyFields extends StratoxTemplate {
  rating(helper) {
    return this.container(() => this.input(helper, { type: 'range', min: 1, max: 5 }));
  }
}

Stratox.setConfigs({ handlers: { fields: MyFields } });

const review = new Stratox('#review');
review.form('score', { type: 'rating', label: 'Your rating', value: 3 });
review.execute();
```

{% code title="Result" %}
```html
<div id="review">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-score w-full">
    <label>Your rating<div class="message hide"></div></label>
    <input type="range" name="score" value="3" data-index="0" min="1" max="5" data-name="score">
  </div>
</div>
```
{% endcode %}

* `this` in a field method is the form builder, holding the field being rendered.
* `container(callback)` writes the field's `<div>`, label and description around what the callback returns.
* `input(helper, attributes)` writes an `<input>` with the field's name, value and attributes; yours are added on
  top.
* A field method returns an HTML string. Escape every value you write yourself with `this.escapeHtml()`, or use
  `getAttr()`, which escapes.

## Change a built-in type

Override the method. This submit field renders a `<button>` instead of an `<input>`:

```js
class ButtonFields extends StratoxTemplate {
  submit() {
    return `<div class="actions"><button type="submit"${this.getAttr({ name: this.name })}>${this.escapeHtml(this.value)}</button></div>`;
  }
}

const app = Stratox.createApp({ handlers: { fields: ButtonFields } });
const actions = new Stratox('#actions', app);
actions.form('save', { type: 'submit', value: 'Save changes' });
actions.execute();
```

{% code title="Result" %}
```html
<div id="actions">
  <div class="actions">
    <button type="submit" name="save" data-name="save">Save changes</button>
  </div>
</div>
```
{% endcode %}

The example uses an app of its own, so its field types apply to its own forms only; see the `handlers` setting on
[Installation](../installation.md).

## What a field method can use

| Property | What it is |
| --- | --- |
| `this.name` | The input's name, with the row path in a repeater: `members[0][name]`. |
| `this.nameJoin` | The same path with commas, `members,0,name`. Written to `data-name`, which lets Stratox track the value. |
| `this.value` | The value, from `setValues()` or the field's own `value`. Text. |
| `this.label`, `this.description` | The texts. HTML. |
| `this.attr` | The field's `attr`. |
| `this.conAttr` | The container's attributes. |
| `this.config` | The field's `config`: free for your own settings. |
| `this.data` | The whole definition, also `this.data.items`. |
| `this.index` | The field's position in the form. |
| `this.view` | The Stratox instance. |

| Method | What it does |
| --- | --- |
| `container(callback)` | The field's container with label and description around the callback's markup. |
| `input(helper, attributes)` | An `<input>` with name, value, `data-name` and the field's `attr`. |
| `getAttr(defaults)` | The defaults merged with the field's `attr` (which wins), as an attribute string, escaped. |
| `getAttrStr(object)` | Any object as an attribute string, escaped. `true` writes a bare attribute, `false` leaves it out. |
| `escapeHtml(value)` | The value escaped for text or an attribute. |
| `isChecked(value)` | Whether an option is the value, or in it when the value is an array. Compared as text. |
| `getFieldID()` | An id for the field, the one `container()` uses. |
| `getClass(key)`, `classList(...classes)` | The layout classes from the `classes` setting; see [Styling forms](styling.md). |

The `helper` argument is what the `handlers.helper` setting gives, the same as a component's `helper`.

{% hint style="info" %}
One builder renders every field of a form, one after the other. Read `this` while the method runs; do not keep it
for later.
{% endhint %}

## Start from nothing

Extend `StratoxBuilder` instead to have no built-in types at all, only yours. A form handler must be
`StratoxBuilder` or a class that extends it:

```js
import { StratoxBuilder } from 'stratox';

class OnlyText extends StratoxBuilder {
  text() {
    return `<p><input type="text"${this.getAttr({ name: this.name, value: this.value })}></p>`;
  }
}

const minimal = new Stratox('#minimal', Stratox.createApp({ handlers: { fields: OnlyText } }));
minimal.form('city').setValue('Malmö');
minimal.execute();
```

{% code title="Result" %}
```html
<div id="minimal">
  <p><input type="text" name="city" value="Malmö" data-name="city"></p>
</div>
```
{% endcode %}

## TypeScript

The class and its methods are typed, so `this` in a field method knows the builder:

```ts
import { Stratox, StratoxTemplate } from 'stratox';

class TypedFields extends StratoxTemplate {
  rating(helper: unknown): string {
    return this.container(() => this.input(helper, { type: 'range', min: 1, max: 5 }));
  }
}

Stratox.setConfigs({ handlers: { fields: TypedFields } });
```
