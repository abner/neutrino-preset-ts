# Neutrino TypeScript Preset
[![NPM version][npm-image]][npm-url] [![NPM downloads][npm-downloads]][npm-url]

`neutrino-preset-ts` is a Neutrino preset that supports building generic applications for the web using TypeScript.

## Features

- Zero upfront configuration necessary to start developing and building a web app using TypeScript
- TypeScript is a superset of JavaScript, allowing up to ES 7 features.
- Webpack loaders for importing HTML, CSS, images, icons, and fonts
- Webpack Dev Server during development
- Automatic creation of HTML pages, no templating necessary
- Hot Module Replacement support
- Tree-shaking to create smaller bundles
- Production-optimized bundles with minification and easy chunking
- Code splitting support to easily split your code into various bundles
- Easily extensible to customize your project as needed

## Requirements

- Node.js v6.9+
- Yarn or npm client
- Neutrino v5

## Installation

`neutrino-preset-ts` can be installed via the Yarn or npm clients. Inside your project, make sure
`neutrino`, `typescript`, and `neutrino-preset-ts` are development dependencies.

#### Yarn

```bash
❯ yarn add --dev neutrino typescript neutrino-preset-ts
```

#### npm

```bash
❯ npm install --save-dev neutrino typescript neutrino-preset-ts
```

## Project Layout

`neutrino-preset-ts` follows the standard [project layout](https://neutrino.js.org/project-layout) specified by Neutrino. This
means that by default all project source code should live in a directory named `src` in the root of the
project. This includes JavaScript files, CSS stylesheets, images, and any other assets that would be available
to your compiled project.

## Quickstart

After installing Neutrino and the TypeScript preset, we need to add some configuration for the TypeScript compiler. Add a
new file named `tsconfig.json` in the root of your project.

```bash
❯ touch tsconfig.json
```
This file contains some TypeScript compiler configuration, and you can read more about its options [here][tsconfig-url].
We do not want to move away from using this file because it is very handy and controls a lot of options. Edit your
`tsconfig.json` file with the following minimalist configuration:

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "sourceMap": true,
    "jsx": "react"
  },
  "exclude": [
    "node_modules"
  ],
  "compileOnSave": false
}
```

Add a new directory named `src` in the root of the project, with
a single TS file named `index.ts` in it.

```bash
❯ mkdir src && touch src/index.ts
```

This preset exposes an element in the page with an ID of `root` to which you can mount your application. Edit
your `src/index.ts` file with the following:

```js
const app = document.createElement('main');
const text = document.createTextNode('Hello world!');

app.appendChild(text);
document.getElementById('root').appendChild(app);
```

Now edit your project's package.json to add commands for starting and building the application:

```json
{
  "neutrino": {
    "use": [
      "neutrino-preset-ts"
    ]
  },
  "scripts": {
    "start": "neutrino start",
    "build": "neutrino build"
  },
}
```

Start the app, then open a browser to the address in the console:

#### Yarn

```bash
❯ yarn start
✔ Development server running on: http://localhost:5000
✔ Build completed
```

#### npm

```bash
❯ npm start
✔ Development server running on: http://localhost:5000
✔ Build completed
```

## Building

`neutrino-preset-ts` builds static assets to the `build` directory by default when running `neutrino build`. Using the
quick start example above as a reference:

```bash
❯ yarn build
clean-webpack-plugin: /build has been removed.
Build completed in 0.779s

Hash: 55c33df4cd1222a03505
Version: webpack 2.2.1
Time: 784ms
                                  Asset       Size  Chunks             Chunk Names
   index.52f2d06086f51d21f9c9.bundle.js  213 bytes    0, 1  [emitted]  index
manifest.c10c6464802bf71a2c3f.bundle.js    1.41 kB       1  [emitted]  manifest
                             index.html  779 bytes          [emitted]
✨  Done in 2.10s.
```

You can either serve or deploy the contents of this `build` directory as a static site.

## Hot Module Replacement

While `neutrino-preset-ts` supports Hot Module Replacement your app, it does require some application-specific changes
in order to operate. Your application should define split points for which to accept modules to reload using
`module.hot`:

For example:

```ts
import app from './app';

document
  .getElementById('root')
  .appendChild(app('Hello world!'));

if (module.hot) {
  module.hot.accept('./app');
}
```

Or for all paths:

```ts
import app from './app';

document
  .getElementById('root')
  .appendChild(app('Hello world!'));

