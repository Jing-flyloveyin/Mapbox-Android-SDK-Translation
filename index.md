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



The Mapbox Maps SDK for Android是一个开源地图工具集，用于在您的 Android 应用程序内显示地图。

{{
  <a href='https://play.google.com/store/apps/details?id=com.mapbox.mapboxandroiddemo&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1' className="fr-mm block wmax180 mx-auto pl24">
    <AppropriateImage imageId="maps-overview-google-play-badge" alt='Get it on Google Play' />
  </a>
}}

在[Google Play Store中的Mapbox演示应用](https://play.google.com/store/apps/details?id=com.mapbox.mapboxandroiddemo&hl=en) 包含许多关于如何使用 Mapbox Maps SDK for Android 的示例。 演示应用和 [示例页面](https://docs.mapbox.com/android/maps/examples/) 将会为您展示出Mapbox Maps SDK的强大功能。

{{<Note title="迁移到最新版本">}}
关于从旧版本迁移到新版本的Mapbox Maps SDK for Android的信息，请参阅：

- **v8.x.x 至 v9.x.x**: 要在应用中通过此版本Maps SDK实现地图则应用必须支持[AndroidX](https://developer.android.com/jetpack/androidx).
- **v7.x.x 至 v8.x.x**: 此版本改进了月度活跃用户的计算方式。无需迁移指南，但您仍可以在 [更新日志](https://github.com/mapbox/mapbox-gl-native/releases/tag/android-v8.0.0) 和 [按产品定价](https://docs.mapbox.com/help/account/pricing/#maps-sdk-for-ios-and-android) 中查看更多信息。
- [**v6.x.x 至 v7.x.x**](https://github.com/mapbox/mapbox-gl-native/wiki/Android-6.x-to-7.x-migration-guide)
- [**v5.x.x 至 v6.x.x**](https://github.com/mapbox/mapbox-gl-native/wiki/Android-5.x-to-6.x-migration-guide)
- [**v4.x.x 至 v5.x.x**](https://github.com/mapbox/mapbox-gl-native/wiki/Android-4.x-to-5.x-migration-guide)
{{</Note>}}

## 安装Maps SDK

在您开始使用Maps SDK开发应用程序前，您需要配置凭据并将此SDK添加为项目依赖项。 此文档描述的是安装稳定版本SDK的步骤， 但您也可以使用每日构建（Nightly Build，例如SNAPSHOT）或者测试（Beta）版本（如果可用）。 关于如何操作的更多信息，请参阅项目的 [GitHub 代码仓库](https://github.com/mapbox/mapbox-gl-native-android/blob/master/README.md)。

如果您的应用程序接近或超过 65k 方法数量限制，可以通过在应用程序内启用 ProGuard 来缓解此问题。ProGuard 指令包含在 Android 依赖项中，用以保留必需类。您还可以通过使用 APK 拆分来减小 APK 文件大小。

### 配置凭据

在安装Mapbox Maps SDK for Android之前，您需要准备好适当的凭据。Maps SDK要求您的Mapbox账户提供两项敏感信息。如果您还没有Maxpbox账户，请：[注册](https://account.mapbox.com/auth/signup/) 并转到您的[“账户”页面](https://account.mapbox.com/)。 您需要:

- **一个公开访问令牌（Public access token）**: 在您账户的[“令牌”（Tokens）页面](https://account.mapbox.com/access-tokens/)，您可以复制您的 _默认公开令牌（default public token）_ 或点击**创建一个令牌（Create a token）** 按钮来新建一个公开访问口令。
- **一个包含 `Downloads:Read` 域的私密访问令牌（secret access token）**。如果您计划 [通过直接下载的方式](#add-the-dependency)来安装Maps SDK，则无需进行此项配置，可以直接跳过此步骤。
   1. 在您账户的[“令牌”（Tokens）页面](https://account.mapbox.com/access-tokens/)，点击 **创建一个令牌（Create a token）** 按钮。
   2. 在创建口令的页面上，为此口令填写一个名字并确保 `Downloads:Read` 旁的复选框被选中。
   3. 点击页面底部的 **创建令牌（Create token）** 按钮来完成口令的创建。
   4. 现在您已经完成了一个 _私密访问令牌（secret token）_ 的创建, 这意味着您只有一次机会复制并将其保存在安全的位置。

您不应将以上创建好的令牌放在可以被公开访问到的源代码中，因为这样可能会使令牌被未经允许的用户访问得到。相反，您应该将其存放在你电脑上的安全位置并利用[Gradle properties](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties) 的优势来保证它们只会在你的应用编译生成时被加入应用。一旦配置步骤完成，您就可以在应用的其它部分访问您的凭据。

#### 配置您的私密令牌

{{ <Note title="请注意">如果您计划通话直接下载的方式安装Maps SDK则无需配置私密口令并可以跳过此步骤。</Note>}}

为了防止私密令牌（secret token）泄露，请将其添加为环境变量：

1. 在[Gradle用户主目录](https://docs.gradle.org/current/userguide/directory_layout.html#dir:gradle_user_home)文件夹中新建或找到已经存在的`gradle.properties`文件。此文件夹可以在`«USER_HOME»/.gradle`位置被找到。您找到了或创建了此文件之后，其路径应为 `«USER_HOME»/.gradle/gradle.properties`。您可以在[Gradle官方文档](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties)中阅读与Gradle配置相关的更多信息。
2. 将您的私密访问令牌（secret token）添加到您的 `gradle.properties` 文件中: 

```
MAPBOX_DOWNLOADS_TOKEN=PASTE_YOUR_SECRET_TOKEN_HERE
```

#### 配置您的公开访问令牌

您可以选用多种方式来配置公开访问令牌。本网站的许多示例和代码片段都假定您的令牌与其它字符串一起存放在项目的文件中。如果您也想用这种方式来管理您的公开访问令牌，请打开您项目的 `R.strings.xml` 文件并添加以下字符串资源：

```xml
<string name="mapbox_access_token">MAPBOX_ACCESS_TOKEN</string>
```

如果需要 [更新您的访问令牌](https://docs.mapbox.com/help/how-mapbox-works/access-tokens/)，请在 `R.strings.xml` 文件中更新令牌的值。

### 添加依赖

Mapbox通过 **Maven** 和 **直接下载** 两种方式提供Maps SDK依赖。您可以根据您的喜好来选择：

{{<MapsInstall
  releaseType="stable" 
  version={constants.MAP_SDK_VERSION}
/>}}


### 配置权限

从Maps SDK版本5.0开始，我们逐渐利用 Manifest 合并功能，减少在应用程序的 Manifest 文件中包含任何 Maps SDK 所需项。 如果您打算在地图上显示用户的位置或者获取用户的位置信息，_无论_ 是精准位置信息 Fine **还是** 粗略位置信息，您都需要在Manifest文件中添加权限。同时，您也应该在运行时使用[`PermissionsManager`](https://docs.mapbox.com/android/core/overview/#permissionsmanager)来检查用户位置权限。

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
