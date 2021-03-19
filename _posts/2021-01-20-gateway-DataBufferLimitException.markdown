---
layout: post
title:  "将swagger2接口 导出成文档 文档类型可以为html pdf"
date:   2019-09-24 21:03:36 +0530
categories: swagger2 导出文档 html pdf
---
今天正好需要整理接口文档，所以就想偷个懒看看能否通过swagger来导出，网上查看了一些前辈们的资料发现还是蛮方便的，这里也留个足迹方便记忆。
首选说使用swagger2markup 导出adoc文档，我们需要依赖引入，这中间可能也会涉及到一些jar包冲突的问题，网上都能搜到可以自行解决（建议google搜）。

这里主要看 springfox-swagger2 依赖里的 swagger-models swagger-core的版本，我这里将是1.5.9 我讲版本换成了1.5.16

```javascript
   <dependency>
       <groupId>io.swagger</groupId>
       <artifactId>swagger-models</artifactId>
       <version>1.5.16</version>
   </dependency>
   <dependency>
       <groupId>io.swagger</groupId>
       <artifactId>swagger-core</artifactId>
       <version>1.5.16</version>
   </dependency>
```

