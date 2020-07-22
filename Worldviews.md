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

In Mapbox services, the worldview data field is used to identify geographic features whose characteristics are defined differently by audiences belonging to various regional, cultural, or political groups.

Please see the [Mapbox glossary's `worldview` entry](https://docs.mapbox.com/help/glossary/worldview) for more information about currently available worldviews and required filters in the Mapbox Style Specification.

{{
  <RelatedPage
    url="https://github.com/mapbox/mapbox-android-demo/blob/master/MapboxAndroidDemo/src/main/java/com/mapbox/mapboxandroiddemo/examples/styles/KotlinWorldviewSwitchActivity.kt"
    title="Toggle world views"
    contentType="example">
}}
Cycle through worldviews to see how borders and other map data visually change.
{{</RelatedPage>}}