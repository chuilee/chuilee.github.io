---
layout: post
title: "Eslint+prettier+stylelint+commitlint踩坑记"
author: "Chuilee"
tags: 前端工程
excerpt_separator: <!--more-->
---

> 网上有很多教怎么搭建的，但却很少记录踩坑的

<!--more-->

### 踩坑

1. #### 无法执行的问题

```shell
git commit -m "test it for hook"
提示：因为没有将钩子 '.git/hooks/commit-msg' 设置为可执行，钩子被忽略。您可以通过
提示：配置 `git config advice.ignoredHook false` 来关闭这条警告。
[dev 32f4480] test it for hook
```

解决方法：

```sh
chmod a+x commit-msg
```

增加文件的执行权限。

2. #### 根目录下存在`.prettierrc.js`配置文件，但右键格式化始终不起作用

解决方法：

```shell
npm i -D prettier 或者 npm i -g prettier
```

安装 prettier 解决

### 配置 eslint、prettier

```sh
npm i eslint prettier eslint-config-prettier eslint-plugin-prettier eslint-plugin-vue @typescript-eslint/parser @typescript-eslint/eslint-plugin -D
```

- eslint: ESLint 的核心代码
- prettier：prettier 插件的核心代码
- eslint-config-prettier：解决 ESLint 中的样式规范和 prettier 中样式规范的冲突，以 prettier 的样式规范为准，使 ESLint 中的样式规范自动失效
- eslint-plugin-prettier：将 prettier 作为 ESLint 规范来使用
- eslint-plugin-vue：包含常用的 vue 规范
- @typescript-eslint/parser：ESLint 的解析器，用于解析 typescript，从而检查和规范 Typescript 代码
- @typescript-eslint/eslint-plugin：包含了各类定义好的检测 Typescript 代码的规范

1. #### 在根目录下新建`.eslintrc.js`文件

```typescript
/**
 * 所需插件
 * prettier // 规则见 https://prettier.io/docs/en/options.html
 * eslint // 规则见 https://cn.eslint.org/docs/rules/
 * eslint-plugin-react 规则见 https://github.com/yannickcr/eslint-plugin-react
 * eslint-plugin-prettier // 将prettier作为ESLint规范来使用
 * eslint-config-prettier
 * @typescript-eslint/eslint-plugin
 * @typescript-eslint/parser // ESLint的解析器，用于解析typescript，从而检查和规范Typescript代码
 *
 * "off" 或 0 - 关闭规则
 * "warn" 或 1 - 开启规则，使用警告级别的错误：warn (不会导致程序退出)
 * "error" 或 2 - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)
 */

module.exports = {
  root: true,
  env: {
    browser: true,
    node: true,
  },
  /* 优先级低于parse的语法解析配置 */
  parserOptions: {
    parser: "@typescript-eslint/parser", // Specifies the ESLint parser
    ecmaVersion: 2020, // Allows for the parsing of modern ECMAScript features
    sourceType: "module", // Allows for the use of imports
    ecmaFeatures: {
      // tsx: true, // Allows for the parsing of JSX
      jsx: true,
    },
  },
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "prettier",
    "plugin:prettier/recommended",
  ],
  plugins: ["react"],
  rules: {
    "no-console": process.env.NODE_ENV === "production" ? 1 : 0,
    "no-debugger": process.env.NODE_ENV === "production" ? 1 : 0,
    eqeqeq: 2, // 要求使用 === 和 !==
    "vue/eqeqeq": 2, // 要求使用 === 和 !==
    "no-undef": 2, // 禁用未声明的变量
    "vue/require-v-for-key": 1, // 当v-for写在自定义组件上时，它需要同时使用v-bind：key。在其他元素上，v-bind：key也最好写。
    "no-unused-vars": 1, // 禁止出现未使用过的变量
    "vars-on-top": 1, // 要求所有的 var 声明出现在它们所在的作用域顶部
    "prefer-destructuring": 0, // 优先使用数组和对象解构
    "no-useless-concat": 1, // 禁止不必要的字符串字面量或模板字面量的连接
    "no-useless-escape": 0, // 禁止不必要的转义字符
    "consistent-return": 1, // 要求 return 语句要么总是指定返回的值，要么不指定
    camelcase: 0, // 强制使用骆驼拼写法命名约定
    "no-redeclare": 1, // 禁止多次声明同一变量
    "array-callback-return": 1, // 强制数组方法的回调函数中有 return 语句,Array有几种过滤，映射和折叠的方法。如果我们忘记return在这些回调中写入语句，那可能是一个错误。
    "default-case": 1, // 要求 switch 语句中有 default 分支
    "no-fallthrough": 1, // 禁止 case 语句落空
    "no-lonely-if": 1, // 禁止 if 作为唯一的语句出现在 else 语句中.如果一个if陈述是该else块中唯一的陈述，那么使用一个else if表格通常会更清晰。
    "no-irregular-whitespace": 1, // 禁止在字符串和注释之外不规则的空白
    "prefer-const": 0, // 要求使用 const 声明那些声明后不再被修改的变量.如果一个变量从不重新分配，使用const声明更好。const 声明告诉读者，“这个变量永远不会被重新分配，”减少认知负荷并提高可维护性。
    "no-use-before-define": 1, // 禁止在变量定义之前使用它们
    "@typescript-eslint/explicit-module-boundary-types": 0,
    "react/react-in-jsx-scope": 0,
    "@typescript-eslint/no-var-requires": 0,
  },
  overrides: [
    {
      files: ["**/__tests__/*.{j,t}s?(x)"],
      env: {

        mocha: true,
      },
    },
  ],
};
```

