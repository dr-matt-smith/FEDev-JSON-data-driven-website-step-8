Back to FEDev main <<<< https://github.com/dr-matt-smith/2026-FDEev-Front-End-Dev-start-here

JSON-driven website: 
[Step 1](https://github.com/dr-matt-smith/FEDev-JSON-data-driven-website-step-1)
|
[Step 2](https://github.com/dr-matt-smith/FEDev-JSON-data-driven-website-step-2)
|
[Step 3](https://github.com/dr-matt-smith/FEDev-JSON-data-driven-website-step-3)
|
[Step 4](https://github.com/dr-matt-smith/FEDev-JSON-data-driven-website-step-4)
|
[Step 5](https://github.com/dr-matt-smith/FEDev-JSON-data-driven-website-step-5)
|
[Step 6](https://github.com/dr-matt-smith/FEDev-JSON-data-driven-website-step-6)
|
[Step 7](https://github.com/dr-matt-smith/FEDev-JSON-data-driven-website-step-7)
|
Step 8


# FEDev-JSON-data-driven-website-step-8


NOTE:
NOTE: for this step to work, you must be running the API JSON server project at port 3000
NOTE: See https://github.com/dr-matt-smith/FEDDev---express-JSON-data-API-server
NOTE:

## 1: Update JavaScript to get list of modules from JSON API server
Let's refactor our modules list JavaScript data loading page, to read from JSON API rather than a hard-coded JSON file on disk.

We'll be making changes to `/routes/modules/+page.server.js`.


Since we are running our Node Express JSON API server at port `3000`, and we have an API endpoint to get a JSON array of modules at `/modules`, we declare a JavaScript variable for this endpoint URL:
```javascript
const endpoint = "http://localhost:3000/modules";
```

We need to update our `load()` function to be `asynch` (asychronour - message send, and we await a reply...), so we delcare the function like this:
```javascript
export const load = async () => {
    ... body of function goes here ...
};
```

The steps are to create a `reponse` from a `fetch()` request. Then to extract the JSON array from the reponse, into a variable `modules`
```javascript
    const response = await fetch(endpoint);
    const modules = await response.json();
```

We then return this `modules` JSON array as the data for the Svelte script.

So the full listing for our updated `/routes/modules/+page.server.js` script is as follows: 
```javascript
const endpoint = "http://localhost:3000/modules";

export const load = async () => {
    const response = await fetch(endpoint);
    const modules = await response.json();

    return { modules };
};
```

## Updating the JavaScript to retrieve details for a single module given the ID
In a similar fashion, we can get the ID from the route Request route parameters and use this to request the JSON for a single module frmo our API.

So we need to update our script in `/routes/modules/[modulecode]/+page.server.js`.

As above, we'll define our endpoint string. However, we'll first get the module code from the request parameters, and append this to the end of our API endpoint, to we'll be requesting just the JSON for a single module:

```javascript
import { error } from '@sveltejs/kit';
const endpoint = "http://localhost:3000/modules";

export const load = async ({ params }) => {
    let moduleCode = params.modulecode;

    // add module code ID to end of API GET URL
    const response = await fetch(endpoint + "/" + moduleCode);
    
    ... more code goes here ...
}
```

We then store the JSON from the response into variable `module`, using a singular variable name since it's a single object.

NOTE: If no data is returned, we'll still get an HTTP Response, so `module` will always contain some text content.

So to test whether a valid module has been received, we'll interogate for a `title` property of the `module` variable:

```javascript
    const module = await response.json();

    // check if we got a module object back or not
    const title = module.title;

    // if module object undefined, we didn't find one,
    // so generate a 404 NOT FOUND redirect
    if (!title) error(404);
```

If no error, we cab then return the module object as data for the Svelte page:

So our final full listing for page `/routes/modules/[modulecode]/+page.server.js` is:

```javascript
import { error } from '@sveltejs/kit';
const endpoint = "http://localhost:3000/modules";

export const load = async ({ params }) => {
    let moduleCode = params.modulecode;

    // add module code ID to end of API GET URL
    const response = await fetch(endpoint + "/" + moduleCode);
    const module = await response.json();

    // check if we got a module object back or not
    const title = module.title;

    // if module object undefined, we didn't find one,
    // so generate a 404 NOT FOUND redirect
    if (!title) error(404);

    return {
        module
    };
}
```
