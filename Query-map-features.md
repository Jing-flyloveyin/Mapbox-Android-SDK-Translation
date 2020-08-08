---
title: "Query map features"
description: "Official documentation about querying map features within the Mapbox Maps SDK for Android. Discover how to retrieve information about a selected place of interest."
prependJs:
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---

The Maps SDK allows you to query map layers and return a list of [GeoJSON `Feature`s](/android/java/overview/geojson/#feature-and-featurecollection) that include information about the `Feature`'s geometry and properties. For example, a user can query map features by tapping the map and return any POIs that exist at that point as a GeoJSON `Feature`. Then, you can access the properties in the feature, including the POI's name as a `String`. Querying the map won't always return the information that you are looking for. It is possible to receive a `List<Feature>` with 0 features in it.

Maps SDK 为您提供查询地图层的工具以获取[GeoJSON`要素`](/android/java/overview/geojson/#feature-and-featurecollection)的列表，保存着`要素`的位置与属性信息。其中一个示例用法是，用户可以通过点击地图上的位置，查询显示地图上以GeoJSON`要素`形式存储的POI。随后可获得相关要素的属性信息，包括以String格式储存的 POI 名称等。需要注意的是，由于地图查询操作并不总是能够返回用户查找的信息，因此可能收到包含0 个要素的`要素列表`。

## How querying works 地图查询方法

You can query the map for features that exist at a `Point` or within a `BoundingBox`. Aside from layers, it is also possible to query the source for specific information matching your query regardless if the items are being displayed on the map.
您可以在地图上查询位于某一`点`或 特定`边界框 `内的要素。除显示的图层外，无论被查询的数据是否实际显示在地图上，您都可以根据自身需求对其执行查询操作。


Because features come from vector tile (or GeoJSON data that is converted to tiles internally), the query may split feature geometries or duplicate them across tile boundaries. As a result, features may appear multiple times in query results.
要素主要来源为矢量切片（或内部转换为切片的GeoJSON数据），因此在查询过程中可能会出现要素被拆分、或在切片边界重复出现的情况，导致同一要素在查询结果中多次出现。

For example, when querying by bounding box in an area that includes a highway spanning multiple tiles, the query will return a separate feature for every tile the highway spans. The geometry of each feature will be limited to the part of the highway that exists on that tile. Similarly, a point feature near a tile boundary may appear in multiple tiles due to tile buffering.
例如，当在一个区域内通过边界框查询时，如果该区域包括一条横跨多个切片的公路，则查询将为该公路横跨的每个切片返回一个单独的要素。各要素的几何形状将被限制描述为这条公路在该切片内存在的部分。同样，由于切片的缓冲作用，切片边界附近的某些点要素可能会出现在相邻的多个切片中。

## Query rendered features 查询渲染的要素

Use `queryRenderedFeatures` to return all map features currently rendered on the device. Features must be visible in the device's viewport **and** fully rendered before you can access them.

使用`查询渲染的要素`功能可以返回当前设备上渲染的所有地图要素。查询需要满足的条件包括，要素必须在设备的视区中可见，**以及**在完全渲染后才能够被访问查询。


### Query at a point

`queryRenderedFeatures` only accepts a screen pixel value instead of `LatLng`, so in many cases you'll need convert screen position to geographic position. In the example below, when the map is clicked it provides a `LatLng` that is used to get the features at that point on the map.

查询地图只接受屏幕像素值而不是 `LatLng`。因此可能时常需要对`LatLng`事先进行转换。在以下代码段中，单击地图将提供一个`LatLng`数值，我们将其用来查询地图并获取该位置的属性。

{{
<AndroidActivityToggle
  id="query-features"

java={`
@Override
public void onMapClick(@NonNull LatLng point) {

  // Convert LatLng coordinates to screen pixel and only query the rendered features.
  final PointF pixel = mapboxMap.getProjection().toScreenLocation(point);

  List<Feature> features = mapboxMap.queryRenderedFeatures(pixel);

  // Get the first feature within the list if one exist
  if (features.size() > 0) {
    Feature feature = features.get(0);

    // Ensure the feature has properties defined
    if (feature.properties() != null) {
      for (Map.Entry<String, JsonElement> entry : feature.properties().entrySet()) {
        // Log all the properties
        Log.d(TAG, String.format("%s = %s", entry.getKey(), entry.getValue()));
      }
    }
  }
}
`}

kotlin={`
override fun onMapClick(point: LatLng) {

	// Convert LatLng coordinates to screen pixel and only query the rendered features.
	val pixel = mapboxMap.projection.toScreenLocation(point)
	val features = mapboxMap.queryRenderedFeatures(pixel)
	
	// Get the first feature within the list if one exist
	if (features.size > 0) {
	val feature = features[0]
	
	// Ensure the feature has properties defined
	for ((key, value) in feature.properties()!!.entrySet()) {
	    // Log all the properties
	    Log.d(TAG, String.format("%s = %s", key, value))
	}
}
`}

/>
}}


Querying rendered features all layers:

{{
<AndroidActivityToggle
  id="query-method"

java={`
List<Feature> features = mapboxMap.queryRenderedFeatures(pixel);
`}

kotlin={`
val features = mapboxMap.queryRenderedFeatures(pixel)
`}

/>
}}

Querying rendered features in a specific layer:

{{
<AndroidActivityToggle
  id="query-method-with-layer-id"

java={`
// You can pass in a single layer id or a list of layer ids
List<Feature> features = mapboxMap.queryRenderedFeatures(pixel,"LAYER-ID");
`}

kotlin={`
// You can pass in a single layer id or a list of layer ids
val features = mapboxMap.queryRenderedFeatures(pixel,"LAYER-ID")
`}

/>
}}

{{
  <RelatedPage
    url="/android/maps/examples/query-a-map-feature/"
    title="Query at point"
    contentType="example">
}}
Query the rendered map to get the properties at a specific location.
{{</RelatedPage>}}

### Query inside a bounding box 查询边界框内的要素

To query the map for `Feature`s in an area, pass in a bounding box using a `RectF` object. This can either come from a Android `View` displayed to the user on top of the map or four coordinates that are shown within the viewport.

如需在地图上查询某一区域内的 `要素`，可通过使用 `RectF`对象传递边界框来实现。边界框既可以来自于地图顶部显示给用户的Android`视图`，也可以是视区域内显示的四个坐标。


The example below shows how to use four coordinates to create a `RectF`. The `RectF` object is passed into `queryRenderedFeatures()`.

以下代码段显示如何获取四个坐标，将它们转换为新的`RectF`对象。这个`RectF`对象随后将被传递到 `queryRenderedFeatures()`中。

{{
<AndroidActivityToggle
  id="rect-f"

java={`
RectF rectF = new RectF(
	mapView.getLeft(),
	mapView.getTop(),
	mapView.getRight(),
	mapView.getBottom()
);
mapboxMap.queryRenderedFeatures(rectF);
`}

kotlin={`
val rectF = RectF(
	mapView.left.toFloat(),
	mapView.top.toFloat(),
	mapView.right.toFloat(),
	mapView.bottom.toFloat()
)
mapboxMap.queryRenderedFeatures(rectF)
`}
/>
}}

{{
  <RelatedPage
    url="https://docs.mapbox.com/android/maps/examples/count-features-in-a-selected-area/"
    title="Query region"
    contentType="example">
}}
Query the rendered map to get the features found inside an Android view.
{{</RelatedPage>}}

## Query source features 查询数据源的要素

`querySourceFeatures()` returns all features that match the query parameters regardless of whether or not the feature is currently rendered on the map. The domain of the query includes all currently-loaded vector tiles and GeoJSON source tiles. This function does not check tiles outside of the visible viewport.

使用 `querySourceFeatures()`函数查询后，所有与查询参数匹配的要素都将被返回，无论它们当前是否由被渲染可见。查询的域包括所有当前加载的矢量切片和 GeoJSON 源切片。此函数不会检索当前可见视区之外的切片。


To query a source, you must pass in the query parameters as a set of `Filters` and only the features that satisfy the statement will be added to the returning list of features. In the example below, the map style contains a GeoJSON source called `population-source`, which contains a `population` property for each feature. The query below limits the features returned to those that have a population greater than 100,000.

若要查询数据源，您必须将查询参数传递为一组`Filters`，只有满足该查询参数表述的要素才会添加至返回的要素列表中。例如，在以下代码段中，地图样式包含名为`population-source`的 GeoJSON 源，它包含了每个要素的`population`属性。以下查询希望实现仅返回 population属性大于100,000的要素列表。

{{
<AndroidActivityToggle
  id="query-source-features"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	GeoJsonSource source = style.getSourceAs("population-source");
	if (source != null){
		List<Feature> features = source.querySourceFeatures(Expression.get("population"));
	}    
  }
});
`}

kotlin={`
mapboxMap.getStyle {
    val source = it.getSourceAs<GeoJsonSource>("population-source")

    val features = source?.querySourceFeatures(Expression.get("population"))
}
`}
/>
}}