if (module.hot) {
  module.hot.accept();
}
```

Using dynamic imports with `import()` will automatically create split points and hot replace those modules upon
modification during development.

## Paths

By default this preset loads assets relative to the path of your application by setting Webpack's
[`output.publicPath`](https://webpack.js.org/configuration/output/#output-publicpath) to `./`. If you wish to load
assets instead from a CDN, or if you wish to change to an absolute path for your application, customize your build to
override `output.publicPath`. See the [Customizing](#Customizing) section below.

## Customizing

To override the build configuration, start with the documentation on [customization](https://neutrino.js.org/customization).
`neutrino-preset-ts` creates some conventions to make overriding the configuration easier once you are ready to make
changes.

By default the TypeScript preset creates a single **main** `index` entry point to your application, and this maps to the
`index.ts` file in the `src` directory. This value is provided by `neutrino.options.entry`.
This means that the TypeScript preset is optimized toward the use case of single-page applications over multi-page
applications.

### Rules

The following is a list of rules and their identifiers which can be overridden:

- `sourcemap`: Allows using source maps for modules. Contains a single loader named `sourcemap`.
- `typescript`: Compiles `.ts` and `.tsx` files from the `src` directory using TypeScript. Contains a single loader named `ts`.
- `html`: Allows importing HTML files from modules. Contains a single loader named `file`.
- `style`: Allows importing CSS stylesheets from modules. Contains two loaders named `style` and `css`.
- `img`, `svg`, `ico`: Allows import image files from modules. Each contains a single loader named `url`.
- `woff`, `ttf`: Allows importing WOFF and TTF font files from modules. Each contains a single loader named `url`.
- `eot`: Allows importing EOT font files from modules. Contains a single loader named `file`.

### Plugins

The following is a list of plugins and their identifiers which can be overridden:

- `env`: Injects the value of `NODE_ENV` into the application as `process.env.NODE_ENV`.
- `html`: Creates HTML files when building. Has various options that can be configured via package.json.
- `chunk`: Defines chunks for `manifest` and `vendor` entry points. Can be configured via package.json.
- `hot`: Enables hot module reloading.
- `copy`: Copies non-JS/TS files from `src` to `build` when using `neutrino build`.
- `clean`: Clears the contents of `build` prior to creating a production bundle.

### Simple customization

By following the [customization guide](https://neutrino.js.org/customization/simple) and knowing the rule, loader, and plugin IDs above,
you can override and augment the build directly from package.json.

#### Vendoring

By defining an entry point in package.json named `vendor` you can split out external dependencies into a chunk separate
from your application code.

_Example: Put lodash into a separate "vendor" chunk:_

```json
{
  "neutrino": {
    "config": {
      "entry": {
        "vendor": [
          "lodash"
        ]
      }
    }
  },
  "dependencies": {
    "lodash": "*"
  }
}
```

#### HTML files

Under the hood `neutrino-preset-ts` uses [html-webpack-template](https://www.npmjs.com/package/html-webpack-template)
for generating HTML files. If you wish to override how these files are created, define an object in your package.json
at `neutrino.options.html` with options matching the format expected by html-webpack-template.

_Example: Change the application mount ID from "root" to "app":_

```json
{
  "neutrino": {
    "options": {
      "html": {
        "appMountId": "app"
      }
    }
  }
}
```

### Advanced configuration

By following the [customization guide](https://neutrino.js.org/customization/advanced) and knowing the rule, loader, and plugin IDs above,
you can override and augment the build by creating a JS module which overrides the config.

#### Vendoring

By defining an entry point named `vendor` you can split out external dependencies into a chunk separate
from your application code.

_Example: Put lodash into a separate "vendor" chunk:_

```js
module.exports = neutrino => {
  neutrino.config
    .entry('vendor')
    .add('lodash');
};
```

#### HTML files

_Example: Change the application mount ID from "root" to "app":_

```js
const merge = require('deepmerge');

module.exports = neutrino => {
  neutrino.options.html.appMountId = 'app';
};
```

[npm-image]: https://img.shields.io/npm/v/neutrino-preset-ts.svg
[npm-downloads]: https://img.shields.io/npm/dt/neutrino-preset-ts.svg
[npm-url]: https://npmjs.org/package/neutrino-preset-ts
[tsconfig-url]: https://www.typescriptlang.org/docs/handbook/tsconfig-json.html
