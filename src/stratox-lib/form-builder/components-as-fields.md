---
description: >-
  Use any Stratox component as a form field: put headings and notes between fields and in repeater rows, or build a
  field type of your own as a component, with its name and value from the form builder.
---

# Components as fields

Fields and components share one set of names. A field whose `type` is a registered component renders that
component, with the field's `data` as its props. That puts your own markup between fields, in every row of a
repeater, or makes a whole new kind of field.

## A component between fields

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({ handlers: { fields: StratoxTemplate } });
Stratox.setComponent('Heading', ({ props, html }) => html`<h3>${props.text}</h3>`);

const booking = new Stratox('#booking');
booking.form('who', { type: 'Heading', data: { text: 'Who is coming?' } });
booking.form('guests', { type: 'group' })
  .setConfig({ nestedNames: true, controls: true })
  .setFields({
    note: { type: 'Heading', data: { text: 'Guest' } },
    name: { type: 'text', label: 'Name' },
  });
booking.execute();
console.log([...document.querySelectorAll('#booking h3')].map((heading) => heading.textContent));
```

{% code title="Result" %}
```json
["Who is coming?", "Guest"]
```
{% endcode %}

The component in the repeater renders in every row. It posts nothing, because it has no input.

## Defaults for props

`context` in the component's object is the form builder. `context.setDefault()` adds props that the field's `data`
did not give:

```js
Stratox.setComponent('Hint', ({ props, context, html }) => {
  context.setDefault({ tone: 'info', text: 'Optional' });
  return html`<p class="hint ${props.tone}">${props.text}</p>`;
});

const hints = new Stratox('#hints');
hints.form('first', { type: 'Hint', data: { text: 'Shown on your profile' } });
hints.form('second', { type: 'Hint' });
hints.execute();
```

{% code title="Result" %}
```html
<div id="hints">
  <p class="hint info">Shown on your profile</p>
  <p class="hint info">Optional</p>
</div>
```
{% endcode %}

## Your own kind of field

A component can also be the input. The builder knows the field's name and value: `context.name` is the input's
name (with the row path in a repeater), `context.value` its value, and `context.isChecked(value)` tells if an option
matches the value. Write `context.nameJoin` into `data-name`, and Stratox keeps `getValues()` up to date when the
user changes the field in a form with a repeater:

```js
Stratox.setComponent('Stars', ({ props, context, html }) => html`
  <fieldset>
    <legend>${props.label}</legend>
    ${[1, 2, 3, 4, 5].map((stars) => html`
      <label>
        <input type="radio" name="${context.name}" value="${stars}" data-name="${context.nameJoin}"
          ${context.isChecked(stars) ? 'checked' : ''}>
        ${stars}
      </label>
    `)}
  </fieldset>
`);

const reviews = new Stratox('#reviews');
reviews.form('reviews', { type: 'group' })
  .setConfig({ nestedNames: true, controls: true })
  .setFields({
    product: { type: 'text', label: 'Product' },
    score: { type: 'Stars', data: { label: 'Your rating' } },
  });
reviews.setValues({ reviews: [{ product: 'Desk lamp', score: 4 }] });
reviews.execute();
```

{% code title="Result: what the form posts" %}
```
reviews[0][product] = Desk lamp
reviews[0][score] = 4
```
{% endcode %}

After the user picks 5 stars, `reviews.getValues()` gives:

{% code title="Result after a click" %}
```json
{ "reviews": [{ "product": "Desk lamp", "score": "5" }] }
```
{% endcode %}

The value from the input is text, `"5"`, as the browser gives it.

A field type that should be part of every form is often easier as a method of a [custom form
template](custom-form-template.md): it gets the label, container and attributes for free.

## Components loaded on demand

A field whose `type` is not registered, and is not a field type, is imported from the `directory` setting by its
name: `{ type: 'Stars' }` loads `Stars.js` from that folder, also inside repeaters at any depth. The form renders
once every import is done; wait for it with `await form.ready()`. A type that cannot be loaded leaves out only that
field and logs why.
