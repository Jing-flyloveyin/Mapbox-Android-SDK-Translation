---
title: "标注"
description: "阅读本文档，了解通过Mapbox Maps SDK for Android在地图上使用标记、信息窗口、线条、多边形等内容。"
prependJs:
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import Note from '@mapbox/dr-ui/note';"
contentType: guide
language:
- Java
- Kotlin
---

Mapbox Maps SDK for Android提供几种不同的方法来标记单个点、创建一个圆、在多个点之间添加线条或绘制一个多边形。 通常，这些对象的绘制是在地图顶部或地图本身（某些情况下）中进行的。

{{<Note theme="warning" title="标注已经被弃用，并由Mapbox标注插件for Android取代">}}
随着Mapbox Maps SDK版本7.0.0的发布，此页面上的许多代码已经被弃用。 **需要在地图上添加图标、文本、线条或多边形，请使用 [Mapbox标注插件for Android](/android/plugins/overview/annotation/). ** 标注插件简化了标注操作并且在展示数据方面有更好的灵活性。

此页面上诸如 `Polygon`, `Polyline`，和 `Marker` 等类将不会继续存在。这也意味着您不应该再使用 `PolygonOptions` 和 `PolylineOptions` 这些类和 `addPolygon()`、 `addPolyline()` 和 `addMarker()` 等方法。
{{</Note>}}

## 数据源和地图层

