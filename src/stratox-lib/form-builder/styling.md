---
description: >-
  Style the Stratox form builder: the classes and icons its markup uses, how to replace them with your own CSS
  framework's classes, and a plain stylesheet to start from.
---

# Styling forms

Stratox ships no CSS. The form builder writes plain markup with class names, and your stylesheet decides how it
looks. Every layout class and both repeater icons are settings, so the markup can use the classes your CSS already
has.

## A stylesheet to start from

The default classes are short utility names, such as `mb-15` and `w-full`. The Stratox repository has a small
stylesheet for exactly these classes, repeater buttons included:
[form-builder.css](https://github.com/stratoxjs/stratox/blob/main/examples/assets/form-builder.css). Copy it to your
site and change what you like.

## Use your own classes

The `classes` setting replaces the default class of each part of the markup. This uses Bootstrap's form classes,
for one app:

```js
import { Stratox, StratoxTemplate } from 'stratox';

const app = Stratox.createApp({
  handlers: { fields: StratoxTemplate },
  classes: {
    field: 'mb-3',
    fieldWidth: '',
    message: 'invalid-feedback',
    description: 'form-text',
    radio: 'form-check',
  },
});

const delivery = new Stratox('#delivery', app);
delivery.form('address', { label: 'Address', description: 'Street and number', attr: { class: 'form-control' } });
delivery.form('speed', { type: 'radio', label: 'Speed', items: { normal: 'Normal', express: 'Express' } });
delivery.execute();
```

{% code title="Result" %}
```html
<div id="delivery">
  <div id="wa-fi-view-0" data-index="0" class="mb-3 field-address">
    <label>Address<div class="invalid-feedback"></div></label>
    <div class="form-text">Street and number</div>
    <input type="text" name="address" value="" data-index="0" class="form-control" data-name="address">
  </div>
  <div id="wa-fi-view-1" data-index="1" class="mb-3 field-speed">
    <label>Speed<div class="invalid-feedback"></div></label>
    <label class="form-check">
      <input type="radio" name="speed" data-index="1" data-name="speed" value="normal">
      <span class="title">Normal</span>
    </label>
    <label class="form-check">
      <input type="radio" name="speed" data-index="1" data-name="speed" value="express">
      <span class="title">Express</span>
    </label>
  </div>
</div>
```
{% endcode %}

`classes` is merged with the defaults, so you set only the keys you change. An empty string removes a class.

| Key | Default | Where |
| --- | --- | --- |
| `field` | `mb-15` | every field's container |
| `fieldWidth` | `w-full` | every field's container, unless `conAttr.class` is set |
| `message` | `message hide` | the empty message element in each label, for your validation messages |
| `description` | `description legend` | the description under the label |
| `radio` | `radio items small` | the label around each radio button |
| `checkbox` | `checkbox items small` | the label around each checkbox |
| `itemTitle` | `title` | the text of each radio button and checkbox |
| `submit` | `submit grow flex justify-end` | the container of a `submit` field |
| `group` | `mb-20` | the wrapper of a repeater's rows |
| `groupRow` | `group relative card-3 mb-15 rounded border border-primary` | each row of a repeater |
| `addBefore` | `inline-block pad top-0 left-1/2 -translate-x-2/4 -translate-y-2/4 absolute z-10` | the button that adds a row before |
| `addAfter` | `inline-block pad bottom-0 left-1/2 -translate-x-2/4 translate-y-2/4 absolute z-10` | the button that adds a row after |
| `delete` | `inline-block pad right-0 top-0 absolute z-10` | the button that removes a row |

The `submit` field's input has the classes `button bg-primary`; change it with `attr: { class: '...' }`, or replace
the type in a [custom form template](custom-form-template.md).

## One field's container

`conAttr` sets attributes of one field's container. Its `class` replaces the width class, so two fields can share a
row:

```js
const names = new Stratox('#names', app);
names.form('first', { label: 'First name', conAttr: { class: 'col-6' } });
names.form('last', { label: 'Last name', conAttr: { class: 'col-6' } });
names.execute();
console.log([...document.querySelectorAll('#names > div')].map((field) => field.className));
```

{% code title="Result" %}
```json
["mb-3 field-first col-6", "mb-3 field-last col-6"]
```
{% endcode %}

`mb-3` comes from the `classes` of the app above; `col-6` replaces its empty `fieldWidth`.

## Icons

The repeater's buttons show an SVG plus and cross. Replace them with the `icons` setting, as HTML:

```js
const icons = Stratox.createApp({
  handlers: { fields: StratoxTemplate },
  icons: { add: '<span aria-hidden="true">+</span>', delete: '<span aria-hidden="true">×</span>' },
});

const tasks = new Stratox('#tasks', icons);
tasks.form('tasks', { type: 'group' })
  .setConfig({ nestedNames: true, controls: true })
  .setFields({ task: { type: 'text', label: 'Task' } });
tasks.setValues({ tasks: [{ task: 'Write docs' }, { task: 'Test them' }] });
tasks.execute();
console.log([...document.querySelectorAll('#tasks .form-group-icon')].map((button) => button.innerHTML));
```

{% code title="Result" %}
```json
[
  "<span aria-hidden=\"true\">×</span>",
  "<span aria-hidden=\"true\">+</span>",
  "<span aria-hidden=\"true\">+</span>",
  "<span aria-hidden=\"true\">×</span>",
  "<span aria-hidden=\"true\">+</span>",
  "<span aria-hidden=\"true\">+</span>"
]
```

Each row has its remove button first, then the buttons that add a row before and after it.
{% endcode %}

The icons are inserted as HTML, so they come from your code, never from users.

## Classes that always stay

Some class names are not settings, because the form builder's own code or your CSS selects them:

| Class | On |
| --- | --- |
| `field-<name>` | every field's container, such as `field-email`. In a repeater the name has commas, such as `field-members,0,name`; select those with `[data-name]` or escape the commas in CSS. |
| `wa-advanced-grouped-field` | the wrapper of a repeater's rows |
| `wa-field-group-btn` | both add buttons; with `before` or `after` |
| `wa-field-group-delete-btn` | the remove button |
| `form-group-icon` | all three buttons |

To change the markup itself, not only its classes, write a [custom form template](custom-form-template.md).
