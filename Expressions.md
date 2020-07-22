---
title: "Expressions"
description: "The Mapbox Maps SDK for Android."
contentType: guide
---

Like the rest of the Mapbox developer experience, expressions are all about fine-grain control. Expressions enable you to dynamically style spatial data based on the data's properties. They offer you many flexible and powerful operations. You can style data with multiple feature properties at once, apply conditional logic, and manipulate data with arithmetic or string operations for a more sophisticated relationship between your data and how it is styled.

The value for any layout property, paint property, or filter may be specified as an expression. An expression defines a formula for computing the value of the property using the operators described below.

The set of expression operators provided by Mapbox GL includes:

- Mathematical operators for performing arithmetic and other operations on numeric values
- Logical operators for manipulating boolean values and making conditional decisions
- String operators for manipulating strings
- Data operators, providing access to the properties of source features
- Camera operators, providing access to the parameters defining the current map view

Expressions are represented as JSON arrays. The first element of an expression array is a string naming the expression operator. For example, "*" or "case". The next elements (if any) are the arguments to the expression. Each argument is either a literal value (a string, number, boolean, or null), or another expression array.

## Popular expressions

While there are many expressions to choose from, you should first become familiar with the following expressions that will likely help you achieve your data-driven styling goals:

- get
- match
- switchCase
- equal
- literal

## Learning resources

There are several Mapbox resources that can provide more guidance and examples for you to understand the concept of expressions.

**Javadocs** – [The `Expressions` Java class in the Mapbox Maps SDK for Android](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/java/com/mapbox/mapboxsdk/style/expressions/Expression.java) offers explanations and code samples to explain how each expression method should be used.

**The Mapbox Android demo app** – [The app's data-driven styling folder](https://github.com/mapbox/mapbox-android-demo/tree/master/MapboxAndroidDemo/src/main/java/com/mapbox/mapboxandroiddemo/examples/dds) has many examples that use expressions in various ways. Don't have the app? [Click here to download it](https://play.google.com/store/apps/details?id=com.mapbox.mapboxandroiddemo&hl=en).


**Introductory blog post** – [Read our blog post that was published to introduce expressions when they were released for Mapbox GL JS](https://blog.mapbox.com/announcing-expressions-in-gl-js-a72b55d0a6af). The post doesn't have examples/syntax related for the Android platform, but it is helpful for understanding the concept of expressions and _why_ Mapbox introduced expressions in the first place.

**Style specification** – Want the minute details about each expression? [The Mapbox Style Specification](https://www.mapbox.com/mapbox-gl-js/style-spec/#expressions) has them.