在Mapbox地图中展示数据的最高效的方式是将 [数据源](/android/maps/overview/data-driven-styling/#sources) 和 [地图层](/android/maps/overview/data-driven-styling/#layers) 组合使用。这种组合还可以使您更精细地控制：

- 代表单个点的图标
- 线条
- 多边形

请参阅 [数据驱动样式](/android/maps/overview/data-driven-styling/) 页面了解更多信息。您可以探索通过组合数据源和图层进行标注的方式。您将在下方找到相对简单（但是性能较差）的关于显示标注的信息。

## 标记

标记在识别地图上的单个点时很有用。该 SDK 附带一个默认标记图标, 您可以配置该图标来满足您的特定需求。有API可以将图标更改为您希望的任何位图图片。要为您的地图创建标记，您只需提供 `LatLng` 置，它定义了地图上放置标记的位置。通过调用 `mapboxMap.addMarker()` 可以将标记真正添加到地图中。

{{
<AndroidActivityToggle
  id="add-a-marker"

java={`
mapboxMap.addMarker(new MarkerOptions()
	.position(new LatLng(48.85819, 2.29458))
	.title("Eiffel Tower"));
`}
kotlin={`
mapboxMap?.addMarker(MarkerOptions()
	.position(LatLng(48.85819, 2.29458))
	.title("Eiffel Tower"))
`}

/>
}}


除了提供该位置之外，您还可以为 [“信息”窗口](#info-window) 添加需要显示的标题和信息。用户点击标记时，将显示“信息”窗口；用户在“信息”窗口外点击时将关闭窗口。

如果您有许多标记或者从 GeoJSON 文件加载标记，请使用 `mapboxMap.addMarkers()` 来添加标记列表。

{{<RelatedPage url="https://docs.mapbox.com/playground/marker/" title="Mapbox Marker Playground" contentType="playground">}}
在Marker Playground中生成适用于Maps SDK for Android的在地图中添加单个标记的示例代码。
{{</RelatedPage>}}

### 移除标记

Mapbox Android SDK 附有两种方法来移除标记。如果要移除特定标记, 请使用 `mapboxMap.removeMarker()` 方法并将要删除的标记对象作为参数传入。 如果您想移出 _所有_ 标记，请调用 `mapboxMap.clear()` 方法。请注意，这也将移除您添加到地图的任何折线和多边形。

### 自定义标记图标

您可以使用 `IconFactory` 对象并将其传递给标记来指定自定义图标。在创建标记时，如果未指定图标, 将会使用默认标记图标。标记锚定在中心，意味着如果您有一个带有点的图标，则需要向图像底部进行填充。

将自定义标记图像放在项目的可绘制文件夹中，并记下其文件名。在以下示例中，自定义图标的图片文件被称为 `blue_marker.png`

{{
<AndroidActivityToggle
  id="customize-a-marker"

java={`
// Create an Icon object for the marker to use
IconFactory iconFactory = IconFactory.getInstance(MainActivity.this);
Icon icon = iconFactory.fromResource(R.drawable.blue_marker);

// Add the marker to the map
mapboxMap.addMarker(new MarkerViewOptions()
	.position(new LatLng(-37.821648, 144.978594))
	.icon(icon));
`}
kotlin={`
// Create an Icon object for the marker to use
val icon = IconFactory.getInstance(this)
icon.fromResource(R.drawable.blue_marker)

// Add the marker to the map
mapboxMap?.addMarker(MarkerViewOptions()
	.position(LatLng(-37.821648, 144.978594))
	.icon(icon))
`}

/>
}}

### 捕获标记事件

用户点击标记时，Mapbox Maps SDK for Android 提供一个方便的监听器来进行捕获。 默认情况下，所有标记都随附一个 `onMarkerClick` 事件监听器来显示和隐藏信息窗口。您可以覆盖此默认事件侦听器，并使用 `setOnMarkerClickListener` 方法设置自己的事件监听器。

如果要在用户点击标记时显示带有标记标题的Toast消息，请使用 `setOnMarkerClickListener` 方法监听时间并在最后调用 `Toast.makeText()`方法。要防止 Toast 消息和信息窗口的同时显示 ，请在末尾返回 true：

{{
<AndroidActivityToggle
  id="capturing-marker-events"

java={`
mapboxMap.setOnMarkerClickListener(new MapboxMap.OnMarkerClickListener() {
  @Override
  public boolean onMarkerClick(@NonNull Marker marker) {

  // Show a toast with the title of the selected marker
    Toast.makeText(this, marker.getTitle(), Toast.LENGTH_LONG).show();
    return true;
  }
});
`}
kotlin={`
mapboxMap.setOnMarkerClickListener { marker ->

// Show a toast with the title of the selected marker
Toast.makeText(this, marker.getTitle(), Toast.LENGTH_LONG).show()
}
`}

/>
}}


### 更新标记

如果您打算更新标记而不是完全将其移除，SDK 提供了一些更新方法。仅对标记进行更新，会减少代码并提高性能。您可以使用这些用于更新标记的 API，如通过 [`ValueAnimator`](https://developer.android.com/reference/android/animation/ValueAnimator.html) 来创建动画标记。这些API能够更新标记对象内部的标记位置或图标位图。

{{
<AndroidActivityToggle
  id="example-code"

java={`
// Change the marker location
marker.setPosition(new LatLng(-37.822884, 144.981916));

// Update the marker icon
marker.setIcon(icon);
`}
kotlin={`
// Change the marker location
marker.setPosition(new LatLng(-37.822884, 144.981916));

// Update the marker icon
marker.setIcon(icon);
`}

/>
}}

{{<RelatedPage url="/android/maps/examples/animate-marker-position/" title="Animate marker" contentType="example">}}
使用 `ValueAnimator` 以在两个位置的标记间添加动画。
{{</RelatedPage>}}

## 折线和多边形

向地图中添加线条或多边形，跟添加一个标记类似。由于这些对象各自的性质，SDK提供了不同的 API，例如多边形颜色或线条宽度。与采用单一位置不同的是，需要将所有 `LatLng` 对象放入 `List` 中，然后使用 `addAll()` 方法将它们传入。

### 在地图上绘制一条折线

请确保第一个和最后一个 `点` 位置相同。

{{
<AndroidActivityToggle
  id="draw-a-polyline"

java={`
mapboxMap.addPolyline(new PolylineOptions()
	.addAll(points)
	.color(Color.parseColor("#3bb2d0"))
	.width(2));
`}

kotlin={`
mapboxMap.addPolyline(PolylineOptions()
	.addAll(points)
	.color(Color.parseColor("#3bb2d0"))
	.width(2));
`}

/>
}}

### 在地图上绘制一个多边形

请确保第一个和最后一个 `点` 位置相同。

{{
<AndroidActivityToggle
  id="draw-polygon-on-map"

java={`
List<LatLng> polygonLatLngList = new ArrayList<>();

polygonLatLngList.add(new LatLng(45.522585, -122.685699));
polygonLatLngList.add(new LatLng(45.534611, -122.708873));
polygonLatLngList.add(new LatLng(45.530883, -122.678833));
polygonLatLngList.add(new LatLng(45.547115, -122.667503));
polygonLatLngList.add(new LatLng(45.530643, -122.660121));
polygonLatLngList.add(new LatLng(45.533529, -122.636260));
polygonLatLngList.add(new LatLng(45.521743, -122.659091));
polygonLatLngList.add(new LatLng(45.510677, -122.648792));
polygonLatLngList.add(new LatLng(45.515008, -122.664070));
polygonLatLngList.add(new LatLng(45.502496, -122.669048));
polygonLatLngList.add(new LatLng(45.515369, -122.678489));
polygonLatLngList.add(new LatLng(45.506346, -122.702007));
polygonLatLngList.add(new LatLng(45.522585, -122.685699));

mapboxMap.addPolygon(new PolygonOptions()
.addAll(polygonLatLngList)
.fillColor(Color.parseColor("#3bb2d0")));
`}

kotlin={`
val polygonLatLngList = ArrayList<LatLng>()

polygonLatLngList.add(LatLng(45.522585, -122.685699))
polygonLatLngList.add(LatLng(45.534611, -122.708873))
polygonLatLngList.add(LatLng(45.530883, -122.678833))
polygonLatLngList.add(LatLng(45.547115, -122.667503))
polygonLatLngList.add(LatLng(45.530643, -122.660121))
polygonLatLngList.add(LatLng(45.533529, -122.636260))
polygonLatLngList.add(LatLng(45.521743, -122.659091))
polygonLatLngList.add(LatLng(45.510677, -122.648792))
polygonLatLngList.add(LatLng(45.515008, -122.664070))
polygonLatLngList.add(LatLng(45.502496, -122.669048))
polygonLatLngList.add(LatLng(45.515369, -122.678489))
polygonLatLngList.add(LatLng(45.506346, -122.702007))
polygonLatLngList.add(LatLng(45.522585, -122.685699))

mapboxMap.addPolygon(PolygonOptions()s
	.addAll(polygonLatLngList)
	.fillColor(Color.parseColor("#3bb2d0")))
`}

/>
}}


### 使用线条并填充图层

要在以上代码中使用 `addPolyline()` 或 `addPolygon()` 方法，您将拥有表示该线条或多边形区域的 `LatLng` 对象的列表。正如本页顶部所说明的那样，使用数据源和图层，可以让您在地图上更灵活地显示地理数据。您可以使用 `LatLng` 对象的列表来创建 `FeatureCollection` 并使用该 `FeatureCollection` 来创建 `GeoJsonSource`。将 `GeoJsonSource` 传递给：

-  一个 `LineLayer` 层，从而显示您通过 `addPolyline()`绘制的线条。
-  一个 `FillLayer` 层，从而显示您通过 `addPolygon()`绘制的区域。
