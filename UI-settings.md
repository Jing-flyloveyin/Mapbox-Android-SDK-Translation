---
title: "UI settings"
description: "Learn how to use the Mapbox Maps SDK for Android to adjust on-screen UI such as the compass, Mapbox logo, and attribution."
prependJs:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
contentType: guide
language:
- Java
- Kotlin
---


The Mapbox Maps SDK for Android gives developers the ability to adjust certain elements of the map's user interface (UI). Once a `MapboxMap` has been correctly set up, obtain its `UiSettings` object.

{{
<AndroidActivityToggle
  id="get-ui-settings"

java={`
mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		UiSettings uiSettings = mapboxMap.getUiSettings();


	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.MAPBOX_STREETS) {

	val uiSettings = mapboxMap.uiSettings

}
`}

/>
}}

Changes to gesture recognition, the compass, the Mapbox logo, and attribution can be made once you have the `UiSettings` object. Changes made to `UiSettings` are immediately reflected on the map.

You can also configure the UI settings with [the Maps SDK's XML attributes](/android/maps/overview/#mapview-xml-attributes) instead of the `UiSettings` class' methods.

All the UI settings also have "get" methods that allow you to see the current settings (for example, if the compass is enabled, what the logo's margins are, or what the custom compass image is).

{{
<AndroidActivityToggle
  id="get-method"

java={`
mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		UiSettings uiSettings = mapboxMap.getUiSettings();
	
		uiSettings.areAllGesturesEnabled();
	
	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.MAPBOX_STREETS) {

	val uiSettings = mapboxMap.uiSettings
	
	uiSettings.areAllGesturesEnabled()

}
`}

/>
}}

## Gestures

The Mapbox Maps SDK for Android uses [the Mapbox Gestures for Android library](/android/maps/overview/gestures/) to detect gestures.

Adjust or completely disable zoom, tilt, scroll, rotate, quick zoom, and double tap gesture recognition. Disabling map gestures doesn't affect whether you can change the camera position programmatically.

For example, here's how you would disable the ability to increase the zoom level by double tapping on the map:

{{
<AndroidActivityToggle
  id="gesture-adjustment"

java={`
mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		UiSettings uiSettings = mapboxMap.getUiSettings();
	
		uiSettings.setDoubleTapGesturesEnabled(false);
	
	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.MAPBOX_STREETS) {

	val uiSettings = mapboxMap.uiSettings
	
	uiSettings.isDoubleTapGesturesEnabled = false

}
`}

/>
}}

See the [map camera position documentation](/android/maps/overview/camera/#camera-position) for more information about how gestures can affect camera position.

## Compass

The Maps SDK contains a compass image, which appears on the map when the map is rotated in any direction. The compass _doesn't_ represent the physical device's relation to the magnetic north pole. The compass image rotates based on the on-screen map rotation.

When the user clicks on the compass image, the camera animates back to the default north orientation (bearing of 0) and the compass image fades away shortly afterwards.

By default, the compass appears in the upper right hand corner of the `MapView`, but its margins can be adjusted. A custom drawable image can be passed to the Maps SDK to replace the default image.

Another option is to completely disable the compass:

{{
<AndroidActivityToggle
  id="disable-compass"

java={`
mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		UiSettings uiSettings = mapboxMap.getUiSettings();
	
		uiSettings.setCompassEnabled(false);
	
	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.MAPBOX_STREETS) {

	val uiSettings = mapboxMap.uiSettings
	
	uiSettings.isCompassEnabled = false

}
`}

/>
}}


## Logo and attribution

Please read [Mapbox's attribution policy](/android/maps/overview/#attribution) for information on displaying the Mapbox wordmark logo and attribution.

The logo's properties such as margins and gravity can be adjusted with XML or the `UiSettings` object. The attribution `i` icon's properties such as tint, margins, and gravity can also be adjusted with XML or the `UiSettings` object.

{{
  <RelatedPage
    url="/android/maps/examples/change-attribution-color/"
    title="Attribution icon color"
    contentType="example">
}}
See how to customize the attribution `i` icon's color to match a map style or stand out from the style color.
{{</RelatedPage>}}