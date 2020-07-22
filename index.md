---
title: "Introduction"
description: "The official overview documentation for the Mapbox Maps SDK for Android. Learn how to customize your Android app's maps, visualize data, and much more."
prependJs:
  - "import OverviewHeader from '@mapbox/dr-ui/overview-header';"
  - "import Note from '@mapbox/dr-ui/note';"
  - "import AppropriateImage from '../../../components/appropriate-image';"
  - "import MapsInstall from '../../../components/maps-install';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import constants from '../../../constants';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---



The Mapbox Maps SDK for Android is a toolset for displaying maps inside of your Android application.

{{
  <a href='https://play.google.com/store/apps/details?id=com.mapbox.mapboxandroiddemo&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1' className="fr-mm block wmax180 mx-auto pl24">
    <AppropriateImage imageId="maps-overview-google-play-badge" alt='Get it on Google Play' />
  </a>
}}

[Mapbox's demo app on the Google Play Store](https://play.google.com/store/apps/details?id=com.mapbox.mapboxandroiddemo&hl=en) includes many examples of how to use the Mapbox Maps SDK for Android. The demo app and [examples page](https://docs.mapbox.com/android/maps/examples/) will illustrate the power of the Mapbox Maps SDK for Android.

{{<Note title="Upgrade to the most recent version">}}
For information about migrating from older versions of the Mapbox Maps SDK for Android to newer ones, see:

- **v8.x.x to v9.x.x**: Applications implementing this version of the Maps SDK must support [AndroidX](https://developer.android.com/jetpack/androidx).
- **v7.x.x to v8.x.x**: This version change improved the method for counting monthly active users. No migration guide is required, but you can read more about these changes in the [changelog](https://github.com/mapbox/mapbox-gl-native/releases/tag/android-v8.0.0) and the [Pricing by products](https://docs.mapbox.com/help/account/pricing/#maps-sdk-for-ios-and-android) guide.
- [**v6.x.x to v7.x.x**](https://github.com/mapbox/mapbox-gl-native/wiki/Android-6.x-to-7.x-migration-guide)
- [**v5.x.x to v6.x.x**](https://github.com/mapbox/mapbox-gl-native/wiki/Android-5.x-to-6.x-migration-guide)
- [**v4.x.x to v5.x.x**](https://github.com/mapbox/mapbox-gl-native/wiki/Android-4.x-to-5.x-migration-guide)
{{</Note>}}

## Install the Maps SDK

Before starting to develop your application with the Maps SDK, you'll need to configure your credentials and add the SDK as a dependency. This document describes the steps to install the stable version of the Maps SDK, but you can also use the nightly build (i.e. SNAPSHOT) or the beta version, if one is available. Find more information about how to do this inside the project’s [GitHub repository](https://github.com/mapbox/mapbox-gl-native-android/blob/master/README.md).

If your application is close to or exceeds the 65k method count limit, you can mitigate this problem by enabling ProGuard inside your application. ProGuard directives are included in the Android dependencies to preserve the required classes. You can also shrink the file APK file size by making use of APK splitting.

### Configure credentials

Before installing the Mapbox Maps SDK for Android, you will need to gather the appropriate credentials. The Maps SDK requires two pieces of sensitive information from your Mapbox account. If you don't have a Mapbox account: [sign up](https://account.mapbox.com/auth/signup/) and navigate to your [Account page](https://account.mapbox.com/). You'll need:

- **A public access token**: From your account's [tokens page](https://account.mapbox.com/access-tokens/), you can either copy your _default public token_ or click the **Create a token** button to create a new public token.
- **A secret access token with the `Downloads:Read` scope**. If you plan to install the Maps SDK [via direct download](#add-the-dependency), you do not need to configure a secret token and can skip this step.
   1. From your account's [tokens page](https://account.mapbox.com/access-tokens/), click the **Create a token** button.
   2. From the token creation page, give your token a name and make sure the box next to the `Downloads:Read` scope is checked.
   3. Click the **Create token** button at the bottom of the page to create your token.
   4. The token you've created is a _secret token_, which means you will only have one opportunity to copy it somewhere secure.

You should not expose these access tokens in publicly-accessible source code where unauthorized users might find them. Instead, you should store them somewhere safe on your computer and take advantage of [Gradle properties](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties) to make sure they're only added when your app is compiled. Once this configuration step has been completed, you will be able to reference your credentials in other parts of your app.

#### Configure your secret token

{{ <Note>If you plan to install the Maps SDK via direct download, you do not need to configure a secret token and can skip to the next step.</Note>}}

To avoid exposing your secret token, add it as an environment variable:

1. Find or create a `gradle.properties` file in your [Gradle user home folder](https://docs.gradle.org/current/userguide/directory_layout.html#dir:gradle_user_home). The folder can be found at `«USER_HOME»/.gradle`. Once you have found or created the file, its path should be `«USER_HOME»/.gradle/gradle.properties`. You can read more about Gradle properties in the [official Gradle documentation](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties).
2. Add your secret token your `gradle.properties` file: 

```
MAPBOX_DOWNLOADS_TOKEN=PASTE_YOUR_SECRET_TOKEN_HERE
```

#### Configure your public token

There are many ways to configure your public access token. Many of the examples and code snippets on this site assume your token is stored in a file in your project with other string values. If you would like to manage your public access token this way, open your project's `R.strings.xml` file and add the following string resource:

```xml
<string name="mapbox_access_token">MAPBOX_ACCESS_TOKEN</string>
```

If you ever need to [rotate your access token](https://docs.mapbox.com/help/how-mapbox-works/access-tokens/), you will need to update the token value in your `R.strings.xml` file. 

### Add the dependency

Mapbox provides the Maps SDK via **Maven** and **direct download**. You can choose whichever you prefer.

{{<MapsInstall
  releaseType="stable" 
  version={constants.MAP_SDK_VERSION}
/>}}


### Configure permissions

Starting with the 5.0 version of the Maps SDK, you can use the Manifest merge feature to reduce the need to include any Maps SDK required things inside of your application's manifest file. You'll need to add _either_ the Fine **or** Coarse location permission if you plan to display a user's location on the map or get the user's location information. The user location permission should also be checked during runtime using the [`PermissionsManager`](https://docs.mapbox.com/android/core/overview/#permissionsmanager).

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### Add a map

Open the activity you'd like to add a map to and use the code below.

{{
<AndroidActivityToggle
  id="add-a-map"

java={`
private MapView mapView;

@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  Mapbox.getInstance(this, getString(R.string.mapbox_access_token));

  setContentView(R.layout.activity_main);

  mapView = (MapView) findViewById(R.id.mapView);
  mapView.onCreate(savedInstanceState);
  mapView.getMapAsync(new OnMapReadyCallback() {
    @Override
    public void onMapReady(@NonNull MapboxMap mapboxMap) {

    	mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
      	@Override
      	public void onStyleLoaded(@NonNull Style style) {
    
        	// Map is set up and the style has loaded. Now you can add data or make other map adjustments


      	}
      });
    
    }
  });
}
`}

kotlin={`
private var mapView: MapView? = null

override fun onCreate(savedInstanceState: Bundle?) {
  super.onCreate(savedInstanceState)

  Mapbox.getInstance(this, getString(R.string.mapbox_access_token)

  setContentView(R.layout.activity_main)

  mapView = findViewById(R.id.mapView)
  mapView?.onCreate(savedInstanceState)
  mapView?.getMapAsync { mapboxMap ->

  	mapboxMap.setStyle(Style.MAPBOX_STREETS) {

      // Map is set up and the style has loaded. Now you can add data or make other map adjustments


  	}

  }
}
`}

/>
}}

Open the activity's XML layout file and add the `mapView` within your layout.

```xml
<com.mapbox.mapboxsdk.maps.MapView
  android:id="@+id/mapView"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  />
```

### Lifecycle methods

The `MapView` contains its own lifecycle methods for managing Android's OpenGL lifecycle, which must be called directly from the containing Activity. In order for your app to correctly call the MapView's lifecycle methods, you must override the following lifecycle methods in the Activity that contains the MapView and call the respective MapView method. For example, your `onStart()` method should look like this:

{{
<AndroidActivityToggle
  id="on-start"

java={`
@Override
protected void onStart() {
	super.onStart();
	mapView.onStart();
}
`}

kotlin={`
override fun onStart() {
	super.onStart()
	mapView?.onStart()
}
`}
/>
}}


Like the `onStart()` override above, the following lifecycle methods also need to be overridden and include the matching `MapView` method:

{{
<AndroidActivityToggle
  id="lifecycle-methods"

java={`
@Override
protected void onResume() {
	super.onResume();
	mapView.onResume();
}

@Override
protected void onPause() {
	super.onPause();
	mapView.onPause();
}

@Override
protected void onStop() {
	super.onStop();
	mapView.onStop();
}

@Override
protected void onSaveInstanceState(Bundle outState) {
	super.onSaveInstanceState(outState);
	mapView.onSaveInstanceState(outState);
}

@Override
public void onLowMemory() {
    super.onLowMemory();
    mapView.onLowMemory();
}

@Override
protected void onDestroy() {
	super.onDestroy();
	mapView.onDestroy();
}
`}

kotlin={`
override fun onResume() {
	super.onResume()
	mapView?.onResume()
}

override fun onPause() {
	super.onPause()
	mapView?.onPause()
}

override fun onStop() {
	super.onStop()
	mapView?.onStop()
}

override fun onSaveInstanceState(outState: Bundle?) {
	super.onSaveInstanceState(outState)
	if (outState != null) {
	  mapbox.onSaveInstanceState(outState)
	}
}

override fun onLowMemory() {
	super.onLowMemory()
	mapView?.onLowMemory()
}

override fun onDestroy() {
	super.onDestroy()
	mapView?.onDestroy()
}
`}
/>
}}


If you're using a fragment, call `mapview.onDestroy()` inside the fragment's `onDestroyView()` method rather than inside `onDestroy()`:

{{
<AndroidActivityToggle
  id="fragment-ondestroyview"

java={`
@Override
public void onDestroyView() {
	super.onDestroyView();
	mapView?.onDestroy();
}
`}

kotlin={`
override fun onDestroyView() {
	super.onDestroyView()
	mapView?.onDestroy()
}
`}

/>
}}

## Attribution

You must include the Mapbox wordmark and attribution notice on any map that uses the Mapbox Maps SDK for Android. The SDK provides an attribution layout that includes all required information and can be customized either in xml or using the `UiSettings` object.

[You may adjust the position of the Mapbox wordmark and attribution notice](/android/maps/overview/ui-settings/#logo), but they must stay visible on the map. You may also change the background and text color of the attribution notice to match your design aesthetics, but all information must be legible.

You may not otherwise alter the Mapbox wordmark or text attribution notice. If you wish to move or to remove the Mapbox wordmark, please [contact our sales team](https://www.mapbox.com/contact/sales/) to discuss options available under our Enterprise plans.

{{
  <RelatedPage
    url="https://docs.mapbox.com/help/how-mapbox-works/attribution/"
    title="Attribution guidelines"
    contentType="guide">
}}
Learn more about what kinds of attribution Mapbox requires and why.
{{</RelatedPage>}}

## Telemetry opt out
Mapbox Telemetry is a [powerful location analytics platform](https://www.mapbox.com/telemetry/) included in this SDK. By default, the SDK sends anonymized location and usage data to Mapbox whenever the host app causes it to be gathered. The [Mapbox Terms of Service](https://www.mapbox.com/tos/) require your app to provide users with a way to individually opt out of Mapbox Telemetry, which is provided automatically as part of the [attribution](#attribution) control. If you hide the attribution control, you must provide an alternative opt out for your users to use.

## MapView XML attributes

XML attributes can be added inside of the XML `MapView` to further customize map behavior, such as setting the starting camera position, enabling tilt, or adjusting the compass' location on the screen. All `MapView` XML attributes start with `mapbox_` for identification and for removing any potential conflicts with other libraries. Due to the current implementation of Android Studio, you can't autogenerate `MapView` attributes by typing. [View the full list of `MapView` attributes here](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/res-public/values/public.xml).

Some examples of `MapView` attributes are:

```xml
<com.mapbox.mapboxsdk.maps.MapView
  mapbox:mapbox_cameraTargetLat="-36.84"
  mapbox:mapbox_cameraTargetLng="174.76"
  mapbox:mapbox_cameraZoom="10"
  mapbox:mapbox_cameraBearing="34.33"
  mapbox:mapbox_cameraTilt="50.25"
  mapbox:mapbox_cameraZoomMax="12.41"
  mapbox:mapbox_cameraZoomMin="6"
  mapbox:mapbox_uiRotateGestures="false"/>
```

## Understanding GeoJSON

Many parts of the Mapbox Maps SDK for Android are based on GeoJSON, an open standard file format for representing map data. Many Maps SDK methods take GeoJSON geometries as a parameter or return GeoJSON features. For example, the Maps SDK has [a `GeoJSONSource` class](/android/maps/overview/data-driven-styling/#geojson) for adding data to a map style, and one helpful Maps SDK's [querying method](/android/maps/overview/query/#query-rendered-features) returns a list of GeoJSON `Feature`s.

The [Mapbox Java SDK's GeoJSON module](/android/java/overview/geojson/) provides GeoJSON support to the Maps SDK. Although it's not required to use the Maps SDK, becoming more familiar with GeoJSON will greatly enhance your understanding and usage of the Maps SDK. Make sure to read [how to use the Java SDK's GeoJSON module with the Maps SDK](/android/java/overview/geojson/#use-with-the-maps-sdk).