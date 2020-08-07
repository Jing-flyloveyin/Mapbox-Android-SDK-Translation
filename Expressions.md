---
title: "Expressions"
description: "The Mapbox Maps SDK for Android."
contentType: guide
---

就像其余的Mapbox开发人员经验一样，表达式都是关于细粒度控制的。 表达式使您可以根据数据的属性动态设置空间数据的样式。 它们为您提供了许多灵活而强大的操作。 您可以一次为具有多个要素属性的数据设置样式，应用条件逻辑，并通过算术或字符串操作来处理数据，以在数据与样式之间建立更复杂的关系。

可以将任何布局属性，绘画属性或过滤器的值指定为表达式。 表达式定义了一个公式，用于使用以下描述的运算符来计算属性的值。

Mapbox GL提供的表达式运算符集包括：

- 用于对数值执行算术和其他运算的数学运算符
- 逻辑运算符，用于处理布尔值和做出条件决策
- 字符串运算符
- 数据运算符，提供对源要素属性的访问
- 相机操作符，可访问定义当前地图视图的参数

表达式表示为JSON数组。 表达式数组的第一个元素是命名表达式运算符的字符串。 例如，“ *”或“ case”。 接下来的元素（如果有）是表达式的参数。 每个参数都是文字值（字符串，数字，布尔值或null）或另一个表达式数组。

## 流行表达

尽管有很多表达式可供选择，但是您首先应该熟悉以下表达式，这些表达式可能会帮助您实现数据驱动的样式目标：

- get
- match
- switchCase
- equal
- literal

## 学习资源

这里有多个Mapbox资源可以为您提供更多指导和示例，以帮助您理解表达式的概念。

**Javadocs** – [The `Expressions` Java class in the Mapbox Maps SDK for Android](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/java/com/mapbox/mapboxsdk/style/expressions/Expression.java) 提供了说明和代码示例，以解释应如何使用每种表达方法。

**The Mapbox Android demo app** – [The app's data-driven styling folder](https://github.com/mapbox/mapbox-android-demo/tree/master/MapboxAndroidDemo/src/main/java/com/mapbox/mapboxandroiddemo/examples/dds) 有很多示例以各种方式使用表达式。没有该应用程序？ [Click here to download it](https://play.google.com/store/apps/details?id=com.mapbox.mapboxandroiddemo&hl=en).


**Introductory blog post** – [Read our blog post that was published to introduce expressions when they were released for Mapbox GL JS](https://blog.mapbox.com/announcing-expressions-in-gl-js-a72b55d0a6af). 这篇文章没有与Android平台相关的示例/语法，但对理解表达式的概念以及Mapbox为何首先引入表达式很有帮助。

**Style specification** – 想要有关每个表达式的详细信息吗？ [The Mapbox Style Specification](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions) has them.
