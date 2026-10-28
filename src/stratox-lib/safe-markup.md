---
description: >-
  Stratox escapes every value you put into markup, so text from users and servers cannot inject HTML. How html,
  raw() and escape() work, script URLs, and what the form builder inserts as HTML.
---

# Safe markup

Stratox escapes by default. Every value you put into `` html`...` `` is shown as text, so a comment, a name or a
search term cannot add elements or scripts to your page.

## Values are text

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Comment', ({ props, html }) => html`
  <p class="comment">${props.text}</p>
`);

const comments = new Stratox('#comments');
comments.view('Comment', { text: 'Nice! <img src="x" onerror="alert(1)">' });
comments.execute();
```

{% code title="Result" %}
```html
<div id="comments">
  <p class="comment">Nice! &lt;img src="x" onerror="alert(1)"&gt;</p>
</div>
```
{% endcode %}

`html` escapes `&`, `<`, `>`, `"` and `'` in every value. That covers text between tags and attribute values in
quotes. Always put quotes around an attribute value, `value="${props.name}"`, never `value=${props.name}`.

What `html` does with each kind of value:

| Value | Result |
| --- | --- |
| text, numbers | escaped |
| `` html`...` `` and `raw()` | inserted as markup |
| an array | each item by these rules, joined |
| `null`, `undefined`, `false` | nothing |

## Script URLs

A `javascript:` or `vbscript:` URL that you put into `href`, `src`, `action`, `formaction` or `xlink:href` gets the
prefix `unsafe:`, so it does not run:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Profile', ({ props, html }) => html`
  <a href="${props.website}">${props.name}</a>
`);

const profile = new Stratox('#profile');
profile.view('Profile', { name: 'Mallory', website: 'javascript:alert(1)' });
profile.execute();
```

{% code title="Result" %}
```html
<div id="profile">
  <a href="unsafe:javascript:alert(1)">Mallory</a>
</div>
```
{% endcode %}

Other URLs are kept as they are. If users give you links, also check them on your server.

## raw() for markup you trust

Some markup is meant to be markup, such as an article body from your own CMS. Wrap it in `raw()`, and `html` inserts
it as it is:

```js
import { Stratox } from 'stratox';

Stratox.setComponent('Article', ({ props, html, raw }) => html`
  <h1>${props.title}</h1>
  <div class="body">${raw(props.body)}</div>
`);

const article = new Stratox('#article');
article.view('Article', { title: 'Opening hours', body: '<p>We are open <strong>every day</strong>.</p>' });
article.execute();
```

{% code title="Result" %}
```html
<div id="article">
  <h1>Opening hours</h1>
  <div class="body">
    <p>We are open <strong>every day</strong>.</p>
  </div>
</div>
```
{% endcode %}

{% hint style="danger" %}
Never pass text from users to `raw()`. It is the one way past the escaping, so search your code for `raw(` to find
every place that needs trust.
{% endhint %}

`raw` comes in the component's object, and you can also import it: `import { raw } from 'stratox'`.

## escape() for strings

`escape(value)` returns the value as escaped text, for code that builds HTML strings without `html`:

```js
import { escape } from 'stratox';

console.log(escape('Tom & Jerry <3'));
```

{% code title="Result" %}
```
Tom &amp; Jerry &lt;3
```
{% endcode %}

Inside `` html`...` `` you do not need it: a string from `escape()` is still a plain string, so `html` escapes it a
second time.

## SafeHtml

`html` and `raw()` return a `SafeHtml` object. That is how Stratox tells markup from text: a `SafeHtml` is inserted as
markup, anything else is escaped. `String(markup)` gives its HTML:

```js
import { html, SafeHtml } from 'stratox';

const markup = html`<em>${'Fish & chips'}</em>`;
console.log(markup instanceof SafeHtml);
console.log(String(markup));
```

{% code title="Result" %}
```
true
<em>Fish &amp; chips</em>
```
{% endcode %}

A component that returns a plain string, not a `SafeHtml`, is shown as text. See [Components](template-engine/create-templates.md).

## In the form builder

The form builder escapes field values and attributes. Labels, descriptions and the titles of options are inserted as
HTML, so you can format them, and they must come from a source you trust:

```js
import { Stratox, StratoxTemplate } from 'stratox';

Stratox.setConfigs({ handlers: { fields: StratoxTemplate } });

const signup = new Stratox('#signup');
signup.form('nickname', { label: 'Nickname <em>(public)</em>' });
signup.setValues({ nickname: '"><script>alert(1)</script>' });
signup.execute();
```

{% code title="Result" %}
```html
<div id="signup">
  <div id="wa-fi-view-0" data-index="0" class="mb-15 field-nickname w-full">
    <label>Nickname <em>(public)</em><div class="message hide"></div></label>
    <input type="text" name="nickname" value="&quot;&gt;&lt;script&gt;alert(1)&lt;/script&gt;" data-index="0" data-name="nickname">
  </div>
</div>
```
{% endcode %}

The label shows `(public)` in italics; the value stays inside the field as text.
