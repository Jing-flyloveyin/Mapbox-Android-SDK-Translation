---
title: "Gestures"
description: "Detecting gestures in the Mapbox Maps SDK for Android."
prependJs:
  - "import constants from '../../../constants';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---

The [Mapbox Gestures for Android library](https://github.com/mapbox/mapbox-gestures-android) is used inside of the Mapbox Maps SDK for Android for gesture detection based on user's input. This library wraps [`GestureDetectorCompat`](https://developer.android.com/reference/kotlin/androidx/core/view/GestureDetectorCompat) and [`ScaleGestureDetector`](https://developer.android.com/reference/android/view/ScaleGestureDetector.html) as well as introduces implementation of rotate, move, shove, and tap gesture detectors.

The library is implemented in both the [Mapbox Maps SDK for Android](https://github.com/mapbox/mapbox-gl-native-android/) and [the library's test application](https://github.com/mapbox/mapbox-gestures-android/tree/master/app/src/main/java/com/mapbox/android/gestures/testapp).

[See the API documentation](/android/api/gestures/{{constants.GESTURES_VERSION}}/index.html) for more details on using the Gestures library.

## Installation

### Gradle

1. Open Android Studio.
2. Open up your application's `build.gradle` file.
3. Make sure that your project's `minSdkVersion` is at API 14 or higher.
4. Under dependencies, add a new build rule for the latest `mapbox-gestures-android` version (see below).
5. Click on `Sync Project with Gradle Files` near the toolbar in Android Studio.

```groovy
implementation 'com.mapbox.mapboxsdk:mapbox-android-gestures:{{constants.GESTURES_VERSION }}'
```

_**Note:** ProGuard directives are included in the Android dependencies to preserve the required classes._

The library's releases are published to Maven Central. If you'd like to test out the `master` branch build, use SNAPSHOTs which are available on Sonatype.

```java
// In the root build.gradle file
repositories {
    mavenCentral()
    maven { url "http://oss.sonatype.org/content/repositories/snapshots/" }
}
```
```java
// In the app build.gradle file
dependencies {
	implementation 'com.mapbox.mapboxsdk:mapbox-android-gestures:{{constants.GESTURES_SNAPSHOT_VERSION }}'
}
```


## Standalone usage

You have to instantiate `AndroidGestureManager` to start any gestures processing.

Set any gesture listeners that you are interested in and pass all `MotionEvent` objects from your Activity/Fragment to `AndroidGestureManager#onTouchEvent()`.


## Mutually-exclusive gestures

Mutually-exclusive gestures are possible thanks to the `AndroidGestureManager` class, which serves as a single entry point to all gesture detectors.

This means that you can pass a list of `GestureType` sets. Whenever a gesture is detected, it will check whether there are any `ProgressiveGesture`s started that are contained within the same set. If there aren't any, the listener for our detected gesture will not be notified.

You can pass mutually exclusive gesture sets in a constructor of `AndroidGestureManager` or with `AndroidGestureManager#setMutuallyExclusiveGestures()`.

For example:

{{
<AndroidActivityToggle
  id="mutually-exclusive-gestures"

java={`

Set<Integer> mutuallyExclusive1 = new HashSet<>();
mutuallyExclusive1.add(AndroidGesturesManager.GESTURE_TYPE_SHOVE);
mutuallyExclusive1.add(AndroidGesturesManager.GESTURE_TYPE_SCROLL);

Set<Integer> mutuallyExclusive2 = new HashSet<>();
mutuallyExclusive2.add(AndroidGesturesManager.GESTURE_TYPE_SHOVE);
mutuallyExclusive2.add(AndroidGesturesManager.GESTURE_TYPE_SCALE);

AndroidGesturesManager androidGesturesManager = new AndroidGesturesManager(
  context,
  mutuallyExclusive1,
  mutuallyExclusive2
);
`}

kotlin={`
val mutuallyExclusive1 = HashSet<Int>()
mutuallyExclusive1.add(AndroidGesturesManager.GESTURE_TYPE_SHOVE)
mutuallyExclusive1.add(AndroidGesturesManager.GESTURE_TYPE_SCROLL)

val mutuallyExclusive2 = HashSet<Int>()
mutuallyExclusive2.add(AndroidGesturesManager.GESTURE_TYPE_SHOVE)
mutuallyExclusive2.add(AndroidGesturesManager.GESTURE_TYPE_SCALE)

val androidGesturesManager = AndroidGesturesManager(this@MatrixApiActivity, mutuallyExclusive1, mutuallyExclusive2)
`}

/>
}}

The first set makes it certain that when a shove gesture is detected, you will no longer be notified about the scroll gestures. The shove gesture will be able to execute because scroll is not a `ProgressiveGesture`.
In the second set, when either a shove or scale is detected, you won't be notified about the other one until the first gesture finishes.

## Thresholds

You can set thresholds for supported gestures, which means that a gesture detector won't fire until it meets the threshold (like minimum rotation angle). This allows you to personalize the gestures experience how you would like.

Set thresholds using `dimen` values, rather than raw pixels, to accommodate for various screen sizes and pixel densities across Android devices. For example:

{{
<AndroidActivityToggle
  id="set-span"

java={`
androidGesturesManager.getStandardScaleGestureDetector().setSpanSinceStartThresholdResource(R.dimen.scaleSpanSinceStartThreshold);
`}

kotlin={`
androidGesturesManager?.standardScaleGestureDetector?.setSpanSinceStartThresholdResource(R.dimen.scaleSpanSinceStartThreshold)
`}

/>
}}

For thresholds that are not expressed in pixels:

{{
<AndroidActivityToggle
  id="set-threshold"

java={`
androidGesturesManager.getRotateGestureDetector().setAngleThreshold(ROTATE_ANGLE_THRESHOLD);
`}

kotlin={`
androidGesturesManager?.rotateGestureDetector?.angleThreshold = ROTATE_ANGLE_THRESHOLD
`}

/>
}}

## Velocity

Each progressive gesture with its respective `#onEnd()` callback will provide `X` velocity and `Y` velocity of the gesture at the moment when the pointer leaves the screen.

## Enable/disable and interrupt

Every gesture detector can be enabled/disabled at any time using the `#setEnabled()` method.

Additionally, every progressive gesture can be interrupted, which will force it to meet start conditions again to resume. A popular use case would be to increase the gesture's threshold when another gesture is detected:

{{
<AndroidActivityToggle
  id="enable-and-disable"

java={`
@Override
public boolean onScaleBegin(StandardScaleGestureDetector detector) {
  // forbid movement when scaling
  androidGesturesManager.getMoveGestureDetector().setEnabled(false); // this interrupts a gesture as well

  // increase rotate angle threshold when scale is detected, then interrupt to force re-check
  RotateGestureDetector rotateGestureDetector = androidGesturesManager.getRotateGestureDetector();
  rotateGestureDetector.setAngleThreshold(ROTATION_THRESHOLD_WHEN_SCALING);
  rotateGestureDetector.interrupt();

  return true;
}

@Override
public boolean onScale(StandardScaleGestureDetector detector) {
  float scaleFactor = detector.getScaleFactor();

  return true;
}

@Override
public void onScaleEnd(StandardScaleGestureDetector detector) {
  // revert thresholds values
  RotateGestureDetector rotateGestureDetector = androidGesturesManager.getRotateGestureDetector();
  rotateGestureDetector.setAngleThreshold(Constants.DEFAULT_ROTATE_ANGLE_THRESHOLD);
}

`}

kotlin={`
override fun onScaleBegin(detector: StandardScaleGestureDetector): Boolean {
        // forbid movement when scaling
        androidGesturesManager?.moveGestureDetector.isEnabled = false // this interrupts a gesture as well

        // increase rotate angle threshold when scale is detected, then interrupt to force re-check
        val rotateGestureDetector = androidGesturesManager.rotateGestureDetector
        rotateGestureDetector.angleThreshold = ROTATION_THRESHOLD_WHEN_SCALING
        rotateGestureDetector.interrupt()
    
        return true
    }

override fun onScale(detector: StandardScaleGestureDetector): Boolean {
        val scaleFactor = detector.scaleFactor

        return true
    }

override fun onScaleEnd(detector: StandardScaleGestureDetector) {
        // revert thresholds values
        val rotateGestureDetector = androidGesturesManager?.rotateGestureDetector
        rotateGestureDetector.angleThreshold = Constants.DEFAULT_ROTATE_ANGLE_THRESHOLD
    }
`}

/>
}}


## Detectors

With this library you will be able to recognize gestures using detectors provided by the AndroidX library and more.

### `StandardGestureDetector`
Wraps [`GestureDetectorCompat`](https://developer.android.com/reference/kotlin/androidx/core/view/GestureDetectorCompat) exposed via the AndroidX library that recognizes gestures like tap, double tap or scroll.

### `StandardScaleGestureDetector`
Wraps [`ScaleGestureDetector`](https://developer.android.com/reference/android/view/ScaleGestureDetector.html) exposed via the `MotionEvent` class that recognizes scale/pinch gesture.

### `MultiFingerTapGestureDetector`
Gesture detector that tell listeners whenever a multi finger tap occurred and how many fingers where involved.

### `RotateGestureDetector`
A detector that finds the angle difference between previous and current line made with two pointers (fingers).

### `ShoveGestureDetector`
Detects a vertical movement of two pointers if they are placed within a certain horizontal angle.

### `MoveGestureDetector`
Behaves similarly to `#onScroll()` contained in the `StandardGestureDetector`, but, it's a `ProgressiveGesture` that enables better filtering options, as well as thresholds.

### `SidewaysShoveGesturesDetector`
A sibling of the `ShoveGestureDetector`, but, it recognizes the two-finger shove gesture executed in a horizontal, rather than vertical, line.


## Usage with the Maps SDK

This library is shipped as a part of the [Mapbox Maps SDK for Android](https://github.com/mapbox/mapbox-gl-native-android/) and you don't have to declare any other dependencies.

To get the `AndroidGesturesManager` object which holds references to all the gesture detectors, use:

{{
<AndroidActivityToggle
  id="usage-with-maps-sdk"

java={`
AndroidGesturesManager gesturesManager = mapboxMap.getGesturesManager();
`}

kotlin={`
val gesturesManager = mapboxMap.gesturesManager
`}

/>
}}

To react to the user's input, you can register several different listeners that will be notified whenever a user interacts with a Mapbox map.

`OnMapClickListener`:

{{
<AndroidActivityToggle
  id="on-map-click"

java={`
mapboxMap.addOnMapClickListener(new MapboxMap.OnMapClickListener() {
  @Override
  public void onMapClick(@NonNull LatLng point) {
    // user clicked on the map
  }
});
`}

kotlin={`
mapboxMap.addOnMapClickListener { point ->

}
`}

/>
}}


`OnMoveListener`:

{{
<AndroidActivityToggle
  id="on-move-click"

java={`
mapboxMap.addOnMoveListener(new MapboxMap.OnMoveListener() {
  @Override
  public void onMoveBegin(MoveGestureDetector detector) {
    // user started moving the map
  }
  @Override
  public void onMove(MoveGestureDetector detector) {
    // user is moving the map
  }
  @Override
  public void onMoveEnd(MoveGestureDetector detector) {
    // user stopped moving the map
  }
});
`}

kotlin={`
mapboxMap.addOnMoveListener(object : MapboxMap.OnMoveListener {
	override fun onMoveBegin(detector: MoveGestureDetector) {
	    // user started moving the map
	}

	override fun onMove(detector: MoveGestureDetector) {
	    // user is moving the map
	}
	
	override fun onMoveEnd(detector: MoveGestureDetector) {
	    // user stopped moving the map
	}
})
`}

/>
}}


You can see a `MoveGestureDetector` object being passed in the callbacks above. This class is the underlying detector from the Mapbox Gestures for Android library.

Other popular listener interfaces that you can implement are:

 - `OnMapLongClickListener` fires when the map is touched and a single finger is held down on the map.
 - `OnRotateListener` fires when the map is rotated.
 - `OnScaleListener` fires when the map is zoomed in or out.
 - `OnShoveListener` fires when map is tilted.

### Custom `AndroidGesturesManager`

The Maps SDK offers a `MapboxMap#setGesturesManager()` method. If you create a custom `AndroidGesturesManager` object, you can use it as a parameter for that method and for the custom mapping experience that you want to create.