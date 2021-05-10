---
title: VSCode 插件推荐以及setting.json配置
cover: 'https://static.ivan.fun/blog/post_2.jpg'
index_img: 'https://static.ivan.fun/blog/post_2.jpg'
tags:
  - VSCode
categories: -软件工具
abbrlink: 933a5b7e
date: 2020-03-13 08:00:00
---

## VSCode

> VSCode 提供了工作区的功能，为你的项目创建工作区，然后在工作区文件 setting 对象内添加如下设置

### 代码片段类插件

- 主题

  - One Dark Pro
    One Dark Pro 把 Atom 编辑器中流行的 “One Dark” 主题带到了 VS Code。
    ![](https://static.ivan.fun/blog/VSCode1.png)
  - vscode-icon
    让 vscode 资源树目录加上图标
    ![](https://static.ivan.fun/blog/VSCode2.png)
  - color highlight
    此扩展程序可样式化文档中的 CSS /网页颜色。
    ![](https://static.ivan.fun/blog/VSCode3.png)
  - Output Colorizer
    输出提示的文字颜色有一些变化，方便获取关键信息
    ![](https://static.ivan.fun/blog/VSCode4.png)
  - Guides
    指导线，当前所在的级别缩进线会变红，当前在哪一级一目了然
    ![](https://static.ivan.fun/blog/VSCode5.png)
  - Log File Highlighter
    日志文件(.log 后缀的文件)高亮
    ![](https://static.ivan.fun/blog/VSCode6.png)

- 代码片段类插件

  - VS Code JavaScript(ES6) snippets
    这个插件为 JavaScript、TypeScript、HTML、React 和 Vue 提供了 ES6 的语法支持；
    ![](https://static.ivan.fun/blog/VSCode7.png)
  - jQuery Code Snippets
    只需输入字母“ jq”即可获取所有可用的 jQuery 代码段的列表
    ![](https://static.ivan.fun/blog/VSCode9.png)
  - Reactjs code snippets
    这个扩展包含 Reactjs 的代码片段，并且基于很棒的 babel-sublime-snippets 包。
    ![](https://static.ivan.fun/blog/VSCode32.png)
  - React Redux ES6 Snippets
    使用 ES6 和箭头功能的 Visual Studio Code 的 React-Redux 代码段
    ![](https://static.ivan.fun/blog/VSCode33.png)
  - React-Native/React/Redux snippets for es6/es7
    该扩展为您提供 ES7 中的 JavaScript 和 React / Redux 片段，以及针对 VS Code 的 Babel 插件功能
    ![](https://static.ivan.fun/blog/VSCode34.png)
  - JavaScript (ES6) code snippets
    此扩展包含 Vs 代码编辑器的 ES6 语法的 JavaScript 代码片段（同时支持 JavaScript 和 TypeScript）
    ![](https://static.ivan.fun/blog/VSCode35.png)
  - Typescript React code snippets
    该扩展包含 React with Typescript 的代码片段
    ![](https://static.ivan.fun/blog/VSCode36.png)

- 自动补全类插件

  - Path Intellisense
    自动路劲补全
    ![](https://static.ivan.fun/blog/VSCode10.png)
  - Visual Studio IntelliCode
    从 GitHub 上高星的开源项目经过大量的机器学习训练，给开发者提供最合适的 IntelliSense 上下文建议功能，除此之外，还有代码格式化和规则推测等功能。
    ![](https://static.ivan.fun/blog/VSCode11.png)
  - Npm Intellisense
    用于在 import 语句中自动填充 npm 模块
    ![](https://static.ivan.fun/blog/VSCode12.png)
  - IntelliSense for CSS class names
    智能提示 css 的 class 名
    ![](https://static.ivan.fun/blog/VSCode13.png)
  - Vetur
    Vue 的语法高亮、智能感知、Emmet 等
    ![](https://static.ivan.fun/blog/VSCode14.png)
  - Surround
    在你的代码块中增加外包裹模板
    ![](https://static.ivan.fun/blog/VSCode15.png)
  - htmltagwrap
    可以在选中 HTML 标签中外面套一层标签
    ![](https://static.ivan.fun/blog/VSCode16.png)
  - Image Preview
    鼠标悬浮在链接或者装订线(gutter)左边可以预览到图片
    ![](https://static.ivan.fun/blog/VSCode17.png)
  - HTML CSS Support
    让 html 标签上写 class 智能提示当前项目所支持的样式。目前没有用，可能是因为当前作者的开发模式是 html 和 css 分开文件开发的。
    ![](https://static.ivan.fun/blog/VSCode18.png)

* 功能增强插件

  - Debugger for Chrome
    让 vscode 映射 chrome 的 debug 功能，静态页面都可以用 vscode 来打断点调试，需要另外配置
    ![](https://static.ivan.fun/blog/VSCode19.png)
  - Project Manager
    在多个项目之前快速切换的工具
    ![](https://static.ivan.fun/blog/VSCode20.png)
  - language-stylus
    为 Stylus 文件添加语法突出显示，stylus 是 CSS 的预处理框架
    ![](https://static.ivan.fun/blog/VSCode21.png)
  - code spell checker
    检查你的英文单词拼写是否有误，如果有库里不存在的单词则会下下波浪线
    ![](https://static.ivan.fun/blog/VSCode22.png)
  - Settings Sync
    Visual Studio 代码设置同步，需要 github 账号辅助的
    ![](https://static.ivan.fun/blog/VSCode23.png)
  - JS Refactor
    自动重构工具，目前没发现没怎么用
    ![](https://static.ivan.fun/blog/VSCode24.png)
  - turbo console log
    自动编写有意义的日志消息
    ![](https://static.ivan.fun/blog/VSCode25.png)

* 代码检测相关插件

  - ESlint
    ESlint 接管原生 js 提示，可以自定制提示规则；
    ![](https://static.ivan.fun/blog/VSCode26.png)
  - TSLint
    使用 TypeScript TSLint 语言服务插件将 tslint 添加到 VS Code
    ![](https://static.ivan.fun/blog/VSCode27.png)
  - Regex Previewer
    这是一个用于实时测试正则表达式的实用工具。它可以将正则表达式模式应用在任何打开的文件上，并高亮所有的匹配项。
    在并排文档中显示当前正则表达式的匹配项
    ![](https://static.ivan.fun/blog/VSCode28.png)

* 其他

  - Laravel goto view
    跳转到相应的文件路径
    ![](https://static.ivan.fun/blog/VSCode29.png)
  - open in browser
    当前的 html 文件用浏览器打开，快捷键 alt+b，或者右键点击 html 文件，选择 open in default Browsers
    ![](https://static.ivan.fun/blog/VSCode30.png)

* 图片

  - SVG Viewer
    无需离开编辑器，便可以打开 SVG 文件并查看它们
    ![](https://static.ivan.fun/blog/VSCode31.png)

### setting.json 配置

方法 1：File -> Preference -> Setting ->搜索 setting.json -> Edit in settings.json

方法 2：File -> Preference -> 搜索框输入：需要配置的项

```
{
  "window.zoomLevel": 0,
  "search.followSymlinks": false,
  "editor.formatOnSave": true,
  "eslint.autoFixOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.tslint": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "html",
    "vue",
    {
      "language": "vue",
      "autoFix": true
    },
    {
      "language": "typescript",
      "autoFix": true
    },
    {
      "language": "typescriptreact",
      "autoFix": true
    }
  ],
  "emmet.syntaxProfiles": {
    "javascript": "jsx",
    "vue": "html",
    "vue-html": "html"
  },
  "editor.tabSize": 2,
  "files.associations": {
    "*.vue": "vue"
  },
  "eslint.options": {
    "extensions": [
      ".js",
      ".vue"
    ]
  },
  "search.exclude": {
    "**/node_modules": true,
    "**/bower_components": true,
    "**/dist": true
  },
  "git.confirmSync": false,
  "javascript.implicitProjectConfig.experimentalDecorators": true,
  "editor.renderWhitespace": "boundary",
  "editor.cursorBlinking": "smooth",
  "editor.minimap.enabled": true,
  "editor.minimap.renderCharacters": true,
  //"editor.fontFamily": "'Droid Sans Mono', 'Courier New', monospace, 'Droid Sans Fallback'",
  "window.title": "${dirty}${activeEditorMedium}${separator}${rootName}",
  "editor.codeLens": true,
  "editor.snippetSuggestions": "top",
  "editor.fontWeight": "none",
  "stylelint.enable": true,
  "editor.fontSize": 16,
  "editor.rulers": [ //代码长度
    120
  ],
  "workbench.colorTheme": "One Dark Pro", //主题
  "workbench.iconTheme": "vscode-icons",
  "editor.suggestSelection": "first",
  "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
  "vsicons.dontShowNewVersionMessage": true //图标
}
```
