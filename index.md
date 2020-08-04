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

从Maps SDK版本5.0开始，我们逐渐利用 Manifest 合并功能，减少在应用程序的 Manifest 文件中包含任何 Maps SDK 所需项。 如果您打算在地图上显示用户的位置或者获取用户的位置信息，_无论_ 是精准位置信息 **还是** 粗略位置信息，您都需要在Manifest文件中添加权限。同时，您也应该在运行时使用[`PermissionsManager`](https://docs.mapbox.com/android/core/overview/#permissionsmanager)来检查用户位置权限。

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### 添加地图

打开您想添加地图的 Activity 文件，并将以下代码添加到该文件。

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

打开Activity的XML文件并在布局中添加 `mapView` 。

```xml
<com.mapbox.mapboxsdk.maps.MapView
  android:id="@+id/mapView"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  />
```

### 生命周期相关方法

 `MapView` 包含有自己的生命周期相关的方法，用来管理 Android 的 OpenGL 生命周期, 并需要从包含 MapView 的 Activity 中直接调用该方法。为了使应用程序能够正确调用 MapView 的生命周期方法，您必须在包含 MapView 的 Activity 中覆盖以下方法，并调用相应的 MapView 方法。例如，您的 `onStart()` 应该如下所示：

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


正如以上被重写的 `onStart()` 方法，以下生命周期相关方法也应该被重写并且在其中包含对应的 `MapView` :

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


如果您是在Fragment中使用地图，请在 `onDestroyView()` 中而不是 `onDestroy()` 中调用 `mapview.onDestroy()` 生命周期方法:

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

## 归属

您必须在任何使用 Mapbox Maps SDK for Android 的地图上包含 Mapbox的文字水印和归属通知。我们提供一个归属布局，其中包含所有必需信息, 可以使用 xml 或 `UiSettings` 对象进行自定义。

[您可以调整 Mapbox 水印和归属通知的位置](/android/maps/overview/ui-settings/#logo)，但是它们必须在地图上保持可见。您也可以更改归属通知的背景和文本颜色以最佳地匹配您的设计美学，但是所有信息必须可读。

您不应该以其他方式更改 Mapbox 水印或文本归属通知。如果您想移动或移除 Mapbox 水印, 请 [联系我们的销售团队](https://www.mapbox.com/contact/sales/) 以讨论我们的企业级支持计划中更多的可行性。

{{
  <RelatedPage
    url="https://docs.mapbox.com/help/how-mapbox-works/attribution/"
    title="归属原则"
    contentType="guide">
}}
了解更多关于Mapbox的归属要求和原因。
{{</RelatedPage>}}

## 选择退出Telemetry
Mapbox Telemetry 是一个包含在此 SDK 中的 [强大的位置分析平台](https://www.mapbox.com/telemetry/) 。默认情况下，每当主机应用触发信息收集时，匿名位置和使用数据会发送到 Mapbox。[Mapbox 服务条款](https://www.mapbox.com/tos/) 要求您的应用为用户提供一种单独选择退出 Mapbox Telemetry 的方法；作为 [归属](#attribution) 控制的一部分，其实默认提供的。如果您隐藏归属控制，必须提供可供用户使用的替代性退出选项。

## MapView XML属性

可以在 `MapView` 中添加属性来进一步定制地图的行为，例如Camera起始位置、启用倾斜或调整指南针在屏幕上的位置。所有的 `MapView` XML属性都以 `mapbox_` 开头以便于区分和避免与其他库发生任何潜在冲突。由于 Android Studio 的当前实现，您不能通过输入来自动生成 `MapView` 属性。请在此处 [查看 `MapView` 属性的完整列表](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDK/src/main/res-public/values/public.xml)。

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

## 了解 GeoJSON

Mapbox Maps SDK for Android的许多部分都基于GeoJSON，这是一种用于表示地图数据的开放标准文件格式。许多Maps SDK方法将GeoJSON几何图形作为参数或返回GeoJSON特征。 例如，Maps SDK有一个 [ `GeoJSONSource` 类](/android/maps/overview/data-driven-styling/#geojson) 用于将数据添加到地图样式中，一个有用的Maps SDK [查询方法](/android/maps/overview/query/#query-rendered-features) 会返回一个GeoJSON `特征（Feature）` 列表。

 [Mapbox Java SDK 的 GeoJSON 模块](/android/java/overview/geojson/) 为Maps SDK提供了GeoJSON支持。虽然理解GeoJSON对于使用Maps SDK并不是必须的，但更加熟悉GeoJSON将大大增强您对Maps SDK的理解和使用。请务必阅读 [如何在Maps SDK中使用Java SDK的GeoJSON模块](/android/java/overview/geojson/#use-with-the-maps-sdk).
