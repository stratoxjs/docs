---
description: >-
  Keep in mind that the guide is designed to be read linearly, so try to avoid
  jumping through it on the first read through. It will only take 30 minutes of
  your time.
---

# Installation

## Installation

```
npm i stratox
```

_Or just download the zip and import Stratox.js file_

### Import Stratox

Start by importing "Stratox.js".

```js
import { Stratox } from './node_modules/stratox/src/Stratox.js';
```

### Config

None of the configs bellow is required for Stratox to work, but they will enable and extends some functionality. Just make sure that the config is executed before any Stratox class instances is called.

```js
Stratox.setConfigs({
    directory: "/absolute/path/to/views/", // Used for autoload
    cache: false, // Automatically clear cache if is false on dynamic import
    handlers: {
    	fields: StratoxTemplate, // Optional: will add form builder (se bellow)
    	helper: function() {
    	    // Pass on helper classes, functions and objects to your views
    	    return {
    		helper1: "Mixed data...",
                helper2: "Could be classes You want to",
                helper3: "Pass on to you components",
    	    };
    	}
    }
});
```

**directory:** Directory path for auto loaded asynchronously or imported templates. Either specify an absolute directory path or if opting for a relative path, **but** keep in mind it starts from the Stratox.js file location meaning if you bundle your files the relative location **will change** to where the bundle file is located at.

**cache:** Automatically clear cache if is false on dynamic import.

**handlers.fields:** Create a custom class handler for creating or modifying form field items, including default fields. The form field handler must extend the "StratoxBuilder" class, which is located in "node\_modules/stratox/src/StratoxBuilder.js". You can also create a new class (or copy the StratoxTemplate.js file) and extend your new class to StratoxTemplate if you want to add default fields or StratoxBuilder if you want to start fresh. Then just create your own form fields in your class. Read more under "Form builder" section.

**handler.helper:** Pass on helper classes, functions and objects to your views. If you are using a DOM traversal enginge then you could pass it on to the helper that in turn passes it on to your components, views and fields.
