## React project setup with Vite for faster development | Blazing fast server startup and updates

## Situation
We are working on a large project using React with many routes, and we have a deadline.

CRA takes too much time to create a new project in an average specs device. We want our new project and development server ready in seconds.

We are starting to build more and more ambitious applications. The amount of JavaScript we are dealing with also increased exponentially. It is common for large-scale projects to contain thousands of modules. 

We are starting to hit a performance bottleneck for JavaScript-based tooling. It can often take an unreasonably long wait (sometimes up to minutes!) to spin up a dev server. And even file edits can take a couple of seconds to be reflected in the browser.

*Let's see what Vite can do for us.*

---

## Some of the features of Vite.

### NPM Dependency Resolving and Pre-Bundling
```js
import { someMethod } from 'my-dep'
```
Vite will detect such bare module imports in all served source files and perform the following:

- Pre-bundle them to improve page loading speed and convert CommonJS / UMD modules to ESM. The pre-bundling step is performed with esbuild and makes Vite's cold start time significantly faster than any JavaScript-based bundler.
- Rewrite the imports to valid URLs like `/node_modules/.vite/my-dep.js?v=f3sf2ebd` so that the browser can import them properly.
- Vite caches dependency requests via HTTP headers.

### Hot Module Replacement
Frameworks with **HMR** (Hot Module Replacement) capabilities can leverage the API to provide instant, precise updates without reloading the page or blowing away application state.

