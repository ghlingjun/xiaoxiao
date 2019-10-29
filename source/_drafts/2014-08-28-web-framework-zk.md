---
layout: default
title: Web应用框架-ZK
date: 2014-08-28 01:50:42
tags: zk,web
---

# Web应用框架
Web应用框架（Web application framework）是一种开发框架，用来支持动态网站、网络应用程序及网络服务的开发。其有助于减轻网页开发时共通性活动的工作负荷，例如许多框架提供数据库访问接口、标准样板以及会话管理等，可提升代码的可再用性。

web框架可以分为基于请求的（request-based）和基于组件的（component-based）两大阵营。前者的代表有Struts和Spring MVC等，后者的成员则有JSF、Tapestry等等。

## 对比

基于请求的框架较早出现，它用以描述一个web应用程序结构的概念和传统的静态Internet站点一样，是将其机制扩展到动态内容的延伸。对一个提供HTML和图片等静态内容的网站，网络另一端的浏览器发出以URI形式指定的资源的请求，Web服务器解读请求，检查该资源是否存在于本地，如果是则返回该静态内容，否则通知浏览器没有找到。Web应用升级到动态内容领域后，这个模型只需要做一点修改。那就是web服务器收到一个URL请求（相较于静态情况下的资源，动态情况下更接近于对一种服务的请求和调用）后，判断该请求的类型，如果是静态资源，则照上面所述处理；如果是动态内容，则通过某种机制（CGI、调用常驻内存的模块、递送给另一个进程如Java容器）运行该动态内容对应的程序，最后由程序给出响应，返回浏览器。在这样一个直接与web底层机制交流的模型中，服务器端程序要收集客户端籍get或post方式提交的数据，转换，校验，然后以这些数据作为输入运行业务逻辑后生成动态的内容（包括HTML、JavaScript、CSS、图片等）。

基于组件的框架采取了另一种思路，它把长久以来软件开发应用的组件思想引入到web开发。服务器返回的原本文档形式的网页被视为由一个个可独立工作、重复使用的组件构成。每个组件都能接受用户的输入，负责自己的显示。上面提到的服务器端程序所做的数据收集、转换、校验的工作都被下放给各个组件。现代web框架基本上都采用了模型、视图、控制器相分离的MVC架构，基于请求和基于组件两种类型大都会有一个控制器将用户的请求分派给负责业务逻辑的模型，运算的结果再以某个视图表现出来，所以两大分类框架的区别主要在视图部分，基于请求的框架仍然把视图也就是网页看作是一个文档整体，程序员要用HTML、Javascript和CSS这些底层的代码来写“文档”，而基于组件的框架则把视图看作由积木一样的构件拼成，积木的显示不用程序员操心（当然它们也是由另一些程序员开发出来的），只要设置好它绑定的数据和调整它的属性，把他们大大从编写HTML、Javascript和CSS这些界面的工作中解放出来。
选择

基于请求的和基于组件的两种框架各有优劣。虽然一眼看上去后者有很大的吸引力，普通的web开发人员只要使用专门的公司或开源组织提供的组件就可以轻松开发出好用漂亮的界面，但是有几种因素综合起来不利于这种理想中的方案。要编写一个没有潜在问题的、跨浏览器的、显示美观并且有足够灵活性可以调整的服务器端组件是需要高水平的技能、丰富的经验和较多时间的，即使付出这些成本，也不能完全避免使用者失望的情况。

综合来看，基于请求的框架要程序员自己动手的地方比较多，但也因此可以更精细地控制HTML、CSS和Javascript这些最终决定应用程序界面的代码，特别是如果要在界面上有创新，尝试新的视觉效果和用户操作，必然选择基于请求的框架。基于组件的框架可以提高开发界面的效率，前提是选用的组件质量优秀。

## ZK 框架
一个开源 Ajax 框架

