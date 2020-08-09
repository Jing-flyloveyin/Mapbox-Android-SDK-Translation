---
title: "Showing device location"
description: "Showing device location with the Mapbox Maps SDK for Android."
prependJs:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import AppropriateImage from '../../../components/appropriate-image';"
  - "import Note from '@mapbox/dr-ui/note';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidDeviceFrame from '../../../components/android-device-frame';"
  - "import pulsingLocationComponent from '../../../video/maps-overview-location-component-pulsing.mp4'"
contentType: guide
language:
- Java
- Kotlin
---

Showing the user's current location as a map annotation is a popular and often critical feature of location-based apps. The Maps SDK's `LocationComponent` makes use of the Maps SDK's [runtime styling capabilities](/android/maps/overview/data-driven-styling/) to display the device location icon within the map itself rather than on top as an Android view. Mapbox map layers and layer styling give you precise control over how you show a device's location on the map.

将用户的当前位置显示为地图标注是基于位置的应用程序的一个关键且高频使用的功能。Maps SDK的`LocationComponent`利用Maps SDK的[运行时样式功能](/android/maps/overview/data-driven-styling/)在地图本身中显示设备位置图标，而不是作为Android视图显示在顶部。Mapbox地图图层和图层样式让您可以对在地图上显示设备的位置进行进行精确控制。


{{<Note 小贴士>}}
This `LocationComponent` has replaced the now-deprecated Location Layer Plugin. The `LocationComponent` is integrated into the Maps SDK for Android so that you don't need to add any additional dependencies. It brings the same set of functionality that you were used to with the plugin.

`LocationComponent`目前已经取代了已废弃的Location Layer Plugin。`LocationComponent`已被集成到Android地图SDK中，因此您无需添加任何额外的依赖关系。它带来了与插件相同的功能集。
{{</Note 小贴士>}}

## Requesting location permissions 请求位置权限

