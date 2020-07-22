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

## How querying works

You can query the map for features that exist at a `Point` or within a `BoundingBox`. Aside from layers, it is also possible to query the source for specific information matching your query regardless if the items are being displayed on the map.

Because features come from vector tile (or GeoJSON data that is converted to tiles internally), the query may split feature geometries or duplicate them across tile boundaries. As a result, features may appear multiple times in query results.

For example, when querying by bounding box in an area that includes a highway spanning multiple tiles, the query will return a separate feature for every tile the highway spans. The geometry of each feature will be limited to the part of the highway that exists on that tile. Similarly, a point feature near a tile boundary may appear in multiple tiles due to tile buffering.


## Query rendered features

Use `queryRenderedFeatures` to return all map features currently rendered on the device. Features must be visible in the device's viewport **and** fully rendered before you can access them.

### Query at a point

`queryRenderedFeatures` only accepts a screen pixel value instead of `LatLng`, so in many cases you'll need convert screen position to geographic position. In the example below, when the map is clicked it provides a `LatLng` that is used to get the features at that point on the map.

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

### Query inside a bounding box

To query the map for `Feature`s in an area, pass in a bounding box using a `RectF` object. This can either come from a Android `View` displayed to the user on top of the map or four coordinates that are shown within the viewport.

The example below shows how to use four coordinates to create a `RectF`. The `RectF` object is passed into `queryRenderedFeatures()`.

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

## Query source features

`querySourceFeatures()` returns all features that match the query parameters regardless of whether or not the feature is currently rendered on the map. The domain of the query includes all currently-loaded vector tiles and GeoJSON source tiles. This function does not check tiles outside of the visible viewport.

To query a source, you must pass in the query parameters as a set of `Filters` and only the features that satisfy the statement will be added to the returning list of features. In the example below, the map style contains a GeoJSON source called `population-source`, which contains a `population` property for each feature. The query below limits the features returned to those that have a population greater than 100,000.

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