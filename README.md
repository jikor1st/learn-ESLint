# learn-ESLint

[doc]: https://poiemaweb.com/eslint

ESLint는 코딩 컨벤션에 위배되는 코드나 안티 패턴을 자동 검출하는 도구입니다. ESLint는 처음부터 유용하게 사용할 수 있는 스타일 가이드(built-in rule)을 제공하지만 개발자가 자신의 스타일 가이드를 작성할 수도 있습니다.

## install eslint & Airbnb style guide

eslint와 필요 플러그인을 로컬 설치합니다. 전역으로 설치할 수도 있으나 권장하지 않습니다.

- eslint-config-airbnb-base

  Airbnb의 style guide를 eslint의 설정 파일인 .eslintrc.json에 확장해 주는 플러그인(React 관련 플러그인 미포함)

- eslint-plugin-import

  ES6+ import/export 지원 플러그인

- eslint-plugin-html

  HTML 파일에 포함된 인라인 자바스크립트 지원 플러그인

```bash
npm init -y

# install eslint & plugins
npm i --save-dev eslint eslint-config-airbnb-base eslint-plugin-import eslint-plugin-html
```

## install ESLint VSCode extension

[VS Code ESLint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 을 설치합니다.

메뉴 Code > 기본 설정 > 설정 > setting.json

```json
...
// ESLINT
"eslint.validate": ["html", "javascript"]
```

## .eslintrc.json

프로젝트의 루트에 `.eslintrc.json` 파일을 생성하고 설정을 변경합니다.

설정 방법은 [여기](https://eslint.org/docs/latest/user-guide/configuring)를 참고

```json
{
  "parserOptions": {
    "ecmaVersion": "latest"
  },
  "env": {
    "browser": true,
    "node": true,
    "commonjs": true,
    "es2022": true
  },
  "globals": { "_": true },
  "plugins": ["import", "html"],
  "extends": "airbnb-base",
  "rules": {
    // "off" or 0 - turn the rule off
    // "warn" or 1 - turn the rule on as a warning (doesn’t affect exit code)
    // "error" or 2 - turn the rule on as an error (exit code is 1 when triggered)
    // "no-var": "off",
    "no-console": "warn",
    "no-plusplus": "off",
    "no-shadow": "off",
    "vars-on-top": "off",
    "no-underscore-dangle": "off", // var _foo;
    "comma-dangle": "off",
    "func-names": "off", // setTimeout(function () {}, 0);
    "prefer-template": "off",
    "no-nested-ternary": "off",
    "max-classes-per-file": "off",
    "consistent-return": "off",
    "no-restricted-syntax": ["off", "ForOfStatement"], // disallow specified syntax(ex. WithStatement)
    "prefer-arrow-callback": "error", // Require using arrow functions for callbacks
    "require-await": "error",
    "arrow-parens": ["error", "as-needed"], // a => {}
    "no-param-reassign": ["error", { "props": false }],
    "no-unused-expressions": [
      "error",
      {
        "allowTernary": true, // a || b
        "allowShortCircuit": true, // a ? b : 0
        "allowTaggedTemplates": true
      }
    ],
    "import/no-extraneous-dependencies": ["error", { "devDependencies": true }],
    "max-len": [
      "error",
      {
        "code": 120,
        "ignoreComments": true,
        "ignoreStrings": true,
        "ignoreTemplateLiterals": true
      }
    ] // prettier의 printWidth 옵션 대신 사용
  }
}
```

## ESLint + Prettier

코드 포맷터인 Prettier과 ESLint를 함께 사용합니다.

prettier-vscode를 VSCode 익스텐션을 통해 설치합니다.

perttier-vscode 익스텐션을 설치해 사용한다면 Prettier를 별도 설치할 필요가 없지만 Prettier CLI를 사용할 경우를 대비해서 Prettier를 설치합니다.

```bash
# --save-exact 옵션을 사용하여 package.json에 ^ 없이 등록되도록 설치합니다.
npm i --save-dev --save-exact prettier
```

## `.prettierrc.json`

프로젝트 루트에 Prettier 설정 파일 .prettierrc.json 파일을 생성합니다.

설정 방법은 [여기](https://prettier.io/docs/en/configuration.html)를 참고합니다.

```json
{
  "singleQuote": true,
  "bracketSpacing": true,
  "bracketSameLine": true,
  "arrowParens": "avoid",
  "printWidth": 120
}
```

- `eslint-config-prettier`

  EsLint와 Prettier을 같이 사용하려면 패키지를 추가로 설치해야합니다.

  ESLint 설정 중에서 Prettier와 충돌하는 부분을 비활성화 합니다.

  ```bash
  npm install --save-dev eslint-config-prettier
  ```

  .eslintrc.json 파일을 수정해줍니다

  ```json
  {
  ...
  "extends": ["airbnb-base", "prettier"],
  ...
  }
  ```

## autofix

파일 저장과 함께 자동으로 Prettier 포맷팅이 실행되도록 하려면 settings.jsom에 설정 파일을 추가해줍니다.

```json
...
  /////////////////////////////
  // ESLint
  "eslint.validate": [
    "html",
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
  "eslint.alwaysShowStatus": true,
  /////////////////////////////
  // Prettier
  // 수정 후 저장할 때 prettier로 자동 스타일링
  "editor.formatOnSave": true,
  // 수정 후 저장할 때 eslint로 autofix 실행 (ex. let => const)
  "editor.codeActionsOnSave": { "source.fixAll.eslint": true },
  "editor.defaultFormatter": "esbenp.prettier-vscode",
...
```

## @babel/eslint-parser

private 필드 정의 제안과 같이 (지금은 표준이 된) ECMAScript 정식 표준이 아닌 제안 단계의 자바스크립트 코드의 경우 eslint 가 소스 코드를 파싱할 수 없어 에러가 발생하는 경우가 있습니다.

이런 경우 @babel/eslint-parser을 사용해 자바스크립트 소스 코드를 파싱해야 합니다.

```bash
npm i -D @babel/core @babel/eslint-parser @babel/preset-env
```

- `babel.config.json`

  ```json
  {
    "presets": ["@babel/preset-env"]
  }
  ```

- `.eslintrc.json`

.eslintrc.json에 parser을 설정하면 에러가 발생하지 않습니다.

```json
{
  "parser": "@babel/eslint-parser",
  ...
}
```

## Typescript

Typescript 고유의 문법을 파싱하기 위해 @typescript-eslint/parser을 사용해야 합니다.

```bash
npm install --save-dev typescript @typescript-eslint/parser
```

.eslintrc.json에 parser을 설정해줍니다.

```json
{
  "parser": "@typescript-eslint/parser",
  ...
}
```

## React

React 프로젝트에서 ESLint와 Prettier을 같이 사용하려면 eslint-config-prettier 플러그인을 추가로 로컬 설치해야합니다. eslint-config-prettier은 ESLint 설정 중에서 Prettier와 충돌하는 부분을 비활성화 합니다.

```bash
npm install --save-dev eslint-config-prettier
```

.eslintrc.json을 수정합니다.

```json
...
  "extends": [
    "airbnb-base",
    "prettier",
    "react-app"
  ],
...
```
