---
description: >-
  Repeaters in the Stratox form builder: rows of fields the user can add and remove, nested to any depth, named so
  your backend receives nested arrays, and kept in sync with getValues().
---

# Repeaters

A repeater is a field of type `group`: a set of fields shown once per row. The user adds and removes rows with
buttons, rows can hold repeaters of their own, and every input is named with its row, so the form posts nested data.

## A repeater

Give the group its fields with `setFields()`, and turn on `nestedNames` and `controls`:

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({ handlers: { fields: StratoxTemplate } });

const team = new Stratox('#team');

team.form('members', { type: 'group', label: 'Team members' })
  .setConfig({ nestedNames: true, controls: true })
  .setFields({
    name: { type: 'text', label: 'Name' },
    role: { type: 'select', label: 'Role', items: { dev: 'Developer', design: 'Designer' } },
  });

team.execute();
```

{% code title="Result" %}
```html
<div id="team">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-members w-full">
    <label>Team members<div class="message hide"></div></label>
    <div id="wa-fi-view-0-group" class="mb-20 wa-advanced-grouped-field">
      <div class="group relative card-3 mb-15 rounded border border-primary" data-length="1">
        <a class="wa-field-group-btn form-group-icon before inline-block pad top-0 left-1/2 -translate-x-2/4 -translate-y-2/4 absolute z-10"
          data-name="members" data-key="view" data-index="1" data-position="0" href="#"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32" width="12" height="12" fill="none" stroke="currentcolor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.6"><path d="M16 2 L16 30 M2 16 L30 16"></path></svg></a>
        <div id="wa-fi-view-1" data-index="1" class="mb-15 field-members,0,name w-full">
          <label>Name<div class="message hide"></div></label>
          <input type="text" name="members[0][name]" value="" data-index="1" data-name="members,0,name">
        </div>
        <div id="wa-fi-view-2" data-index="2" class="mb-15 field-members,0,role w-full">
          <label>Role<div class="message hide"></div></label>
          <select name="members[0][role]" data-index="2" data-name="members,0,role" autocomplete="off">
            <option value="dev">Developer</option>
            <option value="design">Designer</option>
          </select>
        </div>
        <a class="wa-field-group-btn form-group-icon after inline-block pad bottom-0 left-1/2 -translate-x-2/4 translate-y-2/4 absolute z-10"
          data-name="members" data-key="view" data-index="1" data-position="0" href="#"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32" width="12" height="12" fill="none" stroke="currentcolor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.6"><path d="M16 2 L16 30 M2 16 L30 16"></path></svg></a>
      </div>
    </div>
  </div>
</div>
```
{% endcode %}

* A repeater without values shows one empty row.
* `controls: true` adds the buttons: add a row before, add a row after, and, when there is more than one row,
  remove the row. The last row cannot be removed. The buttons are links with a small SVG icon; [Styling forms](styling.md) shows how to change them.
* `nestedNames: true` names each input with the group and the row: `members[0][name]`.

## Rows from values

The rows come from the array at the group's name in `setValues()`:

```js
team.setValues({
  members: [
    { name: 'Ada', role: 'dev' },
    { name: 'Grace', role: 'design' },
  ],
});
team.update();
```

What the form posts now:

{% code title="Result: what the form posts" %}
```
members[0][name] = Ada
members[0][role] = dev
members[1][name] = Grace
members[1][role] = design
```
{% endcode %}

Your backend receives the rows as a nested list. PHP, for example, reads `$_POST['members'][1]['name']` as
`"Grace"`; most other backends have the same convention or a library for it.

## The values follow the user

In a form with a repeater, Stratox writes every change into the values object: typing, checking, adding and removing
rows. `getValues()` is always the current state. `team.getValues()` after the user types "Linus" over the second
name:

{% code title="Result after typing" %}
```json
{
  "members": [
    { "name": "Ada", "role": "dev" },
    { "name": "Linus", "role": "design" }
  ]
}
```
{% endcode %}

After a click on the add button below the last row, the new, empty row is in the values:

{% code title="Result after adding a row" %}
```json
{
  "members": [
    { "name": "Ada", "role": "dev" },
    { "name": "Linus", "role": "design" },
    {}
  ]
}
```
{% endcode %}

And after a click on the remove button of the first row:

{% code title="Result after removing a row" %}
```json
{
  "members": [
    { "name": "Linus", "role": "design" },
    {}
  ]
}
```
{% endcode %}

A new row starts empty in the values; a field gets its key when the user changes it. A `select` the user has not
touched still posts its first option, as in any HTML form. Rows render from the values, so text typed in other rows
stays where it is.

## Rows inside rows

A field in a group can be a group itself, as deep as you need. Give the inner group its settings in `config` and its
fields in `fields`:

```js
const schedule = new Stratox('#schedule');

schedule.form('days', { type: 'group', label: 'Days' })
  .setConfig({ nestedNames: true, controls: true })
  .setFields({
    date: { type: 'date', label: 'Date' },
    talks: {
      type: 'group',
      label: 'Talks',
      config: { nestedNames: true, controls: true },
      fields: {
        title: { type: 'text', label: 'Title' },
        speaker: { type: 'text', label: 'Speaker' },
      },
    },
  });

schedule.setValues({
  days: [
    { date: '2026-11-02', talks: [{ title: 'Forms', speaker: 'Ada' }, { title: 'Routing', speaker: 'Linus' }] },
    { date: '2026-11-03', talks: [{ title: 'Islands', speaker: 'Grace' }] },
  ],
});
schedule.execute();
```

{% code title="Result: what the schedule posts" %}
```
days[0][date] = 2026-11-02
days[0][talks][0][title] = Forms
days[0][talks][0][speaker] = Ada
days[0][talks][1][title] = Routing
days[0][talks][1][speaker] = Linus
days[1][date] = 2026-11-03
days[1][talks][0][title] = Islands
days[1][talks][0][speaker] = Grace
```
{% endcode %}

## Names

* With `nestedNames: true`, a field is named `group[row][field]`, and nested groups add their own parts.
* Without it, every row repeats the bare field name (`name="title"` in each row), and its value comes from the
  top-level value of that name. Use this only when your backend expects it.
* A field with its own `name` keeps the row path: `{ type: 'text', name: 'custom' }` in `members` is named
  `members[0][custom]`.

## A repeater without buttons

Leave out `controls` to show exactly the rows of the values, for example a fixed list the user fills in:

```js
const survey = new Stratox('#survey');
survey.form('answers', { type: 'group' })
  .setConfig({ nestedNames: true })
  .setFields({ answer: { type: 'text', label: 'Answer' } });
survey.setValues({ answers: [{ answer: '' }, { answer: '' }, { answer: '' }] });
survey.execute();
console.log(document.querySelectorAll('#survey input').length);
```

{% code title="Result" %}
```
3
```
{% endcode %}

Repeaters work best with the default stylesheet's layout or your own; see [Styling forms](styling.md).
