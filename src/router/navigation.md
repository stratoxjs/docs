# Navigation

## Navigation

The library provides intuitive navigation options to seamlessly transition between pages and initiate GET or POST requests.

### Page Navigation / GET Request

Initiating a GET request or navigating to a new page is straightforward. Such actions will correspond to a `get` router, with the request parameter converting into an instance of URLSearchParams for the request.

#### Arguments

* **path (string):** Specifies the URI, which can be a **regular path** or a **hash**.
* **request (object):** Sends a GET request or query string to the dispatcher. This will be transformed into an instance of [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams). When executed in a browser, the query string will also be appended to the URL in the address field.

#### Make get request

```javascript
// URI hash (fragment with hashtag) navigation
dispatcher.navigateTo("#articles/824/hello-world", { test: "A get request" });

// URI path navigation
// dispatcher.navigateTo("/articles/824/hello-world", { test: "A get request" });
```

#### The navigation result

The above navigation will trigger the result for the matching router:

```javascript
// GET: example.se/?test=A+get+request#articles/824/hello-world
router.get('/articles/{id:[0-9]+}/{slug:[^/]+}', function(vars, request, path) {
    const id = vars.id.pop();
    const slug = vars.slug.pop();
    const test = request.get.get("test"); // Get the query string/get request "test"
    console.log(`Article ID: ${id}, Slug: ${slug} and GET Request ${test}.`);
});
```

### POST Request

Creating a POST request is similarly efficient, targeting a `post` router. The request parameter will be converted into an instance of FormData to facilitate the request.

#### Arguments

* **path (string):** Defines the URI, which can be a **regular path** or a **hash**.
* **request (object):** Submits a POST request to the dispatcher. This will be processed into an instance of [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData), allowing for detailed and structured data transmission.

#### Make post request

```javascript
dispatcher.postTo("#post/contact", { firstname: "John", lastname: "Doe" });
```

#### The post request result

The above post will trigger the result for the matching router:

```javascript
// POST: example.se/#post/contact
router.post('/post/contact', function(vars, request, path) {
    const firstname = request.post.get("firstname");
    const lastname = request.post.get("lastname");
    console.log(`The post request, first name: ${firstname}, last name: ${lastname}`);
});
```
