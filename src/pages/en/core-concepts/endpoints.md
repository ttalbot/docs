---
layout: ~/layouts/MainLayout.astro
title: Endpoints
description: Learn how to create endpoints that serve any kind of data
i18nReady: true
---
Astro lets you create custom endpoints to serve any kind of data. You can use this to generate images, expose an RSS document, or use them as API Routes to build a full API for your site.

In statically-generated sites, your custom endpoints are called at build time to produce static files. If you opt in to [SSR](/en/guides/server-side-rendering/) mode, custom endpoints turn into live server endpoints that are called on request. Static and SSR endpoints are defined similarly, but SSR endpoints support additional features.

## Static File Endpoints
To create a custom endpoint, add a `.js` or `.ts` file to the `/pages` directory. The `.js` or `.ts` extension will be removed during the build process, so the name of the file should include the extension of the data you want to create. For example, `src/pages/data.json.ts` will build a `/data.json` endpoint.

Endpoints export a `get` function (optionally `async`) that receives a [context object](/en/reference/api-reference/#endpoint-context) with properties similar to the `Astro` global. It returns an object with a `body`, and Astro will call this at build time and use the contents of the body to generate the file.

```js
// Example: src/pages/builtwith.json.ts
// Outputs: /builtwith.json
export async function get({params, request}) {
  return {
    body: JSON.stringify({
      name: 'Astro',
      url: 'https://astro.build/',
    }),
  };
}
```

The return object can also have an `encoding` property. It can be any valid [`BufferEncoding`](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/bdd02508ddb5eebcf701fdb8ffd6e84eabf47885/types/node/buffer.d.ts#L169) accepted by Node.js' `fs.writeFile` method. For example, to produce a binary png image:

```ts title="src/pages/astro-logo.png.ts" {6}
export async function get({ params, request }) => {
  const response = await fetch("https://astro.build/assets/press/full-logo-light.png");
  const buffer = Buffer.from(await response.arrayBuffer());
  return {
    body: buffer,
    encoding: 'binary',
  };
```

You can also type your endpoint functions using the `APIRoute` type:

```js
import type { APIRoute } from 'astro';

export const get: APIRoute = async function get ({params, request}) {
...
```


### `params` and Dynamic routing

Endpoints support the same [dynamic routing](/en/core-concepts/routing/#dynamic-routes) features that pages do. Name your file with a bracketed parameter name and export a [`getStaticPaths()` function](/en/reference/api-reference/#getstaticpaths). Then, you can access the parameter using the `params` property passed to the endpoint function:

```ts title="src/pages/[id].json.ts"
import type { APIRoute } from 'astro';

const usernames = ["Sarah", "Chris", "Dan"]

export const get: APIRoute = ({ params, request }) => {
  const id = params.id;
  return {
    body: JSON.stringify({
      name: usernames[id]
    })
  }
};

export function getStaticPaths () {
  return [ 
    { params: { id: "0"} },
    { params: { id: "1"} },
    { params: { id: "2"} },
  ]
}
```

This will generate three JSON endpoints at build time: `/api/1.json`, `/api/2.json`, `/api/3.json`. Dynamic routing with endpoints works the same as it does with pages, but because the endpoint is a function and not a component, [props](/en/reference/api-reference/#data-passing-with-props) aren't supported.

### `request`
All endpoints receive a `request` property, but in static mode, you only have access to `request.url`. This returns the full URL of the current endpoint and works the same as [Astro.request.url](/en/reference/api-reference/#astrorequest) does for pages.

```ts title="src/pages/request-path.json.ts"
import type { APIRoute } from 'astro';

export const get: APIRoute = ({ params, request }) => {
  return {
    body: JSON.stringify({
      path: new URL(request.url).pathname
    })
  };
}
```

## Server Endpoints (API Routes)
Everything described in the static file endpoints section can also be used in SSR mode: files can export a `get` function which receives a [context object](/en/reference/api-reference/#endpoint-context) with properties similar to the `Astro` global.

But, unlike in `static` mode, when you configure `server` mode, the endpoints will be built when they are requested. This unlocks new features that are unavailable at build time, and allows you to build API routes that listen for requests and securely execute code on the server at runtime.

:::note
Be sure to [enable SSR](/en/guides/server-side-rendering/#enabling-ssr-in-your-project) before trying these examples.
:::

Server endpoints can access `params` without exporting `getStaticPaths`, and they can return a [`Response`](https://developer.mozilla.org/en-US/docs/Web/API/Response) object, allowing you to set status codes and headers:

```js title="src/pages/[id].json.js"
import { getProduct } from '../db';

export async function get({ params }) {
  const id = params.id;
  const product = await getProduct(id);

  if (!product) {
    return new Response(null, {
      status: 404,
      statusText: 'Not found'
    });
  }

  return new Response(JSON.stringify(product), {
    status: 200,
    headers: {
      "Content-Type": "application/json"
    }
  });
}
```
This will respond to any request that matches the dynamic route. For example, if we navigate to `/helmet.json`, `params.id` will be set to `helmet`. If `helmet` exists in the mock product database, the endpoint will use create a `Response` object to respond with JSON and return a successful [HTTP status code](https://developer.mozilla.org/en-US/docs/Web/API/Response/status). If not, it will use a `Response` object to respond with a `404`.


### HTTP methods
In addition to the `get` function, you can export a function with the name of any [HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods). When a request comes in, Astro will check the method and call the corresponding function. 

You can also export an `all` function to match any method that doesn't have a corresponding exported function. If there is a request with no matching method, it will redirect to your site's [404 page](/en/core-concepts/astro-pages/#custom-404-error-page).

:::note
Since `delete` is a reserved word in JavaScript, export a `del` function to match the delete method.
:::

```ts title="src/pages/methods.json.ts"
export const get: APIRoute = ({ params, request }) => {
  return {
    body: JSON.stringify({
      message: "This was a GET!"
    })
  }
};

export const post: APIRoute = ({ request }) => {
  return {
    body: JSON.stringify({
      message: "This was a POST!"
    })
  }
}

export const del: APIRoute = ({ request }) => {
  return {
    body: JSON.stringify({
      message: "This was a DELETE!"
    })
  }
}

export const all: APIRoute = ({ request }) => {
  return {
    body: JSON.stringify({
      message: `This was a ${request.method}!`
    })
  }
}
```

### `request` 
In SSR mode, the `request` property returns a fully usable [`Request`](https://developer.mozilla.org/en-US/docs/Web/API/Request) object that refers to the current request. This allows you to accept data and check headers:

```ts title="src/pages/test-post.json.ts"
export const post: APIRoute = async ({ request }) => {
  if (request.headers.get("Content-Type") === "application/json") {
    const body = await request.json();
    const name = body.name;
    return new Response(JSON.stringify({
      message: "Your name was: " + name
    }), {
      status: 200
    })
  }
  return new Response(null, { status: 400 });
}
```

### Redirects
The endpoint context exports a `redirect()` utility similar to `Astro.redirect`:

```js title="src/pages/links/[id].js" {14}
import { getLinkUrl } from '../db';

export async function get({ params, redirect }) {
  const { id } = params;
  const link = await getLinkUrl(id);

  if (!link) {
    return new Response(null, {
      status: 404,
      statusText: 'Not found'
    });
  }

  return redirect(link, 307);
}
```

### Example: Verifying a captcha
Server endpoints can be used as REST API endpoints to run functions such as authentications, database access, and verifications without exposing sensitive data to the client.

In the example below, an API route is used to verify Google reCAPTCHA v3 without exposing the secret to clients.

On the server, we define a post method that accepts recaptcha data, then verifies it with reCAPTCHA's API. Here, we can safely define secret values or read environment variables.

```js title="src/pages/recaptcha.js"
export async function post({ request }) {
  const data = await request.json();

  const recaptchaURL = 'https://www.google.com/recaptcha/api/siteverify';
  const requestBody = {
    secret: "YOUR_SITE_SECRET_KEY",   // This can be an environment variable
    response: data.recaptcha          // The token passed in from the client
  };

  const response = await fetch(recaptchaURL, {
    method: "POST",
    body: JSON.stringify(requestBody)
  });

  const responseData = await response.json();

  return new Response(JSON.stringify(responseData), { status: 200 });
}
```

Then, we access our endpoint using `fetch` from a client script:

```astro title="src/pages/index.astro"
<html>
  <head>
    <script src="https://www.google.com/recaptcha/api.js"></script>
  </head>

  <body>
    <button class="g-recaptcha" 
      data-sitekey="PUBLIC_SITE_KEY" 
      data-callback="onSubmit" 
      data-action="submit"> Click me to verify the captcha challenge! </button>

    <script is:inline>
      function onSubmit(token) {
        fetch("/recaptcha", {
          method: "POST",
          body: JSON.stringify({ recaptcha: token })
        })
        .then((response) => response.json())
        .then((gResponse) => {
          if (gResponse.success) {
            // Captcha verification was a success
          } else {
            // Captcha verification failed
          }
        })
      }
    </script>
  </body>
</html>
```
