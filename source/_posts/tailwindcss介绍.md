---
title: tailwindcss介绍
date: 2023-03-31 22:24:54
tags: css tailwindcss
featured_image: https://www.bythewayer.com/img/tailwindcss.webp
---

## Tailwindcss 介绍

Tailwind CSS 是一个功能类优先的 CSS 框架，它集成了诸如 flex, pt-4, text-center 和 rotate-90 这样的的类，它们能直接在脚本标记语言中组合起来，构建出任何设计.

[安装参照官网即可](https://www.tailwindcss.cn/docs/installation)

> 99%的功能官网都是介绍，看完英文官网 英语水平稍微提高了一丢丢

### 创建 TailwindCSS 配置文件

```js
npx tailwindcss init -p
// 其实我想知道npx tailwindcss 是怎么运行起来的
npx @chendap/wmh-theme??
```

这一步会做两件事：

- 创建 postcss.config.js 文件，这里的配置主要是添加 tailwindcss 的插件，这样你编写的 css 才会被 tailwindcss 处理；
- 创建 tailwind.config.js 文件，主要进行扫描规则、主题、插件等配置。

所有的安装完之后需要在引入的 css 文件里添加

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

其中 tailwind base 相当于一份重置样式表，包含了最基础的样式。tailwind components 包含了一些组件类， 组件相当于复合样式，tailwind utilities 包含了工具类，也就是 flex mx-auto 这些内置样式。

这么划分的原因是因为 css 的优先级规则，tailwindcss 全部都是一级样式，在类名权重相等的情况，下面的样式可以覆盖上面的样式，所以工具类优先，组件类次之，基础样式兜底，生成的样式顺序尤为重要，所以 上面三句指令的顺序非必须建议不要修改。

## 进阶用法

### 添加自己的插件[使用可复用的第三方插件扩展 Tailwind](https://tailwindcss.com/docs/plugins)

比如项目中有一处样式是这样，需要一行文本之后使用省略号，或者三行文本需要使用省略号，这时候你该怎么办

```js
// tailwind.config.js
const plugin = require("tailwindcss/plugin");

module.exports = {
  plugins: [
    plugin(function ({
      addUtilities,
      addComponents,
      addBase,
      addVariant,
      e,
      prefix,
      config,
    }) {
      // Add your custom styles here
    }),
  ],
};
```

比如官方提供的[line-clamp 插件](https://github.com/tailwindlabs/tailwindcss-line-clamp)

```js
const plugin = require("tailwindcss/plugin");
const baseStyles = {
  overflow: "hidden",
  display: "-webkit-box",
  "-webkit-box-orient": "vertical",
};

const lineClamp = plugin(
  function ({ matchUtilities, addUtilities, theme, variants }) {
    const values = theme("lineClamp");
    matchUtilities(
      {
        "line-clamp": (value) => ({
          ...baseStyles,
          "-webkit-line-clamp": `${value}`,
        }),
      },
      { values }
    );
    addUtilities(
      [
        {
          ".line-clamp-none": {
            "-webkit-line-clamp": "unset",
          },
        },
      ],
      variants("lineClamp")
    );
  },
  {
    theme: {
      lineClamp: {
        1: "1",
        2: "2",
        3: "3",
        4: "4",
        5: "5",
        6: "6",
      },
    },
    variants: {
      lineClamp: ["responsive"],
    },
  }
);

module.exports = lineClamp;
```

### 响应式断点

这是我觉得比较方便的一点，针对不同的屏幕采用不同的断点，很方便构建各种屏幕的样式

| 断点前缀 | 最小宽度 |                CSS                 |
| :------- | -------: | :--------------------------------: |
| sm       |    640px | @media (min-width: 640px) { ... }  |
| md       |    768px | @media (min-width: 768px) { ... }  |
| lg       |   1024px | @media (min-width: 1024px) { ... } |
| xl       |   1280px | @media (min-width: 1280px) { ... } |
| 2xl      |   1536px | @media (min-width: 1536px) { ... } |

比如下面这种，sm 的占 1/2，中等屏幕以上的每分占 1/3

```html
<template>
  <div class="mb-4 flex w-full p-4 sm:w-1/2 md:w-1/3"></div>
</template>
```

还需要注意的一点是：默认情况下，Tailwind 使用移动优先的断点系统，类似于 Bootstrap 这些其它框架中的用法。

这意味着未加前缀的功能类 (像 uppercase) 在所有的屏幕上都有效，而加了前缀的功能类（如 md:uppercase）仅在指定断点及以上的屏幕上生效。

您可以在 tailwind.config.js 文件中完全自定义您的断点：

```js
// tailwind.config.js
module.exports = {
  theme: {
    screens: {
      tablet: "640px",
      // => @media (min-width: 640px) { ... }

      laptop: "1024px",
      // => @media (min-width: 1024px) { ... }

      desktop: "1280px",
      // => @media (min-width: 1280px) { ... }
    },
  },
};
```

### 暗黑模式切换

- 配置

```js
// tailwind.config.js
module.exports = {
  darkMode: "class",
  // ...
};
```

现在，dark:{class} 类将不再根据 prefers-color-scheme 起作用，而是当在 HTML 树中出现 dark 类时起作用。

```html
<!-- Dark mode not enabled -->
<html>
  <body>
    <!-- Will be white -->
    <div class="bg-white dark:bg-black">
      <!-- ... -->
    </div>
  </body>
</html>

<!-- Dark mode enabled -->
<html class="dark">
  <body>
    <!-- Will be black -->
    <div class="bg-white dark:bg-black">
      <!-- ... -->
    </div>
  </body>
</html>
```

如何将 dark 类添加到 html 元素中取决于您，但是一种常见的方式是使用 JS 从某个地方（如 localStorage）读取首选项并相应的更新 DOM。

这是一个简单的示例，说明如何支持浅色模式、深色模式，以及如何遵守操作系统的首选项。

```js
// On page load or when changing themes, best to add inline in `head` to avoid FOUC
if (
  localStorage.theme === "dark" ||
  (!("theme" in localStorage) &&
    window.matchMedia("(prefers-color-scheme: dark)").matches)
) {
  document.documentElement.classList.add("dark");
} else {
  document.documentElement.classList.remove("dark");
}

// Whenever the user explicitly chooses light mode
localStorage.theme = "light";
// Whenever the user explicitly chooses dark mode
localStorage.theme = "dark";
// Whenever the user explicitly chooses to respect the OS preference
localStorage.removeItem("theme");
```

### Reusing Styles 之 自定义 class 中使用 Tailwind 语法 [(使用 @apply 抽取组件类)](https://tailwindcss.com/docs/reusing-styles#extracting-classes-with-apply)

```html
<!-- Before extracting a custom class -->
<button
  class="py-2 px-4 bg-blue-500 text-white font-semibold rounded-lg shadow-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:ring-opacity-75"
>
  Save changes
</button>

<!-- After extracting a custom class -->
<button class="btn-primary">Save changes</button>
```

抽象定制一个类**btn-primary**包含 `py-2 px-4 bg-blue-500 text-white font-semibold rounded-lg shadow-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:ring-opacity-75`,TailwindCSS 提供了@apply 语法，这种语法的使用手感与在 html 模板中使用是一样的：

```css
//styles/global.css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .btn-primary {
    @apply py-2 px-4 bg-blue-500 text-white font-semibold rounded-lg shadow-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:ring-opacity-75;
  }
}
```

### 自定义样式中使用 Tailwind 的变量[使用 theme() 函数可以通过点符号来获取 Tailwind 配置的值](https://www.tailwindcss.cn/docs/functions-and-directives)

大多数情况下，我们完全可以组合使用 Tailwind 的原子化 class 来解决问题。但如果就是要使用自定义的样式代码，我们该如何使用到 Tailwind 配置中定义的变量呢. 比如下面的代码中，希望为某个元素添加一个上边框，但是又想要使用到 Tailwind 配置中的 gray-200 颜色，该怎么办呢？

```css
div {
  border-top: 1px solid ?;
}
```

因为 TailwindCSS 本身是一个 PostCSS 插件，所以理论上任何有关 TailwindCSS 的配置信息都可以通过 css 的方式拿到。

TailwindCSS 提供了一个 theme 函数，可以拿到 tailwind.config.js 中配置的 theme 的值：

```css
div {
  border-top: 1px solid theme("color.gray.200");
}
```

### 重写/覆盖 Tailwind 配置[自定义配置变量](https://www.tailwindcss.cn/docs/customizing-colors)

```js
// tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    extend: {
      textColor: {
        // [!code focus:5]
        primary: "#1D2129",
        regular: "#4E5969",
        secondary: "#86909C",
        disabled: "#C9CDD4",
      },
    },
  },
  // ...
};
```

然后就可以使用了：

```html
<span class="text-primary">主色</span> /
<span class="text-regular">常规色</span> /
<span class="text-secondary">次要色</span> /
<span class="text-disabled">禁用色</span> /
```

### 参考

- [Tailwind CSS 方案简介](https://mp.weixin.qq.com/s/YH3RCYGdvd67jkmN8DPIgg)
