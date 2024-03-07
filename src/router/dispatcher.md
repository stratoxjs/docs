# Dispatcher

## Dispatcher overview

The dispatcher is essential for identifying and providing the appropriate route from the state handler. Designed for flexibility, it enables the incorporation of custom logic, such as AJAX, to tailor functionality to specific needs.

```javascript
dispatcher.dispatcher(Router routerCollection, serverParams, callable dispatch);
```

#### Arguments

* [**routerCollection**](dispatcher.md#router-collection-routercollection)
* [**serverParams**](dispatcher.md#server-params-serverparams)
* [**dispatch**](dispatcher.md#dispatch-function-dispatch)

### Router Collection (routerCollection)

This expects a Router instance, allowing for customization. You can create your router collection extending the Router class, potentially adding more HTTP methods, structure, or functionality.

### Server Params (serverParams)

Server params indicate the URL segment the dispatcher should utilize. These params dynamically target the specified URI segment. Several built-in options include:

#### URI Fragment

Represents the URL hash or anchor minus the "#" character.

```javascript
dispatcher.serverParams("fragment");
```

#### URI Path

The regular URI path segment.

```javascript
dispatcher.serverParams("path");
```

#### Script Path

Ideal for non-browser environments, supporting backend applications, APIs, or shell command routes.

```javascript
dispatcher.request("path");
```

### Dispatch Function (dispatch)

The "dispatch" argument expects a callable function to process the match result, handling both successful (status code 200) and error outcomes (status code 404 for "page not found" and 405 for "Method not allowed"). The function receives two parameters: response (object) and statusCode (int).

#### Response Details

* **response (object):** Provides an object with vital response data.
* **statusCode (int):** Indicates the result, either successful (200) or error (404 or 405).

### Basic Dispatcher Example

Below is an excerpt from the example at the start of the guide:

```javascript
dispatcher.dispatcher(router, dispatcher.serverParams("fragment"), function(response, statusCode) {
    response.controller(response.vars, response.request, response.path, statusCode);
});
```

### Understanding the Response

The response structure, as illustrated with the router pattern `"/{page:product}/{id:[0-9]+}/{slug:[^/]+}"`, and URI path **/product/72/chesterfield** includes:

```json
{
    "verb": "GET",
    "status": 200,
    "path": ["product", "72", "chesterfield"],
    "vars": {
        "page": "product",
        "id": "72",
        "slug": "chesterfield"
    },
    "form": {},
    "request": {
        "get": "URLSearchParams",
        "post": "FormData"
    }
}
```

* **verb:** The HTTP method (GET or POST).
* **status:** The HTTP status code (200, 404, or 405).
* **path:** The URI path as an array.
* **vars:** An object mapping path segments to keys.
* **form:** Captures submitted DOM form elements.
* **request.get:** An instance of URLSearchParams for GET requests.
* **request.post:** An instance of FormData for POST requests.
