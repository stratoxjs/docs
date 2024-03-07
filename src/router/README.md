# Stratox router

Startox Pilot is a JavaScript router designed for ease of use and flexibility. It employs regular expressions to offer dynamic routing, allowing for both straightforward and complex navigation paths. As a universal library, it works across different platforms without needing any external dependencies. This independence makes Startox Pilot a practical option for developers in search of a dependable routing tool that combines advanced features and modular design in a compact package.

## A basic example

Below is a simple yet comprehensive example. Each component will be explored in further detail later in this guide.

```javascript
import { Router, Dispatcher } from '@stratox/pilot';

const router = new Router();
const dispatcher = new Dispatcher();

// GET: example.se/
router.get('/', function() {
    console.log("Start page");
});

// GET: example.se/#about 
// REGULAR URI paths (example.se/about) is of course also supported!
router.get('/about', function(vars, request, path) {
    const page = vars[0].pop();
    console.log(`The current page is: ${page}`);
});

// GET: example.se/#articles/824/hello-world
router.get('/articles/{id:[0-9]+}/{slug:[^/]+}', function(vars, request, path) {
    const id = vars.id.pop();
    const slug = vars.slug.pop();
    console.log(`Article post ID is: ${id} and post slug is: ${slug}.`);
});

// POST: example.se/#post/contact
router.post('/post/contact', function(vars, request, path) {
    console.log(`Contact form catched with post:`, request.post);
});

// Will catch 404 and 405 HTTP Status Errors codes
// Not required you can also handle it directly in the dispatcher
router.get('[STATUS_ERROR]', function(vars, request, path, statusCode) {
    if(statusCode === 404) {
        console.log("404 Page not found", statusCode);
    } else {
        console.log("405 Method not allowed", statusCode);
    }
});

dispatcher.dispatcher(router, dispatcher.serverParams("fragment"), function(response, statusCode) {
    // response.controller is equal to what the routers second argument is being fed with.
    // You can add Ajax here if you wish to trigger a ajax call.
    response.controller(response.vars, response.request, response.path, statusCode);
});
// URI HASH: dispatcher.serverParams("fragment") // Fragment is HASH without "#" character.
// URI PATH: dispatcher.serverParams("path") // Regular URI path
// SCRIPT PATH: dispatcher.request("path") // Will work without browser window.history support
```
