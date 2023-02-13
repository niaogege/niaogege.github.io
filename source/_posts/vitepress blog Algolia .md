---
title: vitepress博客使用Algolia进行全局搜索
date: 2023-02-13 19:31:34
tags: Algolia search
featured_image: https://www.bythewayer.com/img/res.webp
---

vitepress 博客推荐使用**Algolia**进行全文搜索

## Algolia

Algolia 是一个数据库实时搜索服务，能够提供毫秒级的数据库搜索服务，并且其服务能以 API 的形式方便地布局到网页、客户端、APP 等多种场景。
像 vite 官方文档就是使用的 Algolia 搜索，使用 Algolia 搜索最大的好处就是方便，它会自动爬取网站的页面内容并构建索引，你只要申请一个 Algolia 服务，在网站上添加一些代码，就像添加统计代码一样，然后就可以实现一个全文搜索功能：

### 申请，填表单

登陆[docsearch](https://docsearch.algolia.com/apply/)进行表单填写，如图
![apply申请表单](https://www.bythewayer.com/img/register.webp)
然后过几个小时会有回复：
![申请AppKey](https://www.bythewayer.com/img/reply.webp)

这个时候需要回复邮件，告诉自己就是网站的维护者，并且可以修改代码，然后得到最终的回复
![得到的回复](https://www.bythewayer.com/img/appKeyreply.webp)

> 一般需要 2-3 天才能得到回复

## vitePress 结合申请得到的 apiKey 和 appId, 配置下即可

VitePress supports searching your docs site using Algolia DocSearch. Refer their getting started guide. In your .vitepress/config.ts you'll need to provide at least the following to make it work:
点击：[theme-search](https://vitepress.vuejs.org/guide/theme-search)

```js
import { defineConfig } from "vitepress";

export default defineConfig({
  themeConfig: {
    algolia: {
      appId: "...",
      apiKey: "...",
      indexName: "...",
    },
  },
});
```

也阔以查看后台看看当时爬虫的数据记录 点击:[https://www.algolia.com/](https://www.algolia.com/apps/UTFWSZD8OF/explorer/browse/bythewayer)

![indexSearch](https://www.bythewayer.com/img/indexSearch.webp)
