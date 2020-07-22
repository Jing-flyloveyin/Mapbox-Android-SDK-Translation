---
title: "Styling the map"
description: "Learn how to use the Mapbox Maps SDK for Android to change your map style to a custom style or a pre-made style made by the talented Mapbox cartography team."
prependJs:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---

The Maps SDK for Android allows full customization of the look of the map. You can choose one of Mapbox's core styles designed by our cartographers or create a custom map style by adjusting the map's colors, icons, and fonts to match your application's UI or company's brand.

There are two approaches to customizing the look of the map: creating a custom map style with Mapbox Studio and loading it into your application or updating map features at runtime using the Maps SDK for Android.

Using runtime styling, you're able to dynamically change the look and feel of your map in real time. Lighten or darken the map based on the time of day, personalize icons and map colors based on your users’ activity, switch languages dynamically, or increase the size of labels based on user preferences to improve legibility.

## Style object

A `Style` object refers to [the Mapbox map style](https://www.mapbox.com/help/define-style/) being used in your application. Sources, layers, and images appear on the map via a map style, so they are added and retrieved via the `Style` object rather than adding them to the actual `MapboxMap` object.

A `Style` object must be created and given to the map for the map to appear properly. Create a `Style` by using a:

- default Mapbox style found in the `Style` class constants
- custom map style URL from a Mapbox account
- raw style JSON string or referenced style JSON via `asset://` or `path://`

If the style fails to load or you set an invalid style URL, the map view will become blank. An error message will be logged in the Android logcat and the `MapView.OnDidFailLoadingMapListener` callback will be triggered.

## Changing the map style

You'll have to change the map style URL programmatically if you'd like to load a completely new map style. Call `mapboxMap.setStyle()` with the style URL as the parameter.

{{
<AndroidActivityToggle
  id="setting-custom-style-url-java"

java={`
mapboxMap.setStyle(new Style.Builder().fromUri(uniqueStyleUrl), new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

	// Custom map style has been loaded and map is now ready


	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.Builder().fromUri(uniqueStyleUrl)) {

	// Custom map style has been loaded and map is now ready

}
`}

/>
}}

## Default Mapbox styles

As powerful as styling the map can be, Mapbox offers six professionally designed map styles:

1. **Mapbox Streets:** Mapbox Streets is a comprehensive, general-purpose map that emphasizes legible styling of road and transit networks.
2. **Outdoor:** Mapbox Outdoors is a general-purpose map with curated datasets and specialized styling tailored to hiking, biking, and the most adventurous use cases.
3. **Light and Dark:** Mapbox Light and Mapbox Dark are subtle, full-featured maps designed to provide geographic context while highlighting the data on your analytics dashboard, data visualization, or data overlay.
4. **Satellite:** is our full global basemap that works as a blank canvas or an overlay for your own data.
5. **Satellite Streets:** combines our Mapbox Satellite with vector data from Mapbox Streets. The comprehensive set of road, label, and POI information brings clarity and context to the crisp detail in our high-resolution satellite imagery.
6. **Traffic:** Visually show realtime traffic using either the provided day or night traffic styles.

The Maps SDK's `Style` class has `private static final String`s of the default Mapbox styles' URLs, so that you can conveniently reference the styles when you have to pass the style URL through the `setStyle()` method or other methods in your project. For example:

{{
<AndroidActivityToggle
  id="setting-default-mapbox-style-url-java"

java={`
mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

	// Custom map style has been loaded and map is now ready


	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.MAPBOX_STREETS) {

	// Custom map style has been loaded and map is now ready

}
`}

/>
}}


## Retrieving a map layer

Retrieving a map layer is the first step to adjusting the visual look of the map in real time. Every map style has a stack of map layers and each layer has an id. In the professionally-designed `Mapbox Streets` style, layers in that style have ids such as `landuse`, `settlement-label`, `natural-point-label`, and `building`.

{{
<AndroidActivityToggle
  id="getting-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {
    
    Layer singleLayer = style.getLayer(UNIQUE_LAYER_ID);

  }
});
`}

kotlin={`
mapboxMap.getStyle {
	val singleLayer = it.getLayer(UNIQUE_LAYER_ID) 
}
`}

/>
}}

You can view the map style's layer Z-index order and layer ids in Mapbox Studio. Another way to view this information is by printing out each layer ID to your Android logcat once the map has been loaded on the device.

{{
<AndroidActivityToggle
  id="printing-layer-ids"

java={`
mapView.getMapAsync(new OnMapReadyCallback() {
@Override
	public void onMapReady(@NonNull final MapboxMap mapboxMap) {

		mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
			@Override
			public void onStyleLoaded(@NonNull Style style) {
	
				for (Layer singleLayer : style.getLayers()) {
					Log.d(TAG, "onMapReady: layer id = " + singleLayer.getId());
				}
	
			}
		});
	}
});
`}

kotlin={`
mapView?.getMapAsync {
	mapView?.getMapAsync { mapboxMap -> mapboxMap.setStyle(Style.MAPBOX_STREETS) {

			for (singleLayer in it.layers) {
				Log.d(TAG, "onMapReady: layer id = " + singleLayer.id)
			}
	
		}
	}
}
`}

/>
}}

Once you have an individual map layer, you can then use [data-driven styling](/android/maps/overview/data-driven-styling/) and [expressions](/android/maps/overview/expressions/) to adjust the layer's properties. For example, here's how you'd use runtime styling to change the `water` layer's blue color to a deeper blue color:

{{
<AndroidActivityToggle
  id="changing-water-layer"

java={`
button.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View view) {
    mapboxMap.getStyle(new Style.OnStyleLoaded() {
      @Override
      public void onStyleLoaded(@NonNull Style style) {

        Layer waterLayer = style.getLayer("water");
        
        if (waterLayer != null) {
          waterLayer.setProperties(PropertyFactory.fillColor(Color.parseColor("#004f6b")));
        }
    
      }
    });
  }
});
`}

kotlin={`
button.setOnClickListener {
	mapboxMap.getStyle {
	    
	    val waterLayer = it.getLayer("water")
	
	    waterLayer?.setProperties(PropertyFactory.fillColor(Color.parseColor("#004f6b")))
	    
	}
}
`}
/>
}}

## Style-related events

Changing the map's style triggers a "map event". Read the [map change event documentation](https://docs.mapbox.com/android/maps/overview/events/#map-change-events) for more information about the events and how to display or re-display data on your map _after_ a map style has finished loading.