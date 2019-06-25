---
layout: post
title: "代码格式化"
description: ""
category: web
tags: [ formatting ]
---

在开发过程中，一个项目经常是多人参与的，不同的人有着不同的代码风格，统一的语法规则和代码风格可以方便日后代码的阅读与维护。统一的代码风格是需要工具进行支持的，仅仅依靠人力进行代码检查是无法保证的。

<!-- more -->

![code-format-flow](/assets/images/web/code-format-flow.png)

## ESLint

  [ESLint](https://eslint.org/) 略。

## Prettier

[Prettier](https://prettier.io/) 是一个代码格式化工具。Prettier 与 Linters 区别如下：
Linters 提供的功能如下：

 Rules | Prettier  |  Linters  
-|-|-
Formatting rules | YES | YES |
Code-quality rules | NO | YES|

## githooks

[githooks](https://git-scm.com/docs/githooks) 提供了 客户端 hooks 和 服务端 hooks, 通常可以在 pre-commit 或 pre-push 阶段对代码进行检查。 一个简单的 `pre-commit` hooks 如下，实现的功能是 对缓存区 staged 中的文件进行 eslint 检查：

```sh
#!/bin/sh

change_files=$(git diff --cached --name-only --diff-filter=ACM -- '*.less' '*.css' '*.js' | grep '^src/')

if test ${#change_files} -gt 0
then
    which node_modules/.bin/eslint &> /dev/null
    if [[ "$?" == 1 ]]; then
      echo >&2 "\033[31m Hooks Error: eslint not install \033[0m"
      exit 1
    fi

    eslint_pass=true

    for file in $change_files
    do
      node_modules/.bin/eslint $file
      if [[ "$?" == 1 ]]; then
        eslint_pass=false
      fi
    done

  if ! $eslint_pass; then
      echo >&2 "\033[31m Hooks Error: eslint not pass \033[0m"
      exit 1
  fi
fi

exit 0
```

弊端：只能在本地 client 生效，每个人都需要手动添加这个 hooks

## Husky

[Husky](https://github.com/typicode/husky) 支持在 `git commit` 或 `git push` 之前做一些额外的事情。

```json
  {
    "husky": {
      "hooks": {
        "pre-commit": "node_modules/.bin/eslint"
      }
    }
  }
```

这样，只要安装了 husky， git commit 操作时，首先会触发 `pre-commit`

## lint-staged

[lint-staged](https://github.com/okonet/lint-staged) 会提供 git staged 中的文件。在 git commit 的时候，只关注当前 staged 的文件，而不关心其他文件。

  ```json
  {
    "lint-staged": {
      "src/**/*.{js, less, css}": ["eslint --fix", "git add"]
    }
  }
  ```

## 简单的配置

  package.json

  ```json
  {
    "husky": {
      "hooks": {
        "pre-commit": "lint-staged"
      }
    },
    "lint-staged": {
      "src/**/*.{js, less, css}": ["prettier --write", "eslint --fix", "git add"]
    },
    "prettier": {
    }
  }
```