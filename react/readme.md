[eslint](#eslintrcjs)
[prettier](#prettierrcjs)
[babel](#babelrcjson)
[package.json](#packagejson)
[autoComponent](#autocomponent)

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
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}


}
```

## package.json

```json
"jest": {
    "collectCoverageFrom": [
      "src/**/*.{js,jsx,ts,tsx}",
      "!<rootDir>/src/*.{js,jsx,ts,tsx}",
      "!<rootDir>/src/**/*mock*.{js,jsx,ts,tsx}",
      "!<rootDir>/src/styles/*.{js,jsx,ts,tsx}",
      "!<rootDir>/**/stories.{js,jsx,ts,tsx}",
      "!<rootDir>/node_modules/"
    ]
}
```

## autoComponent
```js
// eslint-disable
const chokidar = require('chokidar');
const fs = require('fs');

const templates = {
  index: (name) =>
    `import P from 'prop-types';
import * as Styled from './styles';

export const ${name} = ({ children }) => {
  return (
    <Styled.Container>
      <h1>{children}</h1>
    </Styled.Container>
  );
};

${name}.propTypes = {
  children: P.node.isRequired,
};
`,

  test: (name) =>
    `import { renderTheme } from '../../styles/render-theme';
import { screen } from '@testing-library/react';
import { theme } from '../../styles/theme';
import { ${name} } from '.';

describe('<${name} />', () => {
  it('should render', () => {
    renderTheme(<${name}>Children</${name}>);

    expect();
  });
});
`,
  styles: (name) =>
    "import styled, { css } from 'styled-components';" +
    '\n\nexport const Container = styled.div`' +
    '\n\t${({ theme }) => css``}' +
    '\n`;' +
    '\n',
  stories: (name) =>
    `import { ${name} } from '.';

export default {
  title: '${name}',
  component: ${name},
  args: {
    children: 'example',
  },
  argTypes: {
    children: { type: 'string' },
  },
};

export const Template = (args) => {
  return (
    <div>
      <${name} { ...args } />
    </div>
  );
};
`,
};

const fileExists = (path) => (file) => fs.existsSync(`${path}/${file}`);

const writeToPath = (path) => (file, content) => {
  const filePath = `${path}/${file}`;

  fs.writeFile(filePath, content, (err) => {
    if (err) throw err;
    console.log('Created file: ', filePath);
    return true;
  });
};

function createFiles(path, name) {
  const files = {
    index: 'index.jsx',
    test: `${name}.test.jsx`,
    stories: `stories.jsx`,
    styles: `styles.js`,
  };

  if (name !== 'components') {
    const writeFile = writeToPath(path);
    const toFileMissingBool = (file) => !fileExists(path)(file);
    const checkAllMissing = (acc, cur) => acc && cur;
    const noneExist = Object.values(files)
      .map(toFileMissingBool)
      .reduce(checkAllMissing);

    if (noneExist) {
      console.log(`Detected new component: ${name}, ${path}`);
      Object.entries(files).forEach(([type, fileName]) => {
        writeFile(fileName, templates[type](name));
      });
    }
  }
}

const watcher = chokidar
  .watch('src/components/**', { ignored: /node_modules/ })
  .on('addDir', (path, event) => {
    const name = path.replace(/.*\/components\//, '');
    if (!name.includes('/')) createFiles(path, name);
  });

```
