# jest-webpack-alias

[![Build Status](https://travis-ci.org/nhz-io/jest-webpack-alias.svg?branch=travis-coveralls)](https://travis-ci.org/nhz-io/jest-webpack-alias)
[![Coverage Status](https://coveralls.io/repos/github/nhz-io/jest-webpack-alias/badge.svg?branch=travis-coveralls)](https://coveralls.io/github/nhz-io/jest-webpack-alias?branch=travis-coveralls)
[![NPM](https://img.shields.io/npm/v/jest-webpack-alias.svg?style=flat)](https://www.npmjs.com/package/jest-webpack-alias)
[![bitHound Overall Score](https://www.bithound.io/github/nhz-io/jest-webpack-alias/badges/score.svg)](https://www.bithound.io/github/nhz-io/jest-webpack-alias)
[![bitHound Dependencies](https://www.bithound.io/github/nhz-io/jest-webpack-alias/badges/dependencies.svg)](https://www.bithound.io/github/nhz-io/jest-webpack-alias/master/dependencies/npm)
[![bitHound Dev Dependencies](https://www.bithound.io/github/nhz-io/jest-webpack-alias/badges/devDependencies.svg)](https://www.bithound.io/github/nhz-io/jest-webpack-alias/master/dependencies/npm)
[![bitHound Code](https://www.bithound.io/github/nhz-io/jest-webpack-alias/badges/code.svg)](https://www.bithound.io/github/nhz-io/jest-webpack-alias)

Preprocessor for Jest that is able to resolve `require()` statements using webpack aliases.

See also [transform-jest-deps](https://github.com/Ticketmaster/transform-jest-deps).

## Install

```sh
npm install --save-dev jest-webpack-alias
```

## Setup

File `__tests__/preprocessor.js`:

```js
var babelJest = require('babel-jest');
require('babel-register'); // support ES6 'import' statements
var webpackAlias = require('jest-webpack-alias');

module.exports = {
  process: function(src, filename) {
    if (filename.indexOf('node_modules') === -1) {
      src = babelJest.process(src, filename);
      src = webpackAlias.process(src, filename);
    }
    return src;
  }
};
```

File `package.json`:

```
{
  ...
  "jest": {
    ...
    "scriptPreprocessor": "<rootDir>/__tests__/preprocessor.js",
  },
  "jest-webpack-alias": {
    "profile": "dev"
  }
}
```

## Common problems

### Manual package resolution

Code like this will not work, because an AST parser is not smart enough to evaluate variables into strings.

```js
var moduleName = 'myModName';
var computed = require(moduleName);
```

It can be rewritten like this, using the `resolve` function:

```js
var resolve = require('jest-webpack-alias').resolve;
var moduleName = 'myModName';
var computed = require(resolve(moduleName, __filename));
```

## package.json options

- `jest-webpack-alias.configFile`: Optional, default is `"webpack.config.js"`. If provided, this should be a path
  fragment relative to your `package.json` file.  Example: `"webpack/config.dev.js"`.

- `jest-webpack-alias.profile`: Optional. If provided, will expect your webpack config to be an array of profiles, and
  will match against the `name` field of each to choose a webpack config that applies to your Jest tests. See
  https://github.com/webpack/webpack/tree/master/examples/multi-compiler for an example of this kind of setup.

## Known issues

- `resolve.alias` settings whose values are absolute paths might not work
- `resolve.modulesDirectories` only searches the directory containing your package.json file, not all ancestors of current file

## License

MIT
