[eslint](#eslintrcjs)
[prettier](#prettierrcjs)
[babel](#babelrcjson)

# Install

## Create react app

```zsh
  npx create-react-app .
```

## Vite

```zsh
  npm create vite@latest
```

# Files configs

## install

```zsh
 npm i styled-components
```

## install dev dependencies

```zsh
  npm i -D prettier @babel/eslint-parser @babel/preset-react @babel/env eslint eslint-plugin-prettier eslint-config-prettier eslint-plugin-react eslint-plugin-react-hooks jest-styled-components
```

## .eslintrc.js

```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
    jest: true,
  },
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "plugin:prettier/recommended",
  ],
  globals: {
    Atomics: "readonly",
    SharedArrayBuffer: "readonly",
  },
  parser: "@babel/eslint-parser",
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: "latest",
    sourceType: "module",
  },
  plugins: ["react", "prettier", "react-hooks"],
  settings: {
    react: {
      version: "detect",
    },
  },
  rules: {
    "react/react-in-jsx-scope": "off",
  },
};
```

## .prettierrc.js

```js
module.exports = {
  arrowParens: "always",
  bracketSpacing: true,
  endOfLine: "lf",
  htmlWhitespaceSensitivity: "ignore",
  insertPragma: false,
  jsxSingleQuote: false,
  printWidth: 80,
  proseWrap: "always",
  quoteProps: "as-needed",
  requirePragma: false,
  semi: true,
  singleQuote: true,
  tabWidth: 2,
  trailingComma: "all",
  useTabs: false,
  vueIndentScriptAndStyle: false,
  embeddedLanguageFormatting: "off",
};
```

## .babelrc.json

```json
{
  module.exports = {
  presets: [
    '@babel/preset-env',
    ['@babel/preset-react', { runtime: 'automatic' }],
  ],
};

}
```

## package.json

#### Retirar o "type": "modules"
