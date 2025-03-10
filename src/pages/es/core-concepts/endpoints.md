---
layout: ~/layouts/MainLayout.astro
title: Endpoints
description: Aprende cómo crear endpoints que sirven cualquier tipo de dato
i18nReady: true
---
Astro te permite crear endpoints personalizados para servir cualquier tipo de dato. Puedes usar esto para generar imágenes, exponer un documento RSS o usarlo como rutas API para construir una API completa para tu proyecto.

En los sitios generados estáticamente, tus endpoints personalizados son llamados al momento de compilación para producir archivos estáticos. Si optaste para modo [SSR](/es/guides/server-side-rendering/), los endpoints personalizados se convierten en endpoints de servidor que son llamados a petición. Endpoints estáticos y de tipo SSR son definidos similarmente, pero endpoints SSR soportan características adicionales.

## Endpoints de Archivos Estáticos
Para crear un endpoint personalizado, agrega un archivo `.js` o `.ts` al directorio de `/pages`. La extensión `.js` o `.ts` se eliminará durante el proceso de compilación, por lo que el nombre del archivo debe incluir la extensión de los datos que se desea generar. Por ejemplo, `src/pages/data.json.ts` generará el endpoint `/data.json`.

Los endpoints exportan una función `get` (opcionalmente `async`) que recibe un objeto con dos propiedades (`params` y `request`) como su único argumento. Esto retorna un objeto con un `body`, y Astro va a llamarlo al momento de compilación y usar sus contenidos del body para generar un archivo.

```js
// Ejemplo: src/pages/builtwith.json.ts
// Salidas: /builtwith.json
export async function get({params, request}) {
  return {
    body: JSON.stringify({
      nombre: 'Astro',
      url: 'https://astro.build/es',
    }),
  };
};
```

