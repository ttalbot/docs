---
layout: ~/layouts/MainLayout.astro
title: MPAs 与 SPAs
description: "了解多页面应用程序 (MPA) 和单页面应用程序 (SPA) 架构之间的权衡是了解 Astro 与其他 Web 框架的不同之处的关键。"
i18nReady: true
---

了解多页面应用程序 (MPA，Multi-Page Application) 和单页面应用程序 (SPA，Single-Page Application) 架构之间的权衡是了解 Astro 与 Next.js 和 Remix 等其他 Web 框架不同之处的关键。

## 术语

**多页应用 (MPA，Multi-Page Application)** 是一个由多个 HTML 页面组成的网站，主要呈现在服务器上。 当您导航到一个新页面时，您的浏览器会从服务器请求一个新的 HTML 页面。 **Astro 是一个 MPA 框架。** 传统的 MPA 框架还包括 Ruby 与 Rails、Python Django、PHP Laravel、WordPress、Joomla、Drupal 和静态网站构建器，如 Eleventy 或 Hugo。

**单页应用(SPA，Single-Page Application)** 是一个由单个 JavaScript 应用程序组成的网站，该应用程序在用户浏览器中加载，然后在本地呈现 HTML。 SPA 也可能在服务器上生成 HTML，但 SPA 的独特之处在于它们能够在浏览器中将您的网站作为 JavaScript 应用程序运行，以便在您导航时呈现新的 HTML 页面。此外， Next.js、Nuxt、SvelteKit、Remix、Gatsby 和 Create React App 都是 SPA 框架的示例。

## Astro 和其他的多页应用(MPAs)区别 

Astro 是一个 MPA 框架。 然而，Astro 也不同于其他 MPA 框架。 它的主要区别在于它使用 JavaScript 作为其服务器语言和运行时。 传统的 MPA 框架会让您在服务器上编写不同的语言（Ruby、PHP 等）并在浏览器上编写 JavaScript。 在 Astro 中，您总是只是在编写 JavaScript、HTML 和 CSS。 这样，您可以在服务器和客户端上呈现您的 UI 组件（如 React 和 Svelte）。

其结果是开发人员体验很像 Next.js 和其他现代 Web 框架，但具有更传统的 MPA 站点架构的性能特征。

## 多页应用(MPAs)于单页应用(SPAs)对比 

在比较 MPA 与 SPA 时，需要注意三个主要区别：

####  服务器渲染(MPA) 与 客户端渲染(SPA)

在 服务器渲染(MPA,Server rendering) 中，页面的大部分 HTML 都呈现在服务器上。 而客户端渲染(SPA,client rendering ) 中，大多数 HTML 是通过在浏览器中运行 JavaScript 在本地呈现的。 这对网站行为、性能和 SEO 产生了巨大影响。

在浏览器中呈现 HTML 可能听起来比从远程服务器请求更快的选项。 然而，事实恰恰相反。 除非使用服务器渲染，否则 SPA 在首页加载时始终会比 MPA 执行更慢。 这是因为 SPA 需要在浏览器中下载、解析和运行整个 JavaScript 应用程序，才能在页面上呈现任何 HTML。 

然后，您的 SPA 可能无论如何都需要获取远程数据，从而在您的页面完成加载之前引入更多的等待时间。

大多数 SPA 框架会尝试通过在第一个页面加载时添加基本的服务器渲染来缓解这个性能问题。 这是一项改进，但它引入了新的复杂性，因为您的网站现在可以以多种方式和多种环境（服务器、浏览器）呈现。 这也引入了一个新的“[恐怖谷理论](https://zh.wikipedia.org/zh-tw/%E6%81%90%E6%80%96%E8%B0%B7%E7%90%86%E8%AE%BA)”问题，您的网站似乎已加载 (HTML)，但由于应用程序 JavaScript 逻辑仍在后台加载，因此无响应。

MPA 在远程服务器上呈现所有 HTML，并且通常不需要太多（如果有）JavaScript 即可运行。 这为 MPA 提供了比 SPA 更快的首次加载体验，这对于以内容为中心的网站至关重要。 但这带来了一个折衷：未来的页面导航不能从本地渲染中受益，因此长期用户体验在第一个页面加载后不会受益太多。

#### 服务器路由(MPA) 与 客户端路由(SPA)对比

您的网站路由器在哪里？ 在 服务器路由(MPA) 中，对服务器的每个请求都决定使用哪个 HTML 进行响应，因此路由逻辑存在于服务器中。 在客户端路由(SPA) 中，您的路由器在浏览器中本地运行并劫持任何导航以呈现新页面，而无需访问服务器。

这与上面描述的折衷类似：MPA 提供更快的首次加载体验，而 SPA 可能会在 JavaScript 应用程序在浏览器中完全加载后提供更快的第二或第三页加载。

SPA 还可以跨页面导航提供更强大的转换，因为它们对页面渲染的控制非常多。 为了匹配这种支持，MPA 利用诸如 Hotwire 的 [Turbo](https://turbo.hotwired.dev/) 之类工具，通过控制浏览器中的导航来模拟客户端路由。 HTML 仍然在服务器上呈现，但 Turbo 现在可以显示页面之间的无缝过渡，类似于 SPA 中的客户端路由。

#### MPA 与 SPA

客户端对比状态(SPA，client state management) 是处理复杂、多页面状态管理的网站的高级架构（想想：Gmail）。 这是因为 SPA 将整个网站作为单个 JavaScript 应用程序运行，这使应用程序可以跨多个页面维护状态和内存。 诸如收件箱和管理仪表板之类的交互式数据驱动体验与 SPA 一样出色，因为网站本身本质上是“类似应用程序”的。


## MPA 比 SPA 好吗？

在比较 MPA 和 SPA 时，没有“更好”或“更差”的选择。 这一切都归结为适用场景等因素的考量。

**Astro 优先考虑 MPA 的性能和简单性，因为它对我们以内容为中心的网站的用例最有意义。** 更多有状态、交互重的网站可能会从 SPA 带来的类似应用程序的架构中受益更多，但代价是首次加载性能。

:::note[可访问性(Accessibility)]
MPA 使用标准的 `<a>` 元素进行导航。 这提供了重要的可访问性功能，例如默认管理焦点状态和宣布路线更改。
:::

## 实例探究

以下是我们知道的所有公开的 Astro 比较：

- [Astro vs. SPA (Next.js)](https://twitter.com/t3dotgg/status/1437195415439360003) - 94% less JavaScript
- [Astro vs. SPA (Next.js)](https://twitter.com/jlengstorf/status/1442707241627385860?lang=en) - 34% 更快的加载
- [Astro vs. SPA (Next.js)](https://vanntile.com/blog/next-to-astro) – 65% 网络使用减少
- [Astro vs. SPA (Remix, SvelteKit)](https://www.youtube.com/watch?v=2ZEMb_H-LYE&t=8163s) - "这令人置信的 Google Lighthouse 分数"
- [Astro vs. Qwik](https://www.youtube.com/watch?v=2ZEMb_H-LYE&t=8504s) - 43% 更快的 TTI

如果您知道其他媒体所发布的基准测试但未在此处列出，请创建 PR 以添加它。

## 更多资源

如果您想了解更多信息，Surma (Google) 和 Jake Archibald (Google) 记录了 [关于这个确切主题的精彩来回讨论](https://www.youtube.com/watch?v=ivLhf3hq7eM)
