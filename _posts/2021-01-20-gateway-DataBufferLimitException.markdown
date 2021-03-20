---
layout: post
title:  "gateway DataBufferLimitException"
date:   2021-01-21 15:03:36 +0530
gateway DataBufferLimitException Exceeded limit on max bytes to buffer : 262144​
categories: gateway DataBufferLimitException!
---
spring.codec.max-in-memory-size=5242880 设置无效
spring.codec.max-in-memory-size=5242880 设置无效
首选这里感谢下大神 theClouds的文章 http://theclouds.io/databufferlimitexception-262144/
顺利定位到自己问题并解决了问题
DefaultClientResponse clientResponse = new DefaultClientResponse(responseAdapter, ExchangeStrategies.withDefaults());
ExchangeStrategies.withDefaults()//每次都是重新初始化默认大小，所以怎么设置大小都无效