2. #### 在根目录下建立 prettier 配置文件： `.prettierrc.js`

```typescript
module.exports = {
  printWidth: 100, // 单行输出（不折行）的（最大）长度
  tabWidth: 2, // 每个缩进级别的空格数
  tabs: false, // 使用制表符 (tab) 缩进行而不是空格 (space)。
  semi: true, // 是否在语句末尾打印分号
  singleQuote: true, // 是否使用单引号
  quoteProps: "as-needed", // 仅在需要时在对象属性周围添加引号
  jsxSingleQuote: false, // jsx 不使用单引号，而使用双引号
  trailingComma: "none", // 去除对象最末尾元素跟随的逗号
  bracketSpacing: true, // 是否在对象属性添加空格
  jsxBracketSameLine: true, // 将 > 多行 JSX 元素放在最后一行的末尾，而不是单独放在下一行（不适用于自闭元素）,默认false,这里选择>不另起一行
  arrowParens: "always", // 箭头函数，只有一个参数的时候，也需要括号
  proseWrap: "always", // 当超出print width（上面有这个参数）时就折行
  htmlWhitespaceSensitivity: "ignore", // 指定 HTML 文件的全局空白区域敏感度, "ignore" - 空格被认为是不敏感的
  vueIndentScriptAndStyle: false, // 在VUE文件中不要缩进脚本和样式标记
  stylelintIntegration: true,
  endOfLine: "auto",
};
```

### 配置 stylelint

```sh
npm i stylelint stylelint-scss stylelint-config-standard-scss stylelint-config-prettier -D
```

#### 根目录下新增 `**.stylelintrc.js**`文件

```typescript
module.exports = {
  processors: [],
  plugins: [],
  extends: ['stylelint-config-standard-scss', 'stylelint-config-recommended-vue/scss', 'stylelint-config-recess-order'],
  rules: {
    'declaration-colon-space-after': 'always-single-line',
    'declaration-colon-space-before': 'never',
    'declaration-block-trailing-semicolon': null,
    'declaration-block-semicolon-space-before': 'never',
    'media-feature-name-no-unknown': null,
    'selector-pseudo-class-no-unknown': [
      true,
      {
        ignorePseudoClasses: ['deep'],
      },
    ],
    'rule-empty-line-before': [
      'always',
      {
        ignore: ['after-comment', 'first-nested'],
      },
    ],
    // style calc中使用v-bind
    'function-calc-no-unspaced-operator': null,
  }, // 可以自己自定一些规则
};
```

### husky 和 lint-staged 构建代码工作流

```bash
npm i husky lint-staged @commitlint/cli @commitlint/config-conventional -D
```

- `@commitlint/cli`: commitlint 的命令行工具
- `@commitlint/config-conventional`: commitlint 的规则集
- `husky`: 阻止不符合提交规则的 git 记录

```bash
husky install
```

修改 ./husky/pre-commit 钩子

```sh
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

修改 ./husky/commit-msg

```sh
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# node @commitlint/cli/lib/cli.js --edit $1
npx --no-install commitlint --config commitlint.config.js --edit $1
```

根目录创建**commitlint.config.js**

```typescript
module.exports = { extends: ['@commitlint/config-conventional'] }
```

根目录创建**.lintstagedrc.js**

```typescript
module.exports = {
  '*.{js,jsx,ts,tsx}': ['eslint  --fix', 'prettier --write'],
  '*.md': ['prettier --write'],
  '*.{scss,less,styl,html}': ['stylelint --fix', 'prettier --write'],
  '*.vue': ['eslint --fix', 'prettier --write', 'stylelint --fix'],
};
```
