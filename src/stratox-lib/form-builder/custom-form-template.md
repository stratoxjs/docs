---
description: Create a custom form template
---

# Custom form template

### Create form template file

The new form field template class needs to extend to StratoxTemplate.

```javascript
import { StratoxTemplate } from '../node_modules/stratox/src/StratoxTemplate.js';

export class FormTemplateFields extends StratoxTemplate {
    
    /**
     * Regular input field
     * @return {string}
     */
    text() {
        let inst = this;
        return this.container(function() {
            return inst.input();
        });
    }

    /**
     * Regular input field
     * @return {string}
     */
    text(arg) {
        let inst = this;
        return this.container(function() {
            return inst.input();
        });
    }

    /**
     * Password input field
     * @return {string}
     */
    password() {
        let inst = this;
        return this.container(function() {
            let out =  inst.input({ type: "password" });
            return out;
        });
    }

    /**
     * Date input field
     * @return {string}
     */
    date() {
        let inst = this;
        return this.container(function() {
            return inst.input({ type: "date" });
        });
    }

    /**
     * Date time input field
     * @return {string}
     */
    datetime() {
        let inst = this;
        return this.container(function() {
            return inst.input({ type: "datetime-local" });
        });
    }

    /**
     * Hidden input field
     * @return {string}
     */
    hidden() {
        let inst = this;
        return inst.input({ type: "hidden" });
    }

    /**
     * Textarea field
     * @return {string}
     */
    textarea() {
        let inst = this, attr = this.getAttr({
            name: this.name,
            "data-index": this.index
        });
        
        return this.container(function() {
            return '<textarea'+attr+'>'+inst.value+'</textarea>';
        }); 
    }
    
    /**
     * Select field
     * @return {string}
     */
    select() {
        let inst = this, attrName = ((this.attr && this.attr.multiple) ? this.name+"[]" : this.name), 
        attr = this.getAttr({
            name: attrName,
            "data-index": this.index
        });

        return this.container(function() {
            let out = '<select'+attr+' autocomplete="off">';
            if(typeof inst.data.items === "object") {
                for(const [value, name] of Object.entries(inst.data.items)) {
                    let selected  = (inst.isChecked(value))  ? ' selected="selected"' : "";
                    out += '<option value="'+value+'"'+selected+'>'+name+'</option>';
                }
            } else {
                console.warn("Object items parameter is missing.");
            }
            out += '</select>';
            return out;
        });
    }

    /**
     * Radio input field
     * @return {string}
     */
    radio() {
        let inst = this, attr = this.getAttr({
            type: "radio",
            name: this.name,
            "data-index": this.index
        });

        return this.container(function() {
            let out = '';
            if(typeof inst.data.items === "object") {
                for(const [value, name] of Object.entries(inst.data.items)) {
                    let checked  = (inst.isChecked(value))  ? ' checked="checked"' : "";
                    out += '<label class="radio item small"><input'+attr+' value="'+value+'"'+checked+'><span class="title">'+name+'</span></label>';
                }
            } else {
                console.warn("Object items parameter is missing.");
            }
            return out;
        });
    }

    /**
     * Checkbox input field
     * @return {string}
     */
    checkbox() {
        let inst = this, length = Object.keys(inst.data.items).length, attr = this.getAttr({
            type: "checkbox",
            name: ((length > 1) ? this.name+"[]" : this.name),
            "data-index": this.index
        });

        return this.container(function() {
            let out = '';
            if(typeof inst.data.items === "object") {
                for(const [value, name] of Object.entries(inst.data.items)) {
                    let checked  = (inst.isChecked(value))  ? ' checked="checked"' : "";
                    out += '<label class="checkbox item small"><input'+attr+' value="'+value+'"'+checked+'><span class="title">'+name+'</span></label>';
                }
            } else {
                console.warn("Object items parameter is missing.");
            }
            return out;
        });
    }

}
```

### Break down

To make a quick breakdown of the above: the functions `container` , `input` and `getAttr` are  helper function, to make it easier for you create an input container that also holds a field label and input, creating the input tag fields or generate HTML attributes. They are not required, and you could just write your own HTML code and return that string, and it will work.

But to make it easier for you to understand let's take a look on how the **textarea** is build:

```javascript
textarea() {
    let inst = this, attr = this.getAttr({
        name: this.name,
        "data-index": this.index
    });
    
    return this.container(function() {
        return '<textarea'+attr+'>'+inst.value+'</textarea>';
    }); 
}
```

#### Helper functions:&#x20;

Bellow is a list on helper functions that you can use in your form field template.

* **getAttr:** It will **merge** specified attributes in the form builder with **default** attributes above, which we will then just add to the **textarea**. It is recommended that you use this function, with at least the name attribute, as shown in the example.
* **container:** Will create input container that holds a label and description for your.
* **input:** Will create input tag fields or generate HTML attributes.
* **isChecked:** Will check if items like radio, checkboxes and select lists is active.
* **getFieldID:** Will get a unique identifier that you can use as a element ID. E.G. the container function will automatically do this for you.

#### Accessible objects:

Bellow is a list on object that you can use in your form field template.

* **label:** Will return the expected form field label
* **description:** Will return a form field description you can use if you want
* **attr:** Will return all expected form field attributes as object (recommended tho that you utilize getAttr function)
* **items:** Will return the expected form field items (radio, checkboxes, and list in select lists).
* **name:** Will return the expected form field attribute name
* **value:** Will return the expected form field value
* **config:** Will return the expected form field custom configs that is passable

### Initialize the new template

You can now tell Stratox which form template file which is expected in the configuration.

<pre class="language-javascript"><code class="lang-javascript"><strong>import { FormTemplateFields } from './src/FormTemplateFields.js';
</strong><strong>
</strong>Stratox.setConfigs({
    handlers: {
    	fields: FormTemplateFields
    }
});
<strong>
</strong></code></pre>

Done you can now use your own form template!
