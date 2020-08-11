---
title: "Data clustering"
description: "了解如何使用Mapbox Maps SDK for Android在地图上显示集群数据。"
prependJs:
  - "import AndroidDeviceFrame from '../../../components/android-device-frame'"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---

通常如果一张地图上显示的数据过多，标记会相互重叠，地图看起来会感觉很杂乱，用户将无法快速理解数据的含义。

通过使用Mapbox Maps SDK for Android的数据驱动样式功能，显示集群数据是完全可能的。

将地图上显示的数据量调整到地图的相机变焦水平，是为用户提供更简洁的UI和位置数据体验的一种方式。


## CircleLayer

对于显示集群数据，我们有两种推荐的方法，其中使用`CircleLayer`是其中一种。不同的圆圈颜色可以代表不同的数据范围。例如，蓝色圆圈可以是一百以上个点的集合，红色圆圈是五十个以上，绿色是十个以上。一旦地图被放大到足够大的程度，就只能看到单个的数据点。


{{
<AndroidDeviceFrame
  imageId="maps-overview-clustering-circle-layer"
  alt=""
  rotation="landscape"
/>
}}

{{
  <RelatedPage
    url="/android/maps/examples/circle-layer-clustering/"
    title="CircleLayer clustering"
    contentType="example">
}}
使用GeoJSON数据和图层来用圆圈集群显示数据。
{{</RelatedPage>}}

## SymbolLayer

`SymbolLayer`稍微复杂一点，但本质上与上面的`CircleLayer`实现相同。根据你使用的`SymbolLayer`图标的形状/大小，您可能需要使用`PropertyFactory`的 `iconTranslate` 方法来确定数据计数 `SymbolLayer` 数字文本在`SymbolLayer` 集群图标上对齐。不同的图标也可以代表不同的数据范围。例如，第一个图标可以是一百以上个点的集合，第二个图标是五十个以上，第三个是十个以上。一旦地图被放大到足够大的程度，就只能看到单个的数据点。

{{
  <RelatedPage
    url="/android/maps/examples/symbol-layer-clustering/"
    title="SymbolLayer clustering"
    contentType="example">
}}
使用GeoJSON数据和图层，用各种图像把数据显示为集群图标。
{{</RelatedPage>}}

{{
<AndroidDeviceFrame
  imageId="maps-overview-clustering-symbol-layer"
  alt=""
  rotation="landscape"
/>
}}

步骤：

1. 使用GeoJSON数据源，并将其作为一个`GeoJsonSource`添加到Mapbox 地图中。

	{{
<AndroidActivityToggle
    id="symbol-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {
    try {
      style.addSource(
        new GeoJsonSource("GEOJSON_SOURCE_ID",
          new URI("URL_POINTING_TO_GEOJSON_FILE"),
          new GeoJsonOptions()
            .withCluster(true)
            .withClusterMaxZoom(MAX_ZOOM)
            .withClusterRadius(DESIRED_CLUSTER_RADIUS)
        )
      );
    } catch (URISyntaxException exception) {
      Log.e(TAG, "Check the URL " + exception.getMessage());
    }
  }
});
`}

kotlin={`
mapboxMap.getStyle {
	try {
	    it.addSource(GeoJsonSource("GEOJSON_SOURCE_ID",
	    URI("URL_POINTING_TO_GEOJSON_FILE"),
	    GeoJsonOptions()
	        .withCluster(true)
	        .withClusterMaxZoom(MAX_ZOOM)
	        .withClusterRadius(DESIRED_CLUSTER_RADIUS)))
	} catch (exception: URISyntaxException) {
	    Log.e(FragmentActivity.TAG, "Check the URL " + exception.message)
	}
}
`}

/>
}}


2. 创建一个`SymbolLayer` ，放置点不聚集时代表单个数据点的图标。只有当地图的相机离地图足够近时，这些图标才会显示出来。记住，地图缩放值越高，相机的放大程度就越高。和等级4相比，等级12时相机离地图更近。
3. 创建尽可能多的`SymbolLayer`或`CircleLayer`用于不同的数据范围。你可以设定，红色的圆圈表示有10-30个数据点的数据集群，蓝色的圆圈表示有50个或更多的数据点的数据集群。数据驱动的样式和`Expression`筛选器将决定在哪个缩放级别显示哪个集群层。
4. 为隐藏的数据数量文本创建一个`SymbolLayer`。也就是说，图标/圆圈上出现的数字会告诉用户这个图标/圆圈后面“隐藏”了多少数据点，如果地图被放大，就可以看到这些数据点。不要忘记可以使用运行样式去调整文本的大小、颜色以及其他属性。

{{
<AndroidActivityToggle
  id="set-properties"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {
    SymbolLayer countLayer = new SymbolLayer("SYMBOL_LAYER_COUNT_LAYER_ID", "GEOJSON_SOURCE_ID");
    countLayer.setProperties(
      textField(Expression.toString(get("point_count"))),
      textSize(TEXT_SIZE),
      textColor(TEXT_COLOR),
      textIgnorePlacement(true),
      textAllowOverlap(true)
    );
    style.addLayer(countLayer);
  }
});
`}

kotlin={`
mapboxMap.getStyle {
	val countLayer = SymbolLayer("SYMBOL_LAYER_COUNT_LAYER_ID", "GEOJSON_SOURCE_ID")
	countLayer.setProperties(
		textField(Expression.toString(get("point_count"))),
		textSize(TEXT_SIZE),
		textColor(TEXT_COLOR),
		textIgnorePlacement(true),
		textAllowOverlap(true))

	it.addLayer(countLayer)
}
`}

/>
}}
