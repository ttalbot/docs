---
layout: ~/layouts/MainLayout.astro
title: Estructura de proyecto
description: Aprende cómo estructurar un proyecto en Astro
i18nReady: true
---

Tu nuevo proyecto Astro generado a partir del asistente de instalación `create-astro` incluye algunos archivos y carpetas por defecto. Otros, los crearás tú mismo y los agregarás a la estructura de archivos existente.

Así es como se organiza un proyecto de Astro y algunos archivos que encontrarás en tu nuevo proyecto.
## Carpetas y archivos

Astro propone una estructura de carpetas opinionada para tu proyecto. La raíz de tu proyecto deberá incluir los siguientes archivos y carpetas:

- `src/*` - El código fuente de tu proyecto (componentes, páginas, estilos, etc.)
- `public/*` - Archivos sin código que no serán procesados (fuentes, íconos, etc.)
- `package.json` - El manifiesto de tu proyecto
- `astro.config.mjs` - El archivo de configuración de Astro (recomendado)
- `tsconfig.json` - El archivo de configuración de Typescript (recomendado)

### Ejemplo de árbol de proyecto

Un proyecto de Astro común debería verse así:

```
├── src/
│   ├── components/
│   │   ├── Header.astro
│   │   └-─ Button.jsx
│   ├── layouts/
│   │   └-─ PostLayout.astro
│   └── pages/
│   │   ├── posts/
│   │   │   ├── post1.md
│   │   │   ├── post2.md
│   │   │   └── post3.md
│   │   └── index.astro
│   └── styles/
│       └-─ global.css
├── public/
│   ├── robots.txt
│   ├── favicon.svg
│   └-─ social-image.png
├── astro.config.mjs
├── package.json
└── tsconfig.json

```

### `src/`

La carpeta `src/` es donde se encuentra el código fuente de tu proyecto. Esto incluye:

- [Páginas](/es/core-concepts/astro-pages/)
- [Layout](/es/core-concepts/layouts/)
- [Componentes de Astro](/es/core-concepts/astro-components/)
- [Componentes de framework (React, etc.)](/es/core-concepts/framework-components/)
- [Estilos (CSS, Sass)](/es/guides/styling/)
- [Markdown](/es/guides/markdown-content/)

Astro procesa, optimiza y empaqueta los archivos en `src/` para crear la website final que será desplegada al navegador. A diferencia de la carpeta estática `public/`, los archivos en `src/` serán procesados por Astro.

Algunos archivos (como los componentes de Astro) no serán enviados al navegador como fueron escritos, sino que serán renderizados a HTML estático. Otros archivos (como CSS) serán enviados directamente al navegador, pero antes serán optimizados o empaquetados con otros archivos para un mejor rendimiento.

### `src/components`

Los **componentes** son unidades reutilizables de código para tus páginas HTML. Estos componentes pueden ser [componentes de Astro](/es/core-concepts/astro-components/) o [componentes de framework](/es/core-concepts/framework-components/) como React o Vue. Es común agrupar y organizar todos tus componentes en una sola carpeta.

Esta es la convención común en proyectos de Astro, pero no es necesaria. ¡Siéntete libre de organizar tus componentes a gusto!

### `src/layouts`

[Plantillas](/es/core-concepts/layouts/) son componentes especiales que envuelven el contenido en una página. Estas son comúnmente utilizadas por [páginas de Astro](/es/core-concepts/astro-pages/) y [páginas Markdown o MDX](/es/guides/markdown-content/) para definir una plantilla común entre todas tus páginas.

Así como `src/components`, esta carpeta es una convención común pero no es necesaria.

### `src/pages`

[Páginas](/es/core-concepts/astro-pages/) son componentes especiales usados para crear páginas en tu proyecto. Una página puede ser un componente de Astro o un archivo Markdown.

:::caution
¡`src/pages` es una carpeta **necesaria** en tu proyecto de Astro! ¡Sin ella, tu proyecto no tendrá páginas o rutas!
:::

### `src/styles`

Es una convención común para guardar todos tus archivos CSS o Sass en una sola carpeta `src/styles` pero no es necesaria. Siempre y cuando tus estilos se encuentren dentro de la carpeta `src/` y sean importados correctamente, Astro se encargará de optimizarlos.

### `public/`

La carpeta `public/` es para archivos que no necesiten ser procesados durante la compilación final de tu proyecto. Estos archivos serán copiados dentro de la carpeta build sin ser modificados.


Este comportamiento hace que `public/` sea ideal para activos comunes como imágenes y fuentes, o archivos especiales como `robots.txt` y `manifest.webmanifest`.

Puedes colocar CSS y JavaScript en tu carpeta `public/`, pero ten en cuenta que esos archivos no se empaquetarán ni optimizarán en la compilación final.

:::tip
Como regla general, cualquier archivo CSS o JavaScript que escribas debe estar en tu carpeta `src/`.
:::

### `package.json`

Es un archivo utilizado por los gestores de paquetes de JavaScript para administrar tus dependencias. También define los scripts que se usan comúnmente para ejecutar Astro (ex: `npm start`, `npm run build`).

Hay [dos tipos de dependencias](https://docs.npmjs.com/specifying-dependencies-and-devdependencies-in-a-package-json-file) que puedes especificar en `package.json`: `dependencies` (dependencias) y `devDependencies` (dependencias de desarrollo). En la mayoría de los casos, estas funcionan de la misma manera: Astro necesita todas las dependencias al hacer *build*, y tu gestor de paquetes instalará ambos. Recomendamos poner todas tus dependencias en `dependencies` para comenzar, y solo usar `devDependencies` si encuentras la necesidad de hacerlo.

Para obtener ayuda sobre crear un nuevo archivo `package.json` para tu proyecto, consulta las instrucciones en [instalación manual](/es/install/manual/).

### `astro.config.mjs`

Este archivo es generado al crear tu proyecto en Astro e incluye la configuración de tu proyecto. Aquí puedes especificar las integraciones que desees utilizar, las opciones de compilación final, la configuración del servidor, y más. 

Lee la [Guía de Configuración de Astro](/es/guides/configuring-astro/) para obtener detalles sobre las opciones de configuración.

### `tsconfig.json`

Este archivo es generado en cada plantilla de inicio de Astro e incluye la configuración de las opciones de Typescript para tu proyecto de Astro. Algunas características (como importaciones de paquetes de npm) no están soportadas completamente en tu editor sin un archivo `tsconfig.json`. 

Lee la [Guía de Typescript](/es/guides/typescript/) para obtener detalles sobre las opciones de configuración.