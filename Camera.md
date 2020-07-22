---
title: "Camera"
description: "Information about map camera behavior in the Mapbox Maps SDK for Android. Updating the camera position. Restricting the camera. It's all covered inside."
prependJs:
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---

The Maps SDK's camera is the user's viewpoint above the map. The camera can be tilted, rotated, zoomed, and/or moved by on-screen gestures such as pinching or with pre-written code. It's important to know that the camera object will not make any changes to markers, layers, sources, or other annotations that you've added without you directly influencing this behavior.

A few camera event listeners are provided in the Maps SDK to notify when, for example, the camera is adjusted. You can read more about these listeners inside the [events page](https://docs.mapbox.com/android/maps/overview/events/#camera-change-events).

## Camera position

The Maps SDK includes a `CameraPosition` class which is comprised of the camera's target, angle, zoom, tilt, and padding. These camera options shape the user's perspective of the map tile(s).

### Target

The target is a single latitude and longitude coordinate that the camera centers itself on. Changing the camera's target will move the camera to the inputted coordinates. The target is a `LatLng` object. The target coordinate is always _at the center of the viewport_.

Dragging the map with a finger will pan the map and thus, the camera target.

### Tilt

Tilt is the camera's angle from the nadir (directly facing the Earth) and uses unit degrees. The camera's minimum (default) tilt is 0 degrees, and the maximum tilt is 60. Tilt levels use six decimal point of precision, which enables you to restrict/set/lock a map's bearing with extreme precision.

The map camera tilt can also adjust by placing two fingertips on the map and moving both fingers up and down in parallel at the same time or

### Bearing

Bearing is the direction that the camera is pointing in and measured in degrees _clockwise from north_.

The camera's default bearing is 0 degrees (i.e. "true north") causing the map compass to hide until the camera bearing becomes a non-zero value. The `mapbox_uiCompass` boolean XML attribute allows adjustment of the compass' visibility. Bearing levels use six decimal point precision, which enables you to restrict/set/lock a map's bearing with extreme precision. Besides programmatically adjusting the camera bearing, the user can place two fingertips on the map and rotate their fingers.

### Zoom

Zoom controls the scale of the map and consumes any value between 0 and 22. At zoom level 0, the viewport shows continents and other world features. A middle value of 11 will show city level details, and at a higher zoom level, the map will begin to show buildings and points of interest. The camera can zoom in the following ways:

- Pinch motion two fingers _to zoom in and out_.
- Quickly tap twice on the map with a single finger _to zoom in_.
- Quickly tap twice on the map with a single finger and hold your finger down on the screen after the second tap. Then slide the finger up _to zoom out_ and down _to zoom out_.

Read [Designing maps for mobile devices](https://blog.mapbox.com/designing-maps-for-mobile-devices-32d2e49d2096), about the art/science of maps and visual information, to make sure your map style shows the right data at the correct camera positions.

## Get the current camera position

The `MapboxMap` class' `getCameraPosition()` method helps your code understand what is going on with your map's camera and what the user's viewing. Retreive the map's `CameraPosition` via the `MapboxMap` object once the map has been succesfully initialized. Various camera values are in that `CameraPosition` object.

{{
<AndroidActivityToggle
  id="current-camera-position"

java={`
CameraPosition currentCameraPosition = mapboxMap.getCameraPosition();
double currentZoom = currentCameraPosition.zoom;
double currentTilt = currentCameraPosition.tilt;
`}

kotlin={`
val currentCameraPosition = mapboxMap.cameraPosition
val currentZoom = currentCameraPosition.zoom
val currentTilt = currentCameraPosition.tilt
`}

/>
}}


## Update the camera position

The `MapboxMap` class in the Maps SDK has several methods to change the camera's position. Each camera movement API takes in a `CameraUpdate` object. You should use the `CameraUpdateFactory` class to provide the new camera position information. `CameraUpdateFactory` can build several different `CameraUpdate` objects including a `newLatLngZoom()`, `zoomBy()`, `newLatLngBounds()`, and several more. A straightforward method in `CameraUpdate` is `newCameraPosition()` which is how you'd pass in a built `CameraPosition`.

{{
  <RelatedPage
    url="/android/maps/examples/animate-the-map-camera/"
    title="Animate the map camera"
    contentType="example">
}}
Change the camera target, bearing, zoom, and tilt by animating the camera.
{{</RelatedPage>}}

A `CameraPosition` object can change a single property of the camera object such as the zoom or it can change multiple properties at the same time. For example, you could have the camera change its target, zoom out, **and** tilt _all at the same time_:

{{
<AndroidActivityToggle
  id="camera-position"

java={`
CameraPosition position = new CameraPosition.Builder()
	.target(new LatLng(51.50550, -0.07520))
	.zoom(10)
	.tilt(20)
	.build();
`}

kotlin={`
val position = CameraPosition.Builder()
	.target(LatLng(51.50550, -0.07520))
	.zoom(10.0)
	.tilt(20.0)
	.build()
`}

/>
}}

Read all about [`MapView` XML attributes](https://docs.mapbox.com/android/maps/overview/#mapview-xml-attributes) to learn about setting the camera's initial position with XML rather than programatically with Java or Kotlin. You should either set the initial camera position in XML or through `MapboxMapOptions` to prevent unnecessary downloading of map tiles using up your user's data.


{{
<AndroidActivityToggle
  id="animate-camera"

java={`
mapboxMap.animateCamera(CameraUpdateFactory.newCameraPosition(position), millisecondSpeed);
`}

kotlin={`
mapboxMap.animateCamera(CameraUpdateFactory.newCameraPosition(position), millisecondSpeed)
`}
/>
}}

Aside from consuming a `CameraUpdate` object, a cancelable callback can be added to know when the animation finishes or if the user cancels the camera move by performing a gesture on the map. The `easeCamera()` or `animateCamera()` methods have an optional duration parameter (in milliseconds), which lets you control how quickly the camera travels to the new map location.

| Method            | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| `moveCamera()`    | *Instantaneously* re-position the camera according to the instructions defined in `CameraUpdate` object parameter. `moveCamera()` quickly moves the camera, which can be visually jarring for a user. Strongly consider using either the `easeCamera()` or `animateCamera()` methods instead because they're less abrupt. |
| `easeCamera()`    | Gradually move the camera according to the instructions defined in `CameraUpdate` object parameter. The camera eases with a default duration, but a custom duration in milliseconds can be set as a second parameter. The camera's zoom level will not be affected unless specified in the `CameraUpdate` object. If you're currently using this method to have the map camera track the user as the device location changes, use [the `LocationComponent`'s `CameraMode.TRACKING` mode](/android/maps/overview/location-component/#cameramode/) instead of easing the camera yourself. Another one of `easeCamera()`'s optional parameters is a boolean to set whether the animation interpolator is used. Disabling the interpolator results in a linear animation to the new position. [This Java SDK Isochrone API example](/android/java/examples/isochrone-seekbar/) uses the animation interpolation. |
| `animateCamera()` | Animate the camera to a new location defined within a `CameraUpdate` object. The pre-defined camera animation evokes powered flight. [This example](https://docs.mapbox.com/android/maps/examples/animate-the-map-camera/) uses this method. |


## Center the camera within a map area

The camera can also centered within a map area like how the camera can be restricted to a region (see below). First you'll need a defined `LatLngBounds` object that includes at least two coordinates. You'll then be able to update the camera position using the available `newLatLngBounds()` method, which takes your bounding box and adjusts the viewport so that the specified region will be within view. Besides the bounding box being passed into the camera update factory, you will also need to provide an `int` value defining the padding between the edge of the screen and the actual bounded region. You also have the option to provide different padding values for each side of the box.

{{
  <RelatedPage
    url="/android/maps/examples/fit-camera-in-bounding-box/"
    title="Fit camera in bounding box"
    contentType="example">
}}
Adjust the camera position to fit a bounding box within the viewport.
{{</RelatedPage>}}

{{
<AndroidActivityToggle
  id="lat-lng-bounds"

java={`
LatLngBounds latLngBounds = new LatLngBounds.Builder()
                    .include(first marker position)
                    .include(second marker position)
                    .build();

mapboxMap.animateCamera(CameraUpdateFactory.newLatLngBounds(latLngBounds, 10));
`}

kotlin={`
val latLngBounds = LatLngBounds.Builder()
                .include(first marker position)
                .include(second marker position)
                .build()
mapboxMap.animateCamera(CameraUpdateFactory.newLatLngBounds(latLngBounds, 10));

`}

/>
}}

## Restricting the user's panning to a given area

The `setLatLngBoundsForCameraTarget` method in the `MapboxMap` class can limit the map camera to any area of the world that you'd like. If you feed the `LatLngBounds` object a minimum of two `LatLng` objects/coordinates, an _invisible_ rectangle will automatically be created to restrict the camera to the region.

{{
  <RelatedPage
    url="/android/maps/examples/restrict-map-panning/"
    title="Restrict map panning"
    contentType="example">
}}
Limit the map movement to within a region using a bounding box.
{{</RelatedPage>}}

## Camera and device location

Read about [the `LocationComponent`'s camera options](/android/maps/overview/location-component/) if you'd like to learn more about the relationship between controlling the camera and the device location UI experience. The Maps SDK provides convenient methods for making the camera follow the device location puck, adjusting based on the device rotation, and much more.