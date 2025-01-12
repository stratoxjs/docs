---
description: >-
  Stratox also comes with a specialized JavaScript container library designed
  for seamless communication between template views and the application. It
  allows for efficient data exchange.
---

# Container

### Access container

A new container instance will be binded to each Stratox class instance. You can then access the container instance both inside a component and outside of it.

#### 1. Inside component

```javascript
export function custom({ services })
{
    if (services.has("fallback")) {
        services.get("fallback");
    }
    ...
```

#### 2. Outside of component

```javascript
const stratox = new Stratox("#ingress");
const serviceContainer = stratox.container();
```

### Container usage

Bellow is some quick example on different ways you can use the container.

```javascript
// Example 1
serviceContainer.set("someObject", { test: "Container 1" });

console.log(serviceContainer.get("someObject").test);
// Log response: Container 1

// Example 2
serviceContainer.set("passingAFunction", function(arg1, arg2) {
	alert(arg1+" "+arg2);
});

serviceContainer.get("passingAFunction", "Hello", "world!");
// Alert response: Hello world!
```

### Stand alone

You can also use the container in other projects.

```javascript
import { StratoxContainer } from 'stratox/StratoxContainer';
const container = new StratoxContainer();
```

### Method list

**Set a container or factory**

```
set(key, value, overwrite);
```

* **key:** Unique container key/string identifier
* **value:** Mixed value of whatever you want to share, e.g. String, number, object, function.&#x20;
* **overwrite:** Attempting to set a container multiple times will trigger a warning unless manual consent is provided by setting "overwrite" to true.

**Set factory only**

```
setFactory(key, value, overwrite);
```

* **key:** Unique factory key/string identifier&#x20;
* **value:** callable
* **overwrite:** Attempting to set a container multiple times will trigger a warning unless manual consent is provided by setting "overwrite" to true.

**Get a container or factory**

```
get(key, ...args);
```

* **key:** Unique container key/string identifier
* **args:** pass arguments to possible factory/function

**Check if container/factory exists**

```
has(key);
```

* **key:** Unique container key/string identifier

**Check if is strict a "container".**

```
isContainer(key);
```

* **key:** Unique container key/string identifier

**Check if is strict a "factory/function".**

```
isFactory(key);
```

* **key:** Unique factory key/string identifier
