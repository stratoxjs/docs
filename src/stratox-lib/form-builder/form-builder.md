---
description: Create dynamic, responsive, and engaging web forms with ease.
---

# Form builder

To be able to use the form builder, you will need to specify a form fields template file in your config.

```javascript
import { Stratox } from './node_modules/stratox/src/Stratox.js';
import { StratoxTemplate } from './node_modules/stratox/src/StratoxTemplate.js';

Stratox.setConfigs({
  handlers: {
    fields: StratoxTemplate,
  }
});
```

_Once a instance of StratoxTemplate has been added to the handlers.fields config object you will be able to use the form builder._

### Available form fields

Available form fields out of the box:

* text (password, tel, email, number... and so on)
* textarea
* date
* datetime
* hidden
* select
* radio
* checkbox
* submit (button)
* group
* views/componets

**And can be combined** with all views and components the you have created!

### Form field settings

Available form fields out of the box. Se working examples bellow.

```javascript
let form = new Stratox('#form');
form.form('theFieldName')
	.setType('checkbox') // Default is "text"
	.setLabel('Label')
	.setDescription('Add field description')
	.setAttr({ type: "email", id: "inp-email" }) // Create or overwrite html attributes
	.setConfig({ pass: "configs" }) // Pass configs
	.setItems({ yes: "Yes", no: "No" }) // Add (checkbox, radio or select list items)
	.setFields({ ... }) // Group fields, see example bellow
	.setValue("Field value");
```

### Example 1

Begin by adding an element to the HTML document. This is where the template will be loaded.

#### HTML:

```javascript

<div id="app"></div>

```

#### Javascript:

```javascript

let form = new Stratox('#app');
form.form('name').setLabel('Name').setValue("Jane doe");
form.form('email').setLabel('Email').setAttr({ type: "email", id: "inp-email" });
form.form('message').setType('textarea').setLabel('Message');
form.execute();

```

#### The result

{% embed url="https://codepen.io/wazabii8/pen/gOEWBdY" %}

### Combine template and form

As long as you are using the same instance then you can combine templates with outher template and template with forms.

Lets add our form to the ingress example in prevous page (Views).

#### HTML:

```javascript

<div id="app"></div>

```

#### JavaScript:

```javascript
let form = new Stratox('#app');

form.view("ingress", {
    headline: "Lorem ipsum dolor",
    content: "Lorem ipsum dolor sit amet"
});

form.form('name').setLabel('Name').setValue("Jane doe");
form.form('email').setLabel('Email').setAttr({ type: "email", id: "inp-email" });
form.form('message').setType('textarea').setLabel('Message');
form.execute();
```

#### The result

{% embed url="https://codepen.io/wazabii8/pen/xxBdymJ" %}

#### Group form fields

You can even group form fields and components. I will use the ingress as a example again. You can download the CSS class here form my repeat fields:

[repeat-fields.css](https://wazabii.se/stratoxjs/repeat-fields.css)

#### HTML:

```javascript

<div id="app"></div>

```

#### JavaScript:

```javascript
let form = new Stratox("#app");

form.view("ingress", {
    headline: "Advanced forms",
    content: "Lorem ipsum dolor sit amet"
});

form.form('name').setLabel('Name');
form.form('email').setLabel('Email');
form.form('message').setType('textarea').setLabel('Message');

form.form("customField", { type: "group" })
.setFields({
	// To add component in form you just specify the type and data!
	ingress: {
		type: "ingress",
		data: {
		    headline: "Repeatable fields",
		    content: "Even here can i combine the ingress component!"
		}
	},
	// Add form fields with json
	title: {
		type: "text",
		label: "Title"
	},
	description: {
		type: "textarea",
		label: "Description"
	}
})
.setConfig({
	nestedNames: true, // This will nest the form name automatically
	controls: true // Auto add controls (add and delete fields)
})

// You can also set the values of all fields like this 
// (typically passed from a database).
form.setValues({ name: "John Doe", email: "john.doe@gmail.com" });

form.execute();
```

* **nestedNames**:True: This will nest the form name automatically e.g:\
  (customField\[0]\[title], customField\[1]\[title])False: This will not transform the form fields name eg:\
  title, title
* **controls**: Auto add controls (add and delete fields)

#### The result

{% embed url="https://codepen.io/wazabii8/pen/PoLmyvq" %}
