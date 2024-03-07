# Form submission

Stratox Pilot supports automatic form submission handling through routers, a feature that must be explicitly enabled in the Dispatcher's configuration.

### 1. Enable Form Submission

To allow automatic catching and routing of form submissions, enable the `catchForms` option in the Dispatcher configuration:

```javascript
const dispatcher = new Dispatcher({
    catchForms: true
});
```

### 2. Define Routes

Next, define the routes that will handle form submissions. For example, to handle a POST request:

```javascript
// POST: example.se/#post/contact
router.post('/post/contact', function(vars, request, path) {
    console.log('Contact form posted with form request:', request.post);
});
```

### 3. Implement Form Submission

Forms can use both GET and POST methods. Below is an example of a form designed to submit via POST:

```html
<form action="#post/contact" method="post">
    <div>
        <label>First name</label>
        <input type="text" name="firstname" value="">
    </div>
    <div>
        <label>Last name</label>
        <input type="text" name="lastname" value="">
    </div>
    <div>
        <label>E-mail</label>
        <input type="email" name="email" value="">
    </div>
    <input type="submit" name="submit" value="Send">
</form>
```

With these settings, the dispatcher will automatically capture and route submissions to the corresponding handler if a matching route is found.

## Have any questions

If there's anything unclear or you have further questions, feel free to reach out via email at daniel.ronkainen@wazabii.se.
