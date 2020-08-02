---
title: "Annotations"
description: "Looking to annotate a map with the Mapbox Maps SDK for Android. Read this documentation to learn about markers, info windows, lines, polygons, plus more."
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

## Markers

Markers are useful when identifying a single point on the map. The SDK comes with a default marker icon which can be configured to fit your specific needs. APIs are exposed to optionally change this icon to any bitmap image you wish. To create a marker for you map, you are only required to provide a `LatLng` position which defines where the marker will be placed on the map. Call `mapboxMap.addMarker()` to add the marker to the map.

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


Besides providing the position, you can also add a title and snippet which display inside of an [info window](#info-window). The info window is displayed when users tap on the marker and close when they tap outside of the info window.

Add a list of markers using `mapboxMap.addMarkers()` if you have many markers or are loading them from a GeoJSON file.

{{<RelatedPage url="https://docs.mapbox.com/playground/marker/" title="Mapbox Marker Playground" contentType="playground">}}
Use the Marker Playground to generate sample code for adding a single marker to a map using the Maps SDK for Android.
{{</RelatedPage>}}

### Removing markers

The Mapbox Android SDK comes with two methods for removing markers. If you'd like to remove a particular marker, use `mapboxMap.removeMarker()` while passing in the marker object to be removed. If you would like to remove _all_ markers, call the `mapboxMap.clear()` method. Note that this will also remove any polylines and polygons you’ve added to your map.

### Customize marker icon

You can specify a custom icon by using the `IconFactory` object and passing it to the marker. The default marker icon's used if you don’t specify an icon while creating your marker. The anchoring of the marker will be in the center, meaning if you have an icon with a point, you'll need to add padding to the bottom of the image.

Place your custom marker image in your project’s drawable folder and note its file name. In the example below, the custom icon’s image file's called `blue_marker.png`

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

### Capturing marker events

The Mapbox Maps SDK for Android provides a handy listener for capturing when a user taps on a marker. By default, all markers come with an `onMarkerClick` event listener for displaying and hiding info windows. You can override this default event listener and set your own with the `setOnMarkerClickListener` method.

To display a toast message with the clicked marker’s title, listen for a click event with `setOnMarkerClickListener` and finally call `Toast.makeText()`. To prevent displaying a toast message and an info window at the same time, return true at the end:

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


### Update a marker

If you have intentions to update a marker rather than completely removing it, the SDK provides a few update methods. Using these mean less boilerplate code and an increase in performance since you are only updating the marker. Using these update APIs, you can create animating markers using a [`ValueAnimator`](https://developer.android.com/reference/android/animation/ValueAnimator.html) for example. You can find the APIs for updating either the marker position or icon bitmap inside your marker object reference.

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
Use a `ValueAnimator` to animate a marker between two positions.
{{</RelatedPage>}}

## Polyline and polygons

Adding a line or polygon to your map is like adding a marker. Due to the nature of these objects, different APIs are exposed, such as polygon color or line width. Instead of taking in a single position, bundle all your `LatLng` objects inside of a `List` and then pass them in using the `addAll()` method.

### Draw a polyline on the map

Make sure that the first and last `Point` locations are the same.

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

### Draw a polygon on the map

Make sure that the first and last `Point` locations are the same.

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


### Use line and fill layers

To use the `addPolyline()` or `addPolygon()` methods in the code above, you'll have a list of `LatLng` objects which represent the line or the polygon area. As explained at the top of this page, using sources and layers gives you much more flexibility to show geographic data on your map. With your list of `LatLng` objects, you could create a `FeatureCollection` and use that `FeatureCollection` to create a `GeoJsonSource`. Feed the `GeoJsonSource` to:

-  a `LineLayer` to show the line that you'd otherwise draw via `addPolyline()`.
-  a `FillLayer` to show the area that you'd otherwise draw via `addPolygon()`.
