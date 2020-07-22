---
title: "Extrusions"
description: "Learn how to add and customize 3D shapes on your map using the Mapbox Maps SDK for Android."
prependJs:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import Note from '@mapbox/dr-ui/note';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
contentType: guide
language:
- Java
- Kotlin
---

The Mapbox Style Specification uses the term *extrusion* to refer to a 3D shape displayed on a map. Extrusions are often seen on the map in the form of buildings, but any type of `Polygon` shape can be extruded. The shape of a building extrusion follows the building footprint shape as if the 2D `Polygon` has been stretched vertically, a specified distance in meters, from the map's surface.

Like many other visual aspects of a Mapbox map, extrusions can be styled at run time and based on the values of data fields in the source data.

Adding and styling an extrusion is done via the Maps SDK for Android's `FillExtrusionLayer`. The `FillExtrusionLayer` is initialized with a unique ID and a unique or shared source ID, similar to other Maps SDK layers. Then define the `FillExtrusionLayer` layer's properties before adding the layer to the map's `Style` object.

## Styling

The Maps SDK for Android extrusion styling options follow [the official Mapbox Style Specification](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#fill-extrusion).

Please read the Style Specification properties to learn more about extrusion colors, opacity, pattern, coordinate offsetting, and more.

[The Mapbox Android demo app's extrusions-related examples](/android/maps/examples/#extrusions) show what's possible for styling extrusions.

{{
<AndroidActivityToggle
  id="basic-extrusion-setup"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		FillExtrusionLayer fillExtrusionLayer = new FillExtrusionLayer("extrusion-layer-id", "source-id");
	
		fillExtrusionLayer.setProperties(
		  fillExtrusionHeight(HEIGHT_NUMBER)
		);
	
		style.addLayer(fillExtrusionLayer);
	}
});
`}

kotlin={`
mapboxMap.getStyle {

	val fillExtrusionLayer = FillExtrusionLayer("extrusion-layer-id", "source-id")
	
	fillExtrusionLayer.setProperties(
		fillExtrusionHeight(HEIGHT_NUMBER)
	)
	
	it.addLayer(fillExtrusionLayer)
}
`}
/>
}}

## Light

Part of the Mapbox Style Specification is the concept of _light_. You can adjust the color, intensity, and angle at which light hits the map, as if you were adjusting the location of the sun.

A style's light property provides a _global_ light source for that entire style. Because these properties are applied across all layers, `light` is a [root property](https://docs.mapbox.com/mapbox-gl-js/style-spec/root/#light) in the Style Specification rather than a paint or layout property to be defined in a single [fill-extrusion layer](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#fill-extrusion).


{{<Note>}}
Consider using Mapbox Studio to iterate on a map style's light options. Working in Mapbox Studio allows you to more quickly explore the visual impact of changes to your style's light properties compared to iterating in your Android project. Once you find the values in Mapbox Studio that create the extrusion styling that you want, then use the same final values in your Android project's code.

[Read more about the spherical coordinate system](https://en.wikipedia.org/wiki/Spherical_coordinate_system) to understand light's radial, azimuthal, and polar positioning.
{{</Note>}}

{{
  <RelatedPage
    url="/android/maps/examples/adjust-light-location-and-color/"
    title="Light adjustment"
    contentType="example">
}}
Adjust the light's location and color to see how it affects the extrusion.
{{</RelatedPage>}}

## Tricks

Extrusion styling is enhanced by the Maps SDK's [expressions](/android/maps/overview/expressions/) and [data-driven styling](/android/maps/overview/data-driven-styling/). Their combination can lead to fun effects and achieving the specific data visualization to match your use case.

### Relative heights

If you're using extrusions as a data visualization strategy for data that's not related to the physical height of objects in space (for example, visualizing the population in census block groups), the value of the data field might not be appropriate as an extrusion height (in meters). To make the extrusions visible at various zoom levels you can use Maps SDK expressions to make all the extrusions appear much taller on the map **and** keep their relative height differences the same. This way, the extrusions can be visible at a much lower zoom level when the map camera is farther away from the map surface.

`fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))));` is the key to this effect.

{{
<AndroidActivityToggle
  id="extrusion-height"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		FillExtrusionLayer fillExtrusionLayer = new FillExtrusionLayer("extrusion-layer-id", "source-id");
	
		fillExtrusionLayer.setProperties(
		  fillExtrusionColor(COLOR.RED),
		  fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
		);
	
		style.addLayer(fillExtrusionLayer);
	}
});
`}

kotlin={`
mapboxMap.getStyle {
	val fillExtrusionLayer = FillExtrusionLayer("extrusion-layer-id", "source-id")

	fillExtrusionLayer.setProperties(
	        fillExtrusionColor(Color.RED),
	        fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
	)
	
	it.addLayer(fillExtrusionLayer)
}
`}

/>
}}

{{
  <RelatedPage
    url="/android/maps/examples/display-3d-building-height-based-on-vector-data/"
    title="Vector data height"
    contentType="example">
}}
Set extrusion height based on a dataset.
{{</RelatedPage>}}

### Base vs. height

Adjusting the difference between `fillExtrusionBase` and `fillExtrusionHeight` leads to interesting visual effects as well. If the difference is a small amount such as 3 meters, the visual effect will be a small sliver of an extrusion that appears to be floating in the air. This can be a nice yet rudimentary way to visualize flight data or other data that is not connected to the flat surface of the map.

{{
<AndroidActivityToggle
  id="base-vs-height"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		FillExtrusionLayer fillExtrusionLayer = new FillExtrusionLayer("extrusion-layer-id", "source-id");
	
		fillExtrusionLayer.setProperties(
			fillExtrusionColor(Color.RED),
			fillExtrusionBase(get("HEIGHT_FEATURE_PROPERTY_KEY")),
	      fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
		);
	
		style.addLayer(fillExtrusionLayer);
	}
});

`}

kotlin={`
mapboxMap.getStyle {

	val fillExtrusionLayer = FillExtrusionLayer("extrusion-layer-id", "source-id")
	
	fillExtrusionLayer.setProperties(
	    fillExtrusionColor(Color.RED),
	    fillExtrusionBase(get("HEIGHT_FEATURE_PROPERTY_KEY")),
	    fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
	)
	
	it.addLayer(fillExtrusionLayer)
}
`}

/>
}}