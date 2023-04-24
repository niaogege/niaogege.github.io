---
title: MyBlog 待办计划
date: 2023-04-21 15:48:26
tags: MyBlog, 2022
featured_image: https://www.bythewayer.com/img/prismjs.webp
---

## 初衷

很早就想搞一个自己的博客，从头到尾，现在距离梦想更进一步了！

## 功能介绍

- 完全自定义主题，页面静态化渲染，SSG
- 标签页和时间线以及关于自己页面
- 评论功能，现在还不知道对接哪一个评论系统
- 根据文档自动生成网站 tag
- 国际化(中英文)
- 黑白主题切换 (借助 next-theme)
- 全局搜索，借助 **algolia**
- 输出 npm,开源

## md 文档相关

- 支持文件夹嵌套?已支持
- md 文档样式采用 [tailwindcss/typography](https://tailwindcss.com/docs/typography-plugin)
- [支持代码高亮,代码高亮还是好丑](https://prismjs.com/index.html)，学学人家如何做到很美观的
- md 文档支持 通过索引建立目录,toC 这个真复杂？

## 技术栈

- 基础框架 Nextjs13.0
- 换肤 [next-themes](https://github.com/pacocoursey/next-themes)
- [gray-matter](https://github.com/jonschlinkert/gray-matter)：解析 md 文档成序列号数据， Smarter YAML front matter parser
- fast-glob
- 渲染 md 文档
- [mdast-util-to-string](https://www.npmjs.com/package/mdast-util-to-string) mdast utility to get the text content of a node.

## 后续努力方向

- 全网字体需要更换下，如何更换？
- 图片尺寸不一，需要统一裁剪
- 自动生成 rss 文件 **"build": "node ./scripts/gen-rss && next build"**
- 当前宽度小于 400px 顶部导航部分样式调整

## 技术细节

### 文章索引

```js
import { visit } from "unist-util-visit";
import { slug } from "github-slugger";
import { toString } from "mdast-util-to-string";

export default function remarkTocHeadings(options) {
  return (tree) =>
    visit(tree, "heading", (node) => {
      const textContent = toString(node);
      options.exportRef.push({
        value: textContent,
        url: "#" + slug(textContent),
        depth: node.depth,
      });
    });
}
```