El objeto retornado puede también contener una propiedad de `encoding`. Puede ser cualquier [`BufferEncoding`](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/bdd02508ddb5eebcf701fdb8ffd6e84eabf47885/types/node/buffer.d.ts#L169) válido que sea aceptado por el método `fs.writeFile` de Node.js. Por ejemplo, para producir una imagen PNG binaria:

```ts title="src/pages/astro-logo.png.ts" {6}
export async function get({ params, request }) => {
  const response = await fetch("https://astro.build/assets/press/full-logo-light.png");
  const buffer = Buffer.from(await response.arrayBuffer());
  return {
    body: buffer,
    encoding: 'binary',
  };
};
```

También puedes escribir tus funciones de endpoint usando el tipo de `APIRoute`:

```js
import type { APIRoute } from 'astro';

export const get: APIRoute = async function get ({params, request}) {
...
```

### `params` y Enrutamiento Dinámico

Los endpoints soportan las mismas características que hacen las páginas de [rutas dinámicas](/es/core-concepts/routing/#rutas-dinámicas). Asigna un nombre a tu archivo con un nombre de parámetro entre corchetes y exporta una [función `getStaticPaths()`](/es/reference/api-reference/#getstaticpaths). Después, ya puedes acceder al parámetro usando la propiedad de `params` que fue pasado a la función del endpoint.

```ts title="src/pages/[id].json.ts"
import type { APIRoute } from 'astro';

const usernames = ["Sarah", "Chris", "Dan"]

export const get: APIRoute = ({ params, request }) => {
  const id = params.id;
  return {
    body: JSON.stringify({
      name: usernames[id]
    })
  };
};

export function getStaticPaths () {
    return [ 
        { params: { id: "0"} },
        { params: { id: "1"} },
        { params: { id: "2"} },
    ]
};
```

Esto te va a generar tres JSON endpoints al momento de compilación: `/api/1.json`, `/api/2.json`, `/api/3.json`. El enrutamiento dinámico con endpoints funciona igual que con las páginas, pero debido a que el endpoint es una función y no un componente, las [props](/es/reference/api-reference/#transferencia-de-datos-con-props) no son compatibles.

### `request`
Todos los endpoints reciben una propiedad `request`, pero en el modo estático, solo tienes acceso a `request.url`. Esto retorna una URL completa del endpoint actual y funciona del mismo modo que [Astro.request.url](/es/reference/api-reference/#astrorequest) funciona con las páginas.

```ts title="src/pages/request-path.json.ts"
import type { APIRoute } from 'astro';

export const get: APIRoute = ({ params, request }) => {
  return {
    body: JSON.stringify({
      path: new URL(request.url).pathname
    })
  }
};
```

## Endpoints del Servidor (Rutas de API)
Todo lo descrito en la sección de endpoints de archivos estáticos también se puede usar en modo SSR: los archivos pueden exportar una función `get` que recibe un objeto con las propiedades `params` y `request`.

Pero, a diferencia del modo `static`, cuando configuras el modo `server`, los endpoints se construirán cuando se soliciten. Esto desbloquea nuevas funciones que no están disponibles al momento de la compilación y permite crear rutas de API que escuchan solicitudes y ejecutan código de forma segura en el servidor en tiempo de ejecución.

:::note
Asegúrate de [habilitar SSR](/es/guides/server-side-rendering/#habilitando-ssr-en-su-proyecto) antes de probar estos ejemplos.
:::

Los endpoints del servidor pueden acceder a `params` sin exportar `getStaticPaths`, y pueden retornar un objeto [`Response`](https://developer.mozilla.org/es/docs/Web/API/Response), lo que te permite establecer códigos de estado y encabezados:

```js title="src/pages/[id].json.js"
import { getProduct } from '../db';

export async function get({ params }) {
  const id = params.id;
  const product = await getProduct(id);

  if (!product) {
    return new Response(null, {
      status: 404,
      statusText: 'No Encontrado'
    });
  }

  return new Response(JSON.stringify(product), {
    status: 200,
    headers: {
      "Content-Type": "application/json"
    }
  });
};
```

Esto responderá a cualquier solicitud que coincida con la ruta dinámica. Por ejemplo, si navegamos a `/helmet.json`, `params.id` se establecerá en `helmet`. Si el `helmet` existe en la base de datos del producto simulado, el endpoint usará la creación del objeto `Response` para responder con JSON y devolver un [código de estado HTTP](https://developer.mozilla.org/es/docs/Web/API/Response/status) exitoso. Si no, utilizará un objeto `Response` para responder con un `404`.

### Métodos HTTP
Además de la función `get`, puedes exportar una función con el nombre de cualquier [método HTTP](https://developer.mozilla.org/es/docs/Web/HTTP/Methods). Cuando llega una solicitud, Astro verificará el método y llamará a la función correspondiente.

También puedes exportar una función `all` para que coincida con cualquier método que no tenga una función exportada correspondiente. Si hay una solicitud sin método de coincidencia, se redirigirá a la [página 404](/es/core-concepts/astro-pages/#página-de-error-404-personalizada) de tu proyecto.

:::note
Dado que `delete` es una palabra reservada en JavaScript, exporta una función `del` para que coincida con el método de eliminación.
:::

```ts title="src/pages/methods.json.ts"
export const get: APIRoute = ({ params, request }) => {
  return {
    body: JSON.stringify({
      message: "¡Esto es un GET!"
    })
  }
};

export const post: APIRoute = ({ request }) => {
  return {
    body: JSON.stringify({
      message: "¡Esto es un POST!"
    })
  }
};

export const del: APIRoute = ({ request }) => {
  return {
    body: JSON.stringify({
      message: "¡Esto es un DELETE!"
    })
  }
};

export const all: APIRoute = ({ request }) => {
  return {
    body: JSON.stringify({
      message: `¡Esto fue un ${request.method}!`
    })
  }
};
```

### `request` 
En modo SSR, la propiedad `request` retorna un objeto [`Request`](https://developer.mozilla.org/es/docs/Web/API/Request) totalmente utilizable que hace referencia a la solicitud actual. Esto permite aceptar datos y verificar encabezados:

```ts title="src/pages/test-post.json.ts"
export const post: APIRoute = async ({ request }) => {
  if (request.headers.get("Content-Type") === "application/json") {
    const body = await request.json();
    const nombre = body.nombre;
    return new Response(JSON.stringify({
      message: "Tu nombre fue: " + nombre
    }), {
      status: 200
    })
  }
  return new Response(null, { status: 400 });
};
```

### Redirecciones
Dado que `Astro.redirect` no está disponible para las rutas API, puedes [`Response.redirect`](https://developer.mozilla.org/en-US/docs/Web/API/Response/redirect) para redirigir:

```js title="src/pages/links/[id].js" {14}
import { getLinkUrl } from '../db';

export async function get({ params }) {
  const { id } = params;
  const link = await getLinkUrl(id);

  if (!link) {
    return new Response(null, {
      status: 404,
      statusText: 'Not found'
    });
  };

  return Response.redirect(link, 307);
};
```

### Ejemplo: Verificando un Captcha
Los endpoints del servidor se pueden usar como endpoints del API REST para ejecutar funciones como autenticaciones, acceso a bases de datos y verificaciones sin exponer datos confidenciales al cliente.

En el siguiente ejemplo, se usa una ruta API para verificar el Google reCAPTCHA v3 sin exponer el secreto a los clientes.

En el servidor, definimos un método de publicación que acepta datos de recaptcha y luego los verifica con la API de reCAPTCHA. Aquí, podemos definir con seguridad valores secretos o leer variables de entorno.

```js title="src/pages/recaptcha.js"
export async function post({ request }) {
  const data = await request.json();

  const recaptchaURL = 'https://www.google.com/recaptcha/api/siteverify';
  const requestBody = {
    secret: "YOUR_SITE_SECRET_KEY",   // Esto puede ser una variable de entorno
    response: data.recaptcha          // El token que fue pasado por el cliente
  };

  const response = await fetch(recaptchaURL, {
    method: "POST",
    body: JSON.stringify(requestBody)
  });

  const responseData = await response.json();

  return new Response(JSON.stringify(responseData), { status: 200 });
}
```

Luego, accedemos a nuestro endpoint usando `fetch` desde un script de cliente:

```astro title="src/pages/index.astro"
<html>
  <head>
    <script src="https://www.google.com/recaptcha/api.js"></script>
  </head>

  <body>
    <button class="g-recaptcha" 
      data-sitekey="PUBLIC_SITE_KEY" 
      data-callback="onSubmit" 
      data-action="submit">¡Haz clic para verificar el desafío de captcha!</button>

    <script is:inline>
      function onSubmit(token) {
        fetch("/recaptcha", {
          method: "POST",
          body: JSON.stringify({ recaptcha: token })
        })
        .then((response) => response.json())
        .then((gResponse) => {
          if (gResponse.success) {
            // La verificación del captcha fue un éxito
          } else {
            // Verificación de captcha fallida
          }
        })
      }
    </script>
  </body>
</html>
```
