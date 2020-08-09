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

将用户的当前位置显示为地图标注是基于位置的应用程序一个高频使用的关键功能。Maps SDK的`LocationComponent`利用Maps SDK的[运行时样式功能](/android/maps/overview/data-driven-styling/)在地图本身中显示设备位置图标，而不是作为Android视图显示在顶部。Mapbox地图图层和图层样式让您可以对在地图上显示设备的位置进行进行精确控制。


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

| XML Option                      | Explanation 描述                                                 |
| ------------------------------- | ------------------------------------------------------------ |
| mapbox_foregroundDrawable       | 可绘制的图像，将取代蓝色圆圈。                                   |
| mapbox_foregroundTintColor      | 蓝色圆圈的颜色。                                    |
| mapbox_backgroundDrawable       | 可绘制的图像，将取代白色圆环。          |
| mapbox_backgroundTintColor      | 白色圆环的颜色。                                            |
| mapbox_bearingDrawable          | 可绘制的图像，将取代蓝色三角。     |
| mapbox_bearingTintColor         | 蓝色三角的颜色。                                          |
| mapbox_navigationDrawable       | 可绘制的图像，代表导航状态的图标。          |
| mapbox_accuracyAlpha            | 设备位置图标周围较大的浅蓝色圆圈。 |
| mapbox_accuracyColor            | 设备位置图标周围较大的淡蓝色圆圈的颜色。 |
| mapbox_elevation                | 地图和设备位置图标之间的空间大小。仰角将调整白色圆圈下阴影的大小。传递`0f`作为方法参数将移除阴影效果。|
| mapbox_compassAnimationEnabled  | 启用或禁用蓝色三角形罗盘的平滑动画。 |
| mapbox_accuracyAnimationEnabled | 启用或禁用设备位置图标周围较大的淡蓝色精度圆圈的平滑动画。 |
| mapbox_pulsingLocationCircleEnabled | 启用或禁用脉冲圈。 |
| mapbox_pulsingLocationCircleFadeEnabled | 启用或禁用脉冲圆的淡化。如果淡化，圆的不透明度会随着半径的增大而降低。
| mapbox_pulsingLocationCircleColor | 设置脉冲圈的颜色。
| mapbox_pulsingLocationCircleDuration |设置脉冲圆的单次脉冲所需的毫秒数。
| mapbox_pulsingLocationCircleRadius | 单个脉冲应扩大脉冲圈的最大半径。
| mapbox_pulsingLocationCircleAlpha | 设置脉冲圈的不透明度，范围为0到1。不透明度为1时，图层将完全不透明。
| mapbox_pulsingLocationCircleInterpolator | 设置脉冲圆的插值器动画。

{{<Note>}}

`mapbox_compassAnimationEnabled` and `mapbox_accuracyAnimationEnabled` options are available because smooth animation of the `LocationComponent` can have a significant impact on a device's battery level. Disabling smooth compass and accuracy animations during scenarios where it'd be the only animation running on the device can decrease CPU usage by a significant amount.

`mapbox_compassAnimationEnabled`和 `mapbox_accuracyAnimationEnabled`选项是可用的，因为 `LocationComponent`的动画平滑会对设备的电池消耗水平产生重大影响。作为设备上唯一运行的动画的情况下，禁用罗盘和精度的动画平滑可以显著降低CPU的使用量。

{{</Note>}}

Adding a pulsing circle to the `LocationComponent` is another styling option. The pulsing circle expands its radius over a certain amount of time to make the `LocationComponent` look as if it's a beacon that is emitting energy. This UI can give the `LocationComponent` and your map an even more engaging and energetic feel as the device stays still or moves around the map.

为`LocationComponent`添加一个脉冲圈是另一个样式选项。脉冲圈会在一定时间内扩大其半径，使`LocationComponent`看起来就像一个正在发射能量的信标。当设备在地图上静止或移动时，这个UI可以使`LocationComponent`和您的地图带更具吸引力和活力。

{{
  <AndroidDeviceFrame
    src={pulsingLocationComponent}
    title="Adding a pulsing circle to LocationComponent"
    rotation="portrait"
  />
}}

The pulsing circle is disabled by default. To show the default pulsing UI, pass `true` through the `LocationComponentOptions.builder()`'s `pulseEnabled()` method to enable the pulsing. The `LocationComponentOptions.builder()` has several methods (seen listed above) to customize the pulsing UI.