### TypeScript
Vite supports importing `.ts` files out of the box. However, Some configuration fields under `compilerOptions` in `tsconfig.json` require special attention. [Checkout more](https://vitejs.dev/guide/features.html#typescript-compiler-options) if you use TypeScript.

Vite uses `esbuild` to transpile TypeScript into JavaScript which is about 20~30x faster than vanilla `tsc`, and HMR updates can reflect in the browser in under 50ms.

### JSX
`.jsx` and `.tsx` files are also supported out of the box.

### CSS
Importing `.css` files will inject its content to the page via a `<style>` tag with HMR support. You can also retrieve the processed CSS as a string as the module's default export.

### CSS Modules
Any CSS file ending with `.module.css` is considered a CSS modules file. Importing such a file will return the corresponding module object:
```css
/* example.module.css */
.red {
  color: red;
}
```

```js
import classes from './example.module.css';

function App() {
  return <p className={classes.red}>Hello world</p>
}
```

CSS modules behavior can be configured via the `css.modules` option.

If `css.modules.localsConvention` is set to `camelCaseOnly`, you can also use named imports:

```js
/* vite.config.js */

import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  css: {
    modules: {
      localsConvention: 'camelCaseOnly',
    },
  },
});
```

```js
import { red } from './example.module.css';

function App() {
  return <p className={red}>Hello world</p>
}
```

### CSS Pre-processors
Good news for developers if you are a **Sass** user like me. Vite also provide built-in support for `.scss`, `.sass`, `.less`, `.styl` and `.stylus` files. There is no need to install Vite-specific plugins for them, but the corresponding pre-processor itself must be installed:
```sh
# .scss and .sass
npm install sass --save-dev

# .less
npm install less --save-dev

# .styl and .stylus
npm install stylus --save-dev
```

You can also use CSS modules combined with pre-processors by prepending `.module` to the file extension, for example `style.module.scss`.

### Static Assets
Importing a static asset will return the resolved public URL when it is served:

```js
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl
```

Special queries can modify how assets are loaded:

```js
// Explicitly load assets as URL
import assetAsURL from './asset.js?url'

// Load assets as strings
import assetAsString from './shader.glsl?raw'

// Load Web Workers
import Worker from './worker.js?worker'

// Web Workers inlined as base64 strings at build time
import InlineWorker from './worker.js?worker&inline'
```

**Dynamic URLs**
```js
function getImageUrl(name) {
  return new URL(`./dir/${name}.png`, import.meta.url).href
}
```

`import.meta.url` is a native ESM feature that exposes the current module's URL. Combining it with the native `URL` constructor, we can obtain the full, resolved URL of a static asset using relative path from a JavaScript module.

> **NOTE:**  Does not work with SSR.

This pattern does not work if you are using Vite for Server-Side Rendering, because `import.meta.url` have different semantics in browsers vs. Node.js. The server bundle also cannot determine the client host URL ahead of time.

### JSON
JSON files can be directly imported - named imports are also supported:
```js
// import the entire object
import json from './example.json'

// import a root field as named exports - helps with treeshaking!
import { field } from './example.json'
```

### CSS Code-Splitting
Vite automatically extracts the CSS used by modules in an async chunk and generates a separate file for it. The CSS file is automatically loaded via a `<link>` tag when the associated async chunk is loaded, and the async chunk is guaranteed to only be evaluated after the CSS is loaded to avoid FOUC.

### Preload Directives Generation
Vite automatically generates `<link rel="modulepreload">` directives for entry chunks and their direct imports in the built HTML.

### Env Variables
Vite exposes env variables on the special `import.meta.env` object. Some built-in variables are available in all cases:
- `import.meta.env.MODE`: {string} the mode the app is running in.
- `import.meta.env.BASE_URL`: {string} the base url the app is being served from. This is determined by the `base` config option.
- `import.meta.env.PROD`: {boolean} whether the app is running in production.
- `import.meta.env.DEV`: {boolean} whether the app is running in development (always the opposite of `import.meta.env.PROD`)

### `.env` Files
Only variables prefixed with `VITE_` are exposed to your Vite-processed code. e.g. the following file:
```env
DB_PASSWORD=foobar
VITE_SOME_KEY=123

```

Only `VITE_SOME_KEY` will be exposed as `import.meta.env.VITE_SOME_KEY` to your client source code, but `DB_PASSWORD` will not.

If you want to customize env variables prefix, see [`envPrefix`](https://vitejs.dev/config/index.html#envprefix) option.


❤️❤️ *These were the important features of Vite. *

## Now let's create our React project with Vite.

- Run `npm init vite@latest` and enter you project name.

```bash
$ npm init vite@latest
? Project name: › vite-project
```

- Select your framework supported by Vite.

```bash
✔ Project name: … vite-project
? Select a framework: › - Use arrow-keys. Return to submit.
❯   vanilla
    vue
    react
    preact
    lit
    svelte
```

- Select your variant.

```bash
✔ Project name: … vite-project
✔ Select a framework: › react
? Select a variant: › - Use arrow-keys. Return to submit.
❯   react
    react-ts
```

```bash
✔ Project name: … vite-project
✔ Select a framework: › react
✔ Select a variant: › react

Scaffolding project in /home/raman/Documents/vite-project...

Done. Now run:

  cd vite-project
  npm install
  npm run dev
```

- By now your project should be ready. Now change your current directory with `cd vite-project` and just install dependencies with `npm install`.

- After installing dependencies, spin up your dev server with `npm run dev`.

> NOTE: `npm start` doesn't work with Vite.

```
vite v2.5.10 dev server running at:

> Local: http://localhost:3000/
> Network: use `--host` to expose

ready in 1049ms.
```

If you don't believe this message, try on your own 😂😂.
I was blown away myself when I saw this message.

### Deploying a static site

Command for creating static files for production is `npm run build`. But Vite creates our static files inside `/dist` folder instead of `/build`.

So if you use [Netlify](https://www.netlify.com/) for deploying your React apps like me, Remember to setup:
- **Build Command**: `npm run build`
- **Publish directory**: `dist`

#### Checkout more for deploying with other hosting services:
- [GitHub Pages](https://vitejs.dev/guide/static-deploy.html#github-pages)
- [GitHub Pages and Travis CI](https://vitejs.dev/guide/static-deploy.html#github-pages-and-travis-ci)
- [GitLab Pages and GitLab CI](https://vitejs.dev/guide/static-deploy.html#gitlab-pages-and-gitlab-ci)
- [Google Firebase](https://vitejs.dev/guide/static-deploy.html#google-firebase)
- [Surge](https://vitejs.dev/guide/static-deploy.html#surge)
- [Heroku](https://vitejs.dev/guide/static-deploy.html#heroku)
- [Vercel](https://vitejs.dev/guide/static-deploy.html#vercel)
- [Azure Static Web Apps](https://vitejs.dev/guide/static-deploy.html#azure-static-web-apps)

---

So, that's it on this topic. Hope you found this tool helpful.

Let me know if I missed something.

Thank you for reading this blog. ❤️❤️