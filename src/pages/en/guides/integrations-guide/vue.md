---
# NOTE: This file is auto-generated from 'scripts/generate-integration-pages.ts'
#       and pulls content directly from the package’s README.
#       DO NOT MAKE EDITS TO THIS FILE DIRECTLY, THEY WILL BE OVERWRITTEN!
#       For corrections, please edit the package README at
#       https://github.com/withastro/astro/tree/main/packages/integrations/vue/
#
# TRANSLATORS: please remove this note and the <DontEditWarning/> component.

layout: ~/layouts/IntegrationLayout.astro
title: '@astrojs/vue'
githubURL: 'https://github.com/withastro/astro/tree/main/packages/integrations/vue/'
hasREADME: true
category: renderer
i18nReady: false
setup: |
  import Video from '~/components/Video.astro';
  import DontEditWarning from '../../../../components/DontEditWarning.astro';
---

<DontEditWarning/>

This **[Astro integration][astro-integration]** enables server-side rendering and client-side hydration for your [Vue 3](https://vuejs.org/) components.

## Installation

There are two ways to add integrations to your project. Let's try the most convenient option first!

### `astro add` command

Astro includes a CLI tool for adding first party integrations: `astro add`. This command will:

1.  (Optionally) Install all necessary dependencies and peer dependencies
2.  (Also optionally) Update your `astro.config.*` file to apply this integration

To install `@astrojs/vue`, run the following from your project directory and follow the prompts:

```sh
# Using NPM
npx astro add vue
# Using Yarn
yarn astro add vue
# Using PNPM
pnpm astro add vue
```

If you run into any issues, [feel free to report them to us on GitHub](https://github.com/withastro/astro/issues) and try the manual installation steps below.

### Install dependencies manually

First, install the `@astrojs/vue` integration like so:

```sh
npm install @astrojs/vue
```

Most package managers will install associated peer dependencies as well. Still, if you see a "Cannot find package 'vue'" (or similar) warning when you start up Astro, you'll need to install Vue:

```sh
npm install vue
```

Now, apply this integration to your `astro.config.*` file using the `integrations` property:

**`astro.config.mjs`**

```js
import vue from '@astrojs/vue';

export default {
  // ...
  integrations: [vue()],
}
```

## Getting started

To use your first Vue component in Astro, head to our [UI framework documentation][astro-ui-frameworks]. You'll explore:

*   📦 how framework components are loaded,
*   💧 client-side hydration options, and
*   🤝 opportunities to mix and nest frameworks together

## Troubleshooting

For help, check out the `#support` channel on [Discord](https://astro.build/chat). Our friendly Support Squad members are here to help!

You can also check our [Astro Integration Documentation][astro-integration] for more on integrations.

## Contributing

This package is maintained by Astro's Core team. You're welcome to submit an issue or PR!

[astro-integration]: /en/guides/integrations-guide/

[astro-ui-frameworks]: /en/core-concepts/framework-components/

## Options

This integration is powered by `@vitejs/plugin-vue`. To customize the Vue compiler, options can be provided to the integration. See the `@vitejs/plugin-vue` [docs](https://github.com/vitejs/vite/tree/main/packages/plugin-vue) for more details.

**`astro.config.mjs`**

```js
import vue from '@astrojs/vue';

export default {
  // ...
  integrations: [vue({
    template: {
      compilerOptions: {
        // treat any tag that starts with ion- as custom elements
        isCustomElement: tag => tag.startsWith('ion-')
      }
    }
    // ...
  })],
}
```

### appEntrypoint

You can extend the Vue `app` instance setting the `appEntrypoint` option to a root-relative import specifier (for example, `appEntrypoint: "/src/pages/_app"`).

The default export of this file should be a function that accepts a Vue `App` instance prior to rendering, allowing the use of [custom Vue plugins](https://vuejs.org/guide/reusability/plugins.html), `app.use`, and other customizations for advanced use cases.

**`astro.config.mjs`**

```js
import { defineConfig } from 'astro/config';
import vue from '@astrojs/vue';

export default defineConfig({
  integrations: [
    vue({ appEntrypoint: '/src/pages/_app' })
  ],
});
```

**`src/pages/_app.ts`**

```js
import type { App } from 'vue';
import i18nPlugin from 'my-vue-i18n-plugin';

export default (app: App) => {
  app.use(i18nPlugin);
}
```

### jsx

You can use Vue JSX by setting `jsx: true`.

**`astro.config.mjs`**

```js
import { defineConfig } from 'astro/config';
import vue from '@astrojs/vue';

export default defineConfig({
  integrations: [
    vue({ jsx: true })
  ],
});
```

This will enable rendering for both Vue and Vue JSX components. To customize the Vue JSX compiler, pass an options object instead of a boolean. See the `@vitejs/plugin-vue-jsx` [docs](https://github.com/vitejs/vite/tree/main/packages/plugin-vue-jsx) for more details.

**`astro.config.mjs`**

```js
import { defineConfig } from 'astro/config';
import vue from '@astrojs/vue';

export default defineConfig({
  integrations: [
    vue({
      jsx: {
        // treat any tag that starts with ion- as custom elements
        isCustomElement: tag => tag.startsWith('ion-')
      }
    })
  ],
});
```
