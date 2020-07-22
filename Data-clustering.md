---
title: "Data clustering"
description: "Learn how to use the Mapbox Maps SDK for Android to show clustered data on the map."
prependJs:
  - "import AndroidDeviceFrame from '../../../components/android-device-frame'"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---

Often, a map can show too much data at a single time. Markers overlap with each other. The map looks and feels cluttered. Users can't get a quick understanding of what the data is supposed to say.

Showing clustered data is entirely possible by using the data-driven styling capabilities of the Mapbox Maps SDK for Android.

Adjusting the amount of data shown on the map to the map's camera zoom level, is a way to provide users with a cleaner UI experience and less overwhelming location data experience.


## CircleLayer

Using `CircleLayer`s is one way of two recommended ways to show data clustering. Different circle colors can represent the various data ranges. For example, blue circles might be clusters with 100+ data points, red circles with 50+, and green circles with 10+. Once the map is zoomed in enough, only individual data points would be visible.


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
Use GeoJSON data and layers to show data with circle clusters.
{{</RelatedPage>}}

## SymbolLayer

The `SymbolLayer` is a bit more complicated but essentially the same as the `CircleLayer` implementation above. Depending on the shape/size of the `SymbolLayer` icons that you use, you might have to use the`PropertyFactory`'s `iconTranslate` method to make sure that the data count `SymbolLayer` number text is lined up directly on top of the `SymbolLayer` cluster icons. Different icons could represent the various data ranges. For example, one image could be clusters with 100+ data points, a second image with 50+, and third image with 10+. Once the map is zoomed in enough, only individual data points would be visible

{{
  <RelatedPage
    url="/android/maps/examples/symbol-layer-clustering/"
    title="SymbolLayer clustering"
    contentType="example">
}}
Use GeoJSON data and layers to show data with various images as the cluster icons.
{{</RelatedPage>}}

{{
<AndroidDeviceFrame
  imageId="maps-overview-clustering-symbol-layer"
  alt=""
  rotation="landscape"
/>
}}

To do this:

1. Use a GeoJSON data source and add it to the Mapbox map as a `GeoJsonSource`.

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


2. Create a `SymbolLayer` with icons that represent the individual data points for when points are not clustered. These icons will only be visible when the map's camera is close enough to the map. Remember, the higher the map zoom value, the more zoomed in the camera is. A zoom level of 12 is closer to the map than a zoom value of 4.
3. Create as many additional `SymbolLayer`s or `CircleLayer`s as you want for the various data ranges. You might have red circles represent data clusters that have 10-30 data points and then blue circles that have 50 or more data points. Data-driven styling and `Expression` filtering will determine which cluster layers are shown at which zoom level.
4. Create a `SymbolLayer` for the hidden data amount text. That is, the number that appears and tells a user how many more data points are "hidden" behind the cluster icon/circle and can be viewed if the map is zoomed in on. Don't forget to use runtime styling to adjust the text size, text color, and other text properties:

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