脉冲圈在默认状态下是禁用的。如需显示默认的脉动UI，可以通过`LocationComponentOptions.builder()`的`pulseEnabled()`方法传递`true`来启用脉冲。`LocationComponentOptions.builder()`有几个方法（见上表列出）来定制脉冲用户界面。

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

| XML Option                      | Explanation 描述                                                |
| ------------------------------- | ------------------------------------------------------------ |
| mapbox_foregroundDrawableStale  | Drawable image that would replace the grey circle's location.可绘制的图像，将取代灰圈的位置。 |
| mapbox_foregroundStaleTintColor | The grey circle's color. 灰圈的颜色。                                     |
| mapbox_backgroundDrawableStale  | Drawable image that would replace the white circle's location.绘制的图像，将取代白色圆环。 |
| mapbox_backgroundStaleTintColor | The white circle's color. 白环的颜色。                                   |

## Activating 激活

`MapboxMap#getLocationComponent()` fetches the component and `LocationComponent#activateLocationComponent()` activates it.
`MapboxMap#getLocationComponent()`获取组件，`LocationComponent#activateLocationComponent()`激活它。

The `activateLocationComponent()` method requires a built `LocationComponentActivationOptions` class. The `LocationComponentActivationOptions` class offers a convenient way to set activation options such as whether to use the default `LocationEngine` or [a built `LocationComponentOptions` object](/android/maps/overview/location-component/#customization).

使用`activateLocationComponent()`方法需要一个内置的`LocationComponentActivationOptions`类。`LocationComponentActivationOptions`类提供了一种方便的方式来设置激活选项，例如是否使用默认的`LocationEngine`或[内置的`LocationComponentOptions`对象](/android/maps/overview/location-component/#customization)。

Create a `LocationComponentActivationOptions` class with the builder pattern that is provided to you via the Maps SDK.
使用通过Maps SDK提供给您的构建器模式创建一个`LocationComponentActivationOptions`类。

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
一旦用户授予位置权限**且**地图已完全加载，`LocationComponent`将被检索并激活。

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
    title="Showing device location 显示设备位置"
    contentType="example">
}}
Add the device's location to the map.
在地图上添加设备位置的显示。
{{</RelatedPage>}}

## Visibility 可见度

There is a single method to either enable or disable the `LocationComponent`'s visibility after activation. The `setLocationComponentEnabled()` method requires a `true`/`false` boolean parameter. When set to `false`, this method will hide the device location icon and stop map camera animations from occurring.

在激活后，有一种方法来启用或禁用`LocationComponent`的可见性。`setLocationComponentEnabled()`方法需要一个`true`/`false`布尔参数。当设置为 `false`时，该方法将隐藏设备位置图标，并停止地图相机动画的应用。


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
`RenderMode`类包含设备位置图像的预设选项。

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

| `RenderMode` | 描述                                                  |
| ------------ | ------------------------------------------------------------ |
| `NORMAL`     | 该模式显示设备位置，忽略指南针和GPS方位（无箭头呈现）。{{<AppropriateImage imageId="maps-overview-location-component-normal-render" className="block mx-auto pt18" />}} |
| `COMPASS`    | 这种模式可以显示设备的位置，以及考虑设备方位的指南针箭头。{{<AppropriateImage imageId="maps-overview-location-component-compass-render" className="block mx-auto pt18" />}} |
| `GPS`        | 该模式显示了设备的位置，并使用提供给 `LocationComponent`的 `Location`更新中被更新了的图标。 {{<AppropriateImage imageId="maps-overview-location-component-gps-render" className="block mx-auto pt18" />}} |

{{<Note 小贴士>}}
The actual device location icon is highly customizable with methods such as `LocationComponentOptions#foregroundDrawable()` and `LocationComponentOptions#backgroundDrawable()`.

通过`LocationComponentOptions#foregroundDrawable()`和`LocationComponentOptions#backgroundDrawable()`等方法，您可以高度自定义实际的设备位置图标。
{{</Note 小帖士>}}


## `CameraMode`

The method `LocationComponent#setCameraMode(@CameraMode.Mode int cameraMode)` allows developers to set specific camera tracking instructions as the device location changes.

使用`LocationComponent#setCameraMode(@CameraMode.Mode int cameraMode)`方法允许开发者在设备位置变化时设置特定的camera追踪指令。

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
共有7种 `CameraMode`可供选择。


