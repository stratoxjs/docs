# Installation

## Installation

```javascript
npm install @stratox/pilot
```

## Configuration options

The dispatcher offers several configuration options to tailor its behavior to your application's needs.

```javascript
const dispatcher = new Dispatcher({
    catchForms: false, // Toggle form submission catching
    root: "", // Set a root directory
    fragmentPrefix: "" // Define a prefix for hash/fragment navigation
});
```

### Configuration Parameters

* **catchForms (bool):** When set to `true`, enables the dispatcher to automatically intercept and route form submissions. This feature facilitates seamless integration of form-based navigation within your application.
* **root (string):** This parameter allows you to specify a root directory using an **absolute path**. This setting is crucial for defining where simulated or "pretty" URI paths begin. The necessity of this configuration depends on your specific deployment environment.
* **fragmentPrefix (string):** This option lets you prepend a prefix to fragment or hash navigation calls. For instance, adding the "!" character means the URL's hash will be expected to appear as "#!your-hash", modifying the default behavior to accommodate specific routing schemes or to enhance compatibility with certain browsers or frameworks.

## Defining routes

In Stratox Pilot, there are two primary router types: `get` and `post`. Both types follow the same structural format, as illustrated below, with the key difference being that they will expect different request (see navigation for more information)

```
router.get(string pattern, mixed call);
router.post(string pattern, mixed call);
```

#### Arguments

* **pattern (string):** This parameter expects a URI path in the form of a string, which may include regular expressions for more complex matching criteria.
* **call (mixed):** This parameter can accept any data type, such as a callable, anonymous function, string, number, or boolean. However, it is most common to use a function. For the purposes of this guide, I use a regular callable function in my examples.