You'll need to request the Android-system location permission before using the `LocationComponent`. If you build your Android project targeting API level 23 or higher your application will need to request permissions during runtime. Handling this directly in your activity produces boilerplate code and can often be hard to manage. [Read more about using the Mapbox Core Library for Android's `PermissionsManager`](/android/core/overview/#permissionsmanager) class.

在使用`LocationComponent`之前，您需要请求Android系统的位置权限。如果您构建的Android项目的目标是API级别23或更高，您的应用程序将需要在运行时请求权限。若您在活动中直接处理这个问题会产生模板代码，并且通常很难管理。[阅读更多关于使用Mapbox Core Library for Android的`PermissionsManager`](/android/core/overview/#permissionsmanager)类。

## Customization 定制化

The `LocationComponent` can be customized in many different ways. You can set the image drawables, opacities, colors, and more. [See a full list of XML attributes for styling the `LocationComponent` via XML](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/res-public/values/public.xml#L109-L152). [The `LocationComponentOptions` class](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/java/com/mapbox/mapboxsdk/location/LocationComponentOptions.java) can be used if you prefer to customize the `LocationComponent` programatically. Create a [LocationComponentOptions](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/java/com/mapbox/mapboxsdk/location/LocationComponentOptions.java) object and then use whichever `LocationComponentOptions.builder()`'s various methods you'd like. Then use the built `LocationComponentOptions` object as parameter in `LocationComponentActivationOptions#locationComponentOptions()` or by passing it through as a parameter of the `LocationComponent#applyStyle()` method at a later time.

`LocationComponent`可以通过多种方式进行定制。您可以设置图像的可绘制性、不透明度、颜色等。[查看XML属性的完整列表，从而通过XML对`LocationComponent`进行样式设置](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/res-public/values/public.xml#L109-L152)。如果您喜欢以程序方式定制`LocationComponent`，可以使用[`LocationComponentOptions`类](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/java/com/mapbox/mapboxsdk/location/LocationComponentOptions.java)。创建一个[LocationComponentOptions](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/java/com/mapbox/mapboxsdk/location/LocationComponentOptions.java)对象，然后使用`LocationComponentOptions.builder()`的各种方法。然后在`LocationComponentActivationOptions#locationComponentOptions()`中使用建立的`LocationComponentOptions`对象作为参数，或者在以后在使用`LocationComponent#applyStyle()`方法时将其作为参数。

{{
<AndroidActivityToggle
  id="location-component-customization"
 java={`
LocationComponentOptions locationComponentOptions = LocationComponentOptions.builder(this)
	.layerBelow(layerId)
	.foregroundDrawable(R.drawable.drawable_name)
	.bearingTintColor(int color)
	.accuracyAlpha(float)
	.build();

LocationComponentActivationOptions locationComponentActivationOptions = LocationComponentActivationOptions
	.builder(this, style)
	.locationComponentOptions(locationComponentOptions)
	.build();

locationComponent = mapboxMap.getLocationComponent();
locationComponent.activateLocationComponent(locationComponentActivationOptions);
`}
 kotlin={`
val locationComponentOptions = LocationComponentOptions.builder(this)
	.layerBelow(layerId)
	.foregroundDrawable(R.drawable.drawable_name)
	.bearingTintColor(int color)
	.accuracyAlpha(float)
	.build()

val locationComponentActivationOptions = LocationComponentActivationOptions
	.builder(this, style)
	.locationComponentOptions(locationComponentOptions)
	.build()

locationComponent = mapboxMap.locationComponent
locationComponent.activateLocationComponent(locationComponentActivationOptions)
`}
 />
}}

{{
  <RelatedPage
    url="/android/maps/examples/location-component-options/"
    title="Customizing device location"
    contentType="example">
}}
Use `LocationComponent` options to style the device location icon.
使用 `LocationComponent`选项来设置设备位置图标的样式。
{{</RelatedPage>}}

### Active styling options 激活样式选项

{{
<AppropriateImage imageId="maps-overview-location-component-active" className="block mx-auto pt18 wmax300" />
}}

| XML Option                      | Explanation                                                  |
| ------------------------------- | ------------------------------------------------------------ |
| mapbox_foregroundDrawable       | 运用可绘制的图像，取代蓝色圆圈。                                   |
| mapbox_foregroundTintColor      | 蓝色圆圈的颜色。                                    |
| mapbox_backgroundDrawable       | 运用可绘制的图像，取代白色圆环。          |
| mapbox_backgroundTintColor      | 白色圆环的颜色。                                            |
| mapbox_bearingDrawable          | 运用可绘制的图像，取代蓝色三角。     |
| mapbox_bearingTintColor         | 蓝色三角的颜色。                                          |
| mapbox_navigationDrawable       | 运用可绘制的图像，代表导航状态的图标。          |
| mapbox_accuracyAlpha            | 设备位置图标周围较大的浅蓝色圆圈。 |
| mapbox_accuracyColor            | 设备位置图标周围较大的淡蓝色圆圈的颜色。 |
| mapbox_elevation                | 地图和设备位置图标之间的空间大小。仰角将调整白色圆圈下阴影的大小。传递`0f`作为方法参数将移除阴影效果。|
| mapbox_compassAnimationEnabled  | Enable or disable smooth animation of compass values for the blue triangle.启用或禁用蓝色三角形罗盘的平滑动画。 |
| mapbox_accuracyAnimationEnabled | 启用或禁用设备位置图标周围较大的淡蓝色精度圆圈的平滑动画。 |
| mapbox_pulsingLocationCircleEnabled | 启用或禁用脉动圈。 |
| mapbox_pulsingLocationCircleFadeEnabled | 启用或禁用脉冲圆的淡化。如果淡化，圆的不透明度会随着半径的增大而降低。
| mapbox_pulsingLocationCircleColor | Sets the color of the pulsing circle.
| mapbox_pulsingLocationCircleDuration | Sets the number of milliseconds it takes for a single pulse of the pulsing circle.
| mapbox_pulsingLocationCircleRadius | The maximum radius that a single pulse should expand the pulsing circle to.
| mapbox_pulsingLocationCircleAlpha | Sets the opacity of the pulsing circle. The expected range is 0 to 1. An opacity of 1 makes the layer fully opaque.
| mapbox_pulsingLocationCircleInterpolator | Sets the pulsing circle's interpolator animation.

{{<Note>}}

`mapbox_compassAnimationEnabled` and `mapbox_accuracyAnimationEnabled` options are available because smooth animation of the `LocationComponent` can have a significant impact on a device's battery level. Disabling smooth compass and accuracy animations during scenarios where it'd be the only animation running on the device can decrease CPU usage by a significant amount.

{{</Note>}}

Adding a pulsing circle to the `LocationComponent` is another styling option. The pulsing circle expands its radius over a certain amount of time to make the `LocationComponent` look as if it's a beacon that is emitting energy. This UI can give the `LocationComponent` and your map an even more engaging and energetic feel as the device stays still or moves around the map.

{{
  <AndroidDeviceFrame
    src={pulsingLocationComponent}
    title="Adding a pulsing circle to LocationComponent"
    rotation="portrait"
  />
}}

The pulsing circle is disabled by default. To show the default pulsing UI, pass `true` through the `LocationComponentOptions.builder()`'s `pulseEnabled()` method to enable the pulsing. The `LocationComponentOptions.builder()` has several methods (seen listed above) to customize the pulsing UI.

{{
<AndroidActivityToggle
  id="pulsing-location-component"
 java={`
LocationComponentOptions locationComponentOptions =
	LocationComponentOptions.builder(this)
	.pulseEnabled(true)
	.pulseColor(Color.GREEN)
	.pulseAlpha(.4f)
	.pulseInterpolator(new BounceInterpolator())
	.build();

LocationComponentActivationOptions locationComponentActivationOptions = LocationComponentActivationOptions
	.builder(this, style)
	.locationComponentOptions(locationComponentOptions)
	.build();

locationComponent = mapboxMap.getLocationComponent();
locationComponent.activateLocationComponent(locationComponentActivationOptions);
`}
 kotlin={`
val locationComponentOptions = LocationComponentOptions.builder(this)
	.pulseEnabled(true)
	.pulseColor(Color.GREEN)
	.pulseAlpha(.4f)
	.pulseInterpolator(BounceInterpolator())
	.build()

val locationComponentActivationOptions = LocationComponentActivationOptions
	.builder(this, style)
	.locationComponentOptions(locationComponentOptions)
	.build()

locationComponent = mapboxMap.locationComponent
locationComponent.activateLocationComponent(locationComponentActivationOptions)
`}
 />
}}

### Stale styling options

{{
<AppropriateImage imageId="maps-overview-location-component-stale" className="block mx-auto pt18 wmax300" />
}}

| XML Option                      | Explanation                                                  |
| ------------------------------- | ------------------------------------------------------------ |
| mapbox_foregroundDrawableStale  | Drawable image that would replace the grey circle's location. |
| mapbox_foregroundStaleTintColor | The grey circle's color.                                     |
| mapbox_backgroundDrawableStale  | Drawable image that would replace the white circle's location. |
| mapbox_backgroundStaleTintColor | The white circle's color.                                    |

## Activating

`MapboxMap#getLocationComponent()` fetches the component and `LocationComponent#activateLocationComponent()` activates it.

The `activateLocationComponent()` method requires a built `LocationComponentActivationOptions` class. The `LocationComponentActivationOptions` class offers a convenient way to set activation options such as whether to use the default `LocationEngine` or [a built `LocationComponentOptions` object](/android/maps/overview/location-component/#customization).


Create a `LocationComponentActivationOptions` class with the builder pattern that is provided to you via the Maps SDK.

{{
<AndroidActivityToggle
  id="location-component-activation-option-building"
 java={`
LocationComponentActivationOptions locationComponentActivationOptions = LocationComponentActivationOptions
  .builder(this, style)
  .locationComponentOptions(locationComponentOptions)
  .useDefaultLocationEngine(true)
  .build();
`}
 kotlin={`
 val locationComponentActivationOptions = LocationComponentActivationOptions
  .builder(this, style)
  .locationComponentOptions(locationComponentOptions)
  .useDefaultLocationEngine(true)
  .build()
`}
 />
}}

Retrieve and activate the `LocationComponent` once the user has granted location permission **and** the map has fully loaded.

{{
<AndroidActivityToggle
  id="location-component-activation"
 java={`
@Override
public void onMapReady(@NonNull MapboxMap mapboxMap) {

	mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
	  @Override
	  public void onStyleLoaded(@NonNull Style style) {
	
	    enableLocationComponent();
	
	  }
	});
}

@SuppressWarnings( {"MissingPermission"})
private void enableLocationComponent() {

	// Check if permissions are enabled and if not request
	if (PermissionsManager.areLocationPermissionsGranted(this)) {
	
		// Get an instance of the component
		LocationComponent locationComponent = mapboxMap.getLocationComponent();
	
		// Activate with a built LocationComponentActivationOptions object
		locationComponent.activateLocationComponent(LocationComponentActivationOptions.builder(this, style).build());
	
		// Enable to make component visible
		locationComponent.setLocationComponentEnabled(true);
	
		// Set the component's camera mode
		locationComponent.setCameraMode(CameraMode.TRACKING);
	
		// Set the component's render mode
		locationComponent.setRenderMode(RenderMode.COMPASS);
	
	} else {
	
		permissionsManager = new PermissionsManager(this);
	
		permissionsManager.requestLocationPermissions(this);
	
	}
}

`}
 kotlin={`

override fun onMapReady(mapboxMap: MapboxMap) {

	mapboxMap.setStyle(Style.MAPBOX_STREETS) {
	
		style -> enableLocationComponent()
	
	}
}

@SuppressWarnings("MissingPermission")
private fun enableLocationComponent() {

	// Check if permissions are enabled and if not request
	if (PermissionsManager.areLocationPermissionsGranted(this)) {
	
		// Get an instance of the component
		val locationComponent = mapboxMap?.locationComponent
	
		// Activate with a built LocationComponentActivationOptions object
		locationComponent?.activateLocationComponent(LocationComponentActivationOptions.builder(this, style).build())
	
		// Enable to make component visible
		locationComponent?.isLocationComponentEnabled = true
	
		// Set the component's camera mode
		locationComponent?.cameraMode = CameraMode.TRACKING
	
		// Set the component's render mode
		locationComponent?.renderMode = RenderMode.COMPASS
	
	} else {
	
		permissionsManager = PermissionsManager(this)
	
		permissionsManager?.requestLocationPermissions(this)
	
	}
}
`}
 />
}}

{{
  <RelatedPage
    url="/android/maps/examples/show-a-users-location/"
    title="Showing device location"
    contentType="example">
}}
Add the device's location to the map.
{{</RelatedPage>}}

## Visibility

There is a single method to either enable or disable the `LocationComponent`'s visibility after activation. The `setLocationComponentEnabled()` method requires a `true`/`false` boolean parameter. When set to `false`, this method will hide the device location icon and stop map camera animations from occurring.

{{
<AndroidActivityToggle
  id="location-component-visibility"
 java={`
locationComponent.setLocationComponentEnabled(true);
`}
 kotlin={`
locationComponent.isLocationComponentEnabled = true
`}
 />
}}

## `RenderMode`

The `RenderMode` class contains preset options for the device location image.

{{
<AndroidActivityToggle
  id="location-component-render-mode"
 java={`
locationComponent.setRenderMode(RenderMode.NORMAL);
`}
 kotlin={`
locationComponent?.renderMode = RenderMode.NORMAL
`}
 />
}}

There are three types of `RenderMode` options:

| `RenderMode` | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| `NORMAL`     | This mode shows the device location, ignoring both compass and GPS bearing (no arrow rendered). {{<AppropriateImage imageId="maps-overview-location-component-normal-render" className="block mx-auto pt18" />}} |
| `COMPASS`    | This mode shows the device location, as well as an arrow that is considering the compass of the device. {{<AppropriateImage imageId="maps-overview-location-component-compass-render" className="block mx-auto pt18" />}} |
| `GPS`        | This mode shows the device location with the icon bearing updated from the `Location` updates being provided to the `LocationComponent`. {{<AppropriateImage imageId="maps-overview-location-component-gps-render" className="block mx-auto pt18" />}} |

{{<Note>}}
The actual device location icon is highly customizable with methods such as `LocationComponentOptions#foregroundDrawable()` and `LocationComponentOptions#backgroundDrawable()`.
{{</Note>}}


## `CameraMode`

The method `LocationComponent#setCameraMode(@CameraMode.Mode int cameraMode)` allows developers to set specific camera tracking instructions as the device location changes.

{{
<AndroidActivityToggle
  id="location-component-camera-mode"
 java={`
locationComponent.setCameraMode(CameraMode.TRACKING);
`}
 kotlin={`
locationComponent?.cameraMode = CameraMode.TRACKING
`}
 />
}}

There are 7 `CameraMode` options available:

| `CameraMode`         | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| `NONE`               | No camera tracking.                                          |
| `NONE_COMPASS`       | Camera does not track location, but does track compass bearing. |
| `NONE_GPS`           | Camera does not track location, but does track GPS `Location` bearing. |
| `TRACKING`           | Camera tracks the device location, no bearing is considered. |
| `TRACKING_COMPASS`   | Camera tracks the device location, tracking bearing provided by the device compass. |
| `TRACKING_GPS`       | Camera tracks the device location, with bearing provided by a normalized `Location#getBearing()`. |
| `TRACKING_GPS_NORTH` | Camera tracks the device location, with bearing always set to north (0). |


Here are a few examples from [the `LocationModesActivity` in the Maps SDK's test application](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDKTestApp/src/main/java/com/mapbox/mapboxsdk/testapp/activity/location/LocationModesActivity.java):

**CameraMode.NORMAL**

{{
<AppropriateImage
    imageId="maps-overview-location-component-normal-camera"
    alt="screenshot of a the normal location layer mode on an Android device"
    className="wmax300 mb24"
/>
}}

**CameraMode.COMPASS**

{{
<AppropriateImage
    imageId="maps-overview-location-component-compass-camera"
    alt="screenshot of a the compass location layer mode on an Android device"
    className="wmax300 mb24"
/>
}}

**CameraMode.GPS**

{{
<AppropriateImage
    imageId="maps-overview-location-component-gps-camera"
    alt="screenshot of a the GPS location layer mode on an Android device"
    className="wmax300 mb24"
/>
}}


Traditional camera transitions will be canceled when any of the camera modes, besides `CameraMode#NONE`, are engaged. Use `LocationComponent#zoomWhileTracking` and `LocationComponent#tiltWhileTracking` to manipulate the camera in a tracking state. Use these two in combination with traditional camera transitions and `MapboxMap#CancelableCallback` to schedule fluid transitions.

When instantiating the `LocationComponent` for the first time, the map's max/min zoom levels will be set to`LocationComponentOptions#MAX_ZOOM_DEFAULT` and `LocationComponentOptions#MIN_ZOOM_DEFAULT` respectively. Adjust the zoom range with the `LocationComponentOptions#maxZoom()` and `LocationComponentOptions#minZoom()` methods in the `LocationComponentOptions` class.

## Gesture thresholds while camera tracking

The `LocationComponent` is integrated with [the Mapbox Gestures for Android library](https://docs.mapbox.com/android/maps/overview/gestures/). The component will adjust the camera's focal point and increase thresholds to enable camera manipulation, like zooming in and out, without breaking tracking. Enabling this feature is explicitly opt-in because it overwrites custom gestures detection implementation set with `MapboxMap#setGesturesManager(AndroidGesturesManager, boolean, boolean)`.

To enable the feature use `LocationComponentOptions#trackingGesturesManagement(boolean)`.

You can adjust thresholds that need to be exceeded to break the tracking for one pointer gestures (like panning the map, double-tap to zoom in) and multi-pointer gestures (like scale gesture, two-tap to zoom out):
- `LocationComponentOptions#trackingInitialMoveThreshold(float)` adjusts the minimum single pointer movement in pixels required to break camera tracking.
- `LocationComponentOptions#trackingMultiFingerMoveThreshold(float)` adjusts minimum multi pointer movement in pixels required to break camera tracking (for example during scale gesture).
- If either of these thresholds are exceeded and tracking is dismissed, developers can listen to this with a `OnCameraTrackingChangedListener`:

{{
<AndroidActivityToggle
  id="location-component-gestures"
 java={`
LocationComponent locationComponent = mapboxMap.getLocationComponent();

locationComponent.addOnCameraTrackingChangedListener(new OnCameraTrackingChangedListener() {
    @Override
    public void onCameraTrackingDismissed() {
      // Tracking has been dismissed


    }
    
    @Override
    public void onCameraTrackingChanged(int currentMode) {
      // CameraMode has been updated


    }
});
`}
 kotlin={`
val locationComponent = mapboxMap?.locationComponent

locationComponent.addOnCameraTrackingChangedListener(object : OnCameraTrackingChangedListener {
	override fun onCameraTrackingDismissed() {
	    // Tracking has been dismissed

	}
	
	override fun onCameraTrackingChanged(currentMode: Int) {
	    // CameraMode has been updated
	
	}
})
`}
 />
}}