| `CameraMode`         | 描述                                                 |
| -------------------- | ------------------------------------------------------------ |
| `NONE`               | 无camera追踪。                                          |
| `NONE_COMPASS`       | Camera不追踪位置，但可以追踪罗盘方位。 |
| `NONE_GPS`           | Camera不追踪位置，但会跟踪GPS`Location`方位。 |
| `TRACKING`           | Camera追踪设备位置，不考虑方位。 |
| `TRACKING_COMPASS`   | Camera追踪设备位置，同时追踪设备罗盘提供的方位。 |
| `TRACKING_GPS`       | Camera追踪设备位置，方位则由归一化的`Location#getBearing()`提供。|
| `TRACKING_GPS_NORTH` | Camera追踪设备位置，方位始终设置为北（0）。 |


Here are a few examples from [the `LocationModesActivity` in the Maps SDK's test application](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDKTestApp/src/main/java/com/mapbox/mapboxsdk/testapp/activity/location/LocationModesActivity.java):
以下是[地图SDK测试应用程序中的`LocationModesActivity`](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDKTestApp/src/main/java/com/mapbox/mapboxsdk/testapp/activity/location/LocationModesActivity.java)中的几个例子：

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

当除了`CameraMode#NONE`之外的任何一种camera模式被激活时，传统的camera转换将被取消。使用`LocationComponent#zoomWhileTracking`和`LocationComponent#tiltWhileTracking`来操作处于追踪状态的camera。将这两个功能与传统的camera转换和`MapboxMap#CancelableCallback`结合使用，可以实现流畅的转换。

When instantiating the `LocationComponent` for the first time, the map's max/min zoom levels will be set to`LocationComponentOptions#MAX_ZOOM_DEFAULT` and `LocationComponentOptions#MIN_ZOOM_DEFAULT` respectively. Adjust the zoom range with the `LocationComponentOptions#maxZoom()` and `LocationComponentOptions#minZoom()` methods in the `LocationComponentOptions` class.

当首次实例化`LocationComponent`时，地图的最大/最小缩放级别将分别设置为`LocationComponentOptions#MAX_ZOOM_DEFAULT`和`LocationComponentOptions#MIN_ZOOM_DEFAULT`。通过`LocationComponentOptions`类中的`LocationComponentOptions#maxZoom()`和`LocationComponentOptions#minZoom()`方法，可以调整缩放范围。

## Gesture thresholds while camera tracking camera追踪时的手势阈值

The `LocationComponent` is integrated with [the Mapbox Gestures for Android library](https://docs.mapbox.com/android/maps/overview/gestures/). The component will adjust the camera's focal point and increase thresholds to enable camera manipulation, like zooming in and out, without breaking tracking. Enabling this feature is explicitly opt-in because it overwrites custom gestures detection implementation set with `MapboxMap#setGesturesManager(AndroidGesturesManager, boolean, boolean)`.

`LocationComponent`已实现与[Mapbox Gestures for Android库](https://docs.mapbox.com/android/maps/overview/gestures/)的集成。该组件将调整camera的焦点并增加阈值，以实现camera操作，如放大和缩小，而不破坏追踪。启用这个功能需要清晰的主动选择，因为它将覆盖用`MapboxMap#setGesturesManager(AndroidGesturesManager, boolean, boolean)`设置的自定义手势检测实现。

To enable the feature use `LocationComponentOptions#trackingGesturesManagement(boolean)`.
要启用该功能，请使用`LocationComponentOptions#trackingGesturesManagement(boolean)`。

You can adjust thresholds that need to be exceeded to break the tracking for one pointer gestures (like panning the map, double-tap to zoom in) and multi-pointer gestures (like scale gesture, two-tap to zoom out):
- `LocationComponentOptions#trackingInitialMoveThreshold(float)` adjusts the minimum single pointer movement in pixels required to break camera tracking.
- `LocationComponentOptions#trackingMultiFingerMoveThreshold(float)` adjusts minimum multi pointer movement in pixels required to break camera tracking (for example during scale gesture).
- If either of these thresholds are exceeded and tracking is dismissed, developers can listen to this with a `OnCameraTrackingChangedListener`:

针对单指针手势（如平移地图，双击放大）和多指针手势（如缩放手势，双击放大），您可以调整打破追踪所需要超过的阈值。
- `LocationComponentOptions#trackingInitialMoveThreshold(float)`可调整打破camera追踪所需的最小单指针移动像素。
- `LocationComponentOptions#trackingMultiFingerMoveThreshold(float)`可调整打破camera追踪所需的最小多指针像素移动量（例如在缩放手势期间）。
- 如果超过其中任何一个阈值，追踪就会被驳回，开发者可以使用`OnCameraTrackingChangedListener`来监听。



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
