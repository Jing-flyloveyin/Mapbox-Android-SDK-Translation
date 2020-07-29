---
title: "Cache management"
description: "阅读本文档，了解Mapbox Maps SDK for Android为开发者提供的对设备地图缓存的具体控制管理功能。"
prependJs:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import Note from '@mapbox/dr-ui/note';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
contentType: guide
language:
- Java
- Kotlin
---

当在设备上加载和查看地图时，地图瓦片会被放置在设备的**环境缓存**中。终端用户访问过的地图瓦片会存储在环境缓存中，用户下次再访问时地图的加载几乎瞬间能完成。因为渲染地图时设备不需要向服务器发送请求和接受信息，所以地图的加载速度非常快。

Maps SDK for Android提供了几种不同的管理设备缓存的方法。这些方法可以修改环境缓存、离线缓存或者存储以上两种缓存的整个数据库。

{{<Note theme="warning" title="Cache specificity">}}
缓存管理方法是在地图瓦片层上进行操作的，而不是在地图资源层上。例如，这些方法不会删除缓存中特定的`经纬边界`，地图图层或者GeoJSON `要素`。
{{</Note>}}

## 推荐的实现选项

有三个选择可以用于实现以下任何一种缓存管理方法。您可以为终端用户、云端通知系统或远程配置系统构建用户界面。您选择的选项将部分取决于您是否希望由终端用户来修改缓存。

如果要由终端用户来使用这些方法，则可以构建一个用户交互界面。这可以是一个用来清除应用程序设置页面中缓存的选项，也可以是Mapbox地图附近的一个按钮。

<!--copyeditor ignore simple-->

如果不由终端用户使用这些方法，则可以设置一个基于云端的后端服务器，以便向设备发送操作缓存的指令。一种选择是使用通知系统，如[Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)，[Amazon’s Simple Notification Service](https://aws.amazon.com/sns/)，[SendBird](https://sendbird.com/features/chat-messaging)，[OneSignal](https://documentation.onesignal.com/docs/android-sdk-setup)，或者[Urban Airship](https://www.airship.com/)。


{{
  <RelatedPage
    url="https://github.com/mapbox/mapbox-android-demo/blob/master/MapboxAndroidDemo/src/main/java/com/mapbox/mapboxandroiddemo/utils/CustomFirebaseMessagingService.java"
    title="Backend notification"
    contentType="example">
}}
了解如何将Firebase Messaging连接到Maps SDK的缓存管理中。
{{</RelatedPage>}}

另一个云后端选择是设置远程配置系统，也称为特性标记。Firebase的远程配置系统是Android开发人员的热门选择。其他管理A/B测试和特性标记的系统包括[Apptimize](https://apptimize.com/feature-flags/)和[LaunchDarkly](https://launchdarkly.com/)。远程配置系统将确定设备在运行时如何以及何时调用缓存管理方法。

{{
  <RelatedPage
    url="/android/maps/examples/cache-management/"
    title="Cache management"
    contentType="example">
}}
了解如何将Firebase Remote Config连接到Maps SDK的缓存管理中。
{{</RelatedPage>}}

<!--copyeditor enable ignore-->
## 管理方法


**`invalidateAmbientCache()`**
对环境缓存中的地图瓦片进行强制再检验，并从服务器中下载瓦片的新版本。**我们推荐您使用该方法来清除缓存**同时这也是最有效的方法，因为要从设备中删除之前的数据需要重新下载环境缓存中的瓦片。它不会删除环境缓存中的资源或删除数据库，该操作计算成本较大，可能会带来意想不到的副作用。

提示：覆盖离线区域的环境缓存瓦片将不会被这种方法的调用影响。


{{
<AndroidActivityToggle
  id="invalidate-ambient-cache"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

fileSource.invalidateAmbientCache(new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

fileSource.invalidateAmbientCache(object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {


	}
})
`}

/>
}}

**`clearAmbientCache()`**

删除环境缓存中的资源。此方法清除缓存并减少地图资源在设备上占用的空间量。然而，除非环境缓存的空间大小减少，否则资源占用空间的减少不是永久的（查看 `setMaximumAmbientCacheSize()`），因为当终端用户使用地图时，设备的环境缓存将被再次占用。这种方法的计算成本可能很高，并可能导致意想不到的副作用。**我们不建议使用这种方法，除非您确定您的使用实例需要使用这种方法。**


{{
<AndroidActivityToggle
  id="clear-ambient-cache"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

fileSource.clearAmbientCache(new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

fileSource.clearAmbientCache(object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {


	}
})
`}

/>
}}

**`setMaximumAmbientCacheSize()`**

以字节为单位设置环境缓存的最大大小。如果设置为`0`，则环境缓存被禁用。

这种方法的计算成本可能很高，因为如果环境缓存的大小减小，它将删除环境缓存中的资源。

需要注意，离线区域会减少环境缓存的可用空间，但`setMaximumAmbientCacheSize()` 不会阻止离线下载。例如，如果最大环境缓存大小为50 MB，并且离线区域已经使用了40 MB空间，那么可用的环境缓存大小将为10 MB。

{{
<AndroidActivityToggle
  id="set-ambient-cache-size"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

long DESIRED_AMBIENT_CACHE_SIZE = 5000000;

fileSource.setMaximumAmbientCacheSize(DESIRED_AMBIENT_CACHE_SIZE, new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

val DESIRED_AMBIENT_CACHE_SIZE = 5000000

fileSource.setMaximumAmbientCacheSize(DESIRED_AMBIENT_CACHE_SIZE, object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {
	
	}
})
`}

/>
}}

**`resetDatabase`**

删除存放环境缓存和离线缓存的现有数据库，并重新初始化数据库。这种方法可能会产生意想不到的副作用。**我们不建议使用这种方法，除非您确定您的使用实例需要使用这种方法。**

{{
<AndroidActivityToggle
  id="reset-database"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

fileSource.resetDatabase(new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

fileSource.resetDatabase(object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {
	
	}
})
`}

/>
}}
