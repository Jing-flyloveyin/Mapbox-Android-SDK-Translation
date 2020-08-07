---
title: "Worldviews"
description: "Learn how to use the Mapbox Maps SDK for Android to adjust the map's borders to match a world view."
prependJs:
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
contentType: guide
language:
- Java
- Kotlin
---

在Mapbox服务中，worldview数据字段用于标识地理特征，这些地理特征的特征由属于各个区域，文化或政治团体的受众不同地定义。

请参阅 [Mapbox glossary's `worldview` entry](https://docs.mapbox.com/help/glossary/worldview) 以获取有关当前可用的worldview和Mapbox样式规范中所需过滤器的更多信息。

{{
  <RelatedPage
    url="https://github.com/mapbox/mapbox-android-demo/blob/master/MapboxAndroidDemo/src/main/java/com/mapbox/mapboxandroiddemo/examples/styles/KotlinWorldviewSwitchActivity.kt"
    title="切换worldview"
    contentType="例">
}}
循环浏览切换worldview，来查看边界和其他地图数据的视觉变化。
{{</RelatedPage>}}
