# Add routes

### A really Basic example

```javascript
// Possible path: #about
router.get('/about', function(vars, request, path) {
});
```

And you can of course **add multiple** paths.

```javascript
// Possible path: #about/contact
router.get('/about/contact', function(vars, request, path) {
});
```

### Using Regular Expressions

To incorporate regular expressions in routing patterns, enclose the expression within **curly brackets: `{PATTERN}`**. This syntax allows for flexible and powerful URL matching based on specified patterns.

```javascript
// Possible path: #about/location/stockholm
router.get('/about/location/{[a-z]+}', function(vars, request, path) {
});
```

### Binding Router Patterns to a Key

It is strongly advised to associate each URI path you wish to access with a specific **key**. This approach enhances the clarity and manageability of your route definitions.

```javascript
// Possible path: #about/location/stockholm
router.get('/{page:about}/location/{city:[^/]+}', function(vars, request, path) {
    //vars.page[0] is expected to be "about"
    //vars.city[0] is expected to be any string value (stockholm, denmark, new-york) from passed URI.
});
```

You can also map an entire path to a **key**, allowing for more concise and organized route management.

```javascript
// Possible path: #about/contact
router.get('/{page:about/location}', function(vars, request, path) {
    //vars.page[0] is expected to be "about"
    //vars.page[1] is expected to be "location"
});
```

### Combining pattern with keywords

Combining patterns with keywords e.g. (**post-**\[0-9]+) enables you to create more expressive and versatile route definitions.

```javascript
// Possible path: #articles/post-824/hello-world
router.get('/articles/{id:post-[0-9]+}/{slug:[^/]+}', function(vars, request, path) {
    //vars.id[0] is expected to be "post-824"
    //vars.slug[0] is expected to be "hello-world"
});
```

### Handling Unlimited Nested Paths

To accommodate an unlimited number of nested paths within your routing configuration, you can utilize the pattern `".+"`. However, it's strongly advised to precede such a router pattern with a specific prefix to maintain clarity and structure, as demonstrated in the example below with the prefix `/shop`.

```javascript
// Example of accessing a single category: #shop/furniture
// Example of accessing multiple nested categories: #shop/furniture/sofas/chesterfield
router.get('/shop/{category:.+}', function(vars, request, path) {
    // Retrieves the last category segment from the path
    const category = vars.category.pop();
    console.log(`The current category is: ${category}`);
});
```

This approach allows for the dynamic handling of deeply nested routes under a common parent path, offering flexibility in how URLs are structured and processed.

### Optional URI Paths

To define one or more optional URI paths, enclose the path segment (excluding the slash) in brackets followed by a question mark, for example: **/(PATH\_NAME)?**. This syntax allows for flexibility in route matching by making certain path segments non-mandatory.

```javascript
// Possible path: #articles
// Possible path: #articles/post-824/hello-world
router.get('/articles/({id:post-[0-9]+})?/({slug:[^/]+})?', function(vars, request, path) {
});
```

It's important to note that you should not enclose the **leading slash** in brackets. The leading slash is automatically excluded from the pattern, ensuring the correct interpretation of the route.

### Catch status errors

There is an optional and special router pattern that let's you catch HTTP Status Errors with in a router.

```javascript
router.get('[STATUS_ERROR]', function(vars, request, path, statusCode) {
    if(statusCode === 404) {
        console.log("404 Page not found", statusCode);
    } else {
        console.log("405 Method not allowed", statusCode);
    }
});
```