ZK 是一个用 Java™ 代码编写的开源 Asynchronous JavaScript + XML (Ajax) 框架，使用该框架，您无需编写 JavaScript 代码就可以编写一个支持 Web 2.0 的富 Internet 应用程序。Dojo 等典型的 Ajax 框架拥有一些 JavaScript 库，用于公开某些 API 以进行 “Ajax 化” 调用。另一方面，ZK 使用一个基于 XML 的元定义（meta-definition）来定义用户界面。当客户机请求这个页面时，XML 将转化为 HTML 代码。本文将向您介绍 ZK，通过一个真实的示例来展示其使用方法，这个示例运行在 Apache Tomcat 上并连接到 MySQL 数据库。
### 简介

您可以将 ZK 看做是没有 JavaScript 的 Ajax。它包含一个基于 Ajax 的、事件驱动的引擎，一组丰富的 XHTML 和 XUL 元素，一种名为 ZUML 的标记语言，这种语言用于创建特性丰富的用户界面。业务逻辑可以通过 Java 代码直接编写并集成到您的应用程序中，并基于事件或组件触发。ZK 最强大的特性是其丰富的、用于用户界面开发的控件库。有意思吧？

首先，我将更详细地描述前面的术语：

XHTML：可扩展超文本标记语言（Extensible Hypertext Markup Language），是 HTML 和 XML 的结合体，结合了 HTML 的威力和灵活性与 XML 的可扩展性。清单 1 提供了一个 XHTML 代码示例。

清单 1. XHTML 代码示例
```
<?xml version="1.0" encoding="iso-8859-1"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 
Transitional//EN" "DTD/xhtml1-transitional.dtd">
<html xml:lang="en" lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
<title>Hello ZK</title>
</head>
<body>
<h1>Introducing XHTML</h1>
</body>
</html>
```
XUL：XML 用户界面语言（XML User Interface Language），简称 XUL（发音同 “Zool”），是一种由 Mozilla 研发的标记语言，一个用于描述图形用户界面的 XML 应用程序。XUL 能够创建多种元素，比如输入控件、工具栏、菜单、树状图、键盘快捷键等。清单 2 展示了一个 XUL 代码示例。 

清单 2. XUL 代码示例
```
<?xml version="1.0"?>
<?xml-stylesheet href="chrome://global/skin/" type="text/css"?>
<window id="main" title="My App" width="300" height="300"
xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
<caption label="Hello World"/>
</window>
```

ZUML：ZK 用户界面标记语言（ZK User Interface Markup Language），用于定义富用户界面。由于它基于 XML，因此每个元素都描述组件，而属性描述组件值。清单 3 展示了一个 ZUML 代码示例。 
清单 3. ZUML 代码示例
```
<window title="Hello ZUML" border="normal">
Hello World!
</window>
```

## 获取 ZK
获取和安装 ZK 非常简单。ZK 文档网站上包含大量关于库和如何建立文件夹结构的文档。因此，获取 ZK（包括运行 hello world 应用程序）应该非常简单。 

## 为何要使用 ZK？

ZK 是一个直接 Ajax 实现 — 或者换句话说，一个以服务器为中心的模型。ZK 与其他框架不同，其他框架包含大量令人眼花缭乱的 Ajax 调用细节。另外，Ajax 调用需要大量使用 JavaScript 和相关知识，以便在浏览器（客户机）上操作 Document Object Model (DOM) 并在客户机/服务器通信过程中同步数据。ZK 消除了这些复杂性，使您能够专注于业务逻辑。ZK 的其他好处包括： 
丰富的用户界面。 
- Web 服务访问。 
- 组件数据绑定。 
- 简单但强大的标记语言 ZUML。 
- 由于没有客户机代码，因此具有高度的可维护和可扩展性。 
- 高度易用性。 
- 提高开发人员生产力。

## ZK 应用
为理解 ZK 的工作方式，我们来看一个真实示例。这个示例是一个客户管理应用程序，用户可以通过它进行各种操作，比如添加新客户，编辑客户数据，以及数据库中的客户条目的软删除（soft deletion）。但是，在深入代码之前，我将描述几个通过 ZK 生成的用户界面屏幕。检查过这些屏幕之后，我将描述 ZK 的架构，它是生成这个出色 UI 的底层引擎。最后，我将介绍这个应用程序使用的详细代码和配置参数。
