---
title: "Snapshotter"
description: "获取地图的静态快照，用于设备上的应用程序，通知以及用户分享。"
prependJs:
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
contentType: guide
language:
- Java
- Kotlin
---

使用Mapbox Maps SDK for Android的快照功能，可以获得一张用于安卓项目的静态地图图片。可以获取任意Mapbox地图的快照，并将图片添加至：

- 您的APP中的其他页面
- 主界面
- 主界面的窗口小部件
- 通知信息
- `ListView`/`RecyclerView`
- 可放置`Bitmap`的地方

使用快照功能时不需要显示Mapbox地图。`MapSnapshotter`可以在应用程序的任何地方调用。

除非您已经缓存了地图瓦片，否则设备 _将_ 需要网络连接来下载渲染地图和快照所需的样式的地图瓦片。

快照的获取可以在设备的后台线程中进行，不会影响用户体验。

快照功能不同于[Mapbox静态图像API](/android/java/overview/static-image)。[类`MapboxStaticMap`](https://github.com/mapbox/mapbox-java/blob/afe9e88c9a09a413405571678d17499aa0a5f25c/services-staticmap/src/main/java/com/mapbox/api/staticmap/v1/MapboxStaticMap.java)帮助您构建一个URL来请求静态地图图像，请求获得的图像会看起来像一个没有交互性或控制的嵌入式地图。

## 获取地图快照

`MapSnapshotter`构造函数需要一个`MapSnapshotter.Options`对象。

{{
<AndroidActivityToggle
  id="take-a-map-snapshot"

java={`
MapSnapshotter.Options snapShotOptions = new MapSnapshotter.Options(500, 500);

snapShotOptions.withRegion(mapboxMap.getProjection().getVisibleRegion().latLngBounds);

snapShotOptions.withStyle(mapboxMap.getStyle().getUrl());

MapSnapshotter mapSnapshotter = new MapSnapshotter(this, snapShotOptions);
`}

kotlin={`

val snapShotOptions = MapSnapshotter.Options(500, 500)

snapShotOptions.withRegion(mapboxMap.projection.visibleRegion.latLngBounds)

snapShotOptions.withStyle(mapboxMap.style!!.url)

val mapSnapshotter = MapSnapshotter(this, options)
`}

/>
}}

以下是类`MapSnapshotter.Options`可用的一些属性。它们的调用方法和上面的代码片段中的`withRegion()`和`withStyle()`一样。

| 函数方法 | 描述 |
| --- | --- |
| `withApiBaseUrl` | 指定用于Maps API终端的具体URL。您基本不需要修改API的基本URL。
| `withLocalIdeographFontFamily` | 设置用于在本地生成符号的字体集，包括中日韩越统一表意文字和朝鲜文音节等。
| `withCameraPosition` | 用于快照图像的相机位置。如果调用了`withRegion`函数，该位置的值将会被覆盖。
| `withLogo ` |一个用于决定快照图像中是否包含Mapbox logo的布尔标识。
| `withPixelRatio ` | 使用的像素比例，默认值为1。
| `withRegion ` | 用于显示快照图像的区域，使用于相机位置之后。
| `withStyle ` | 用于快照图像的地图样式。
| `withStyleJson ` | 使用的地图样式JSON，而不是地图样式URL。

一旦您创建了您的`MapSnapshotter`对象，就会调用`start()`启动快照进程。当`MapSnapshot`准备就绪后，调用`snapshot.getBitmap()`来检索所需的`Bitmap`图像。

{{
<AndroidActivityToggle
  id="get-bipmap"

java={`
mapSnapshotter.start(new MapSnapshotter.SnapshotReadyCallback() {
	@Override
	public void onSnapshotReady(MapSnapshot snapshot) {

	// Display, share, or use bitmap image how you'd like
	
	Bitmap bitmapImage = snapshot.getBitmap();



	}
});
`}

kotlin={`

mapSnapshotter?.start { snapshot ->

	// Display, share, or use bitmap image how you'd like
	
	val bitmapOfMapSnapshotImage = snapshot.bitmap


}
`}

/>
}}


一旦您获取了`Bitmap`图像，您就可以随心所欲地使用该图像。

Mapbox Android的demo应用程序有两个展示了如何使用快照`Bitmap`图像的例子：

{{
  <RelatedPage
    url="/android/maps/examples/share-a-snapshot/"
    title="分享快照图像"
    contentType="example">
}}
分享实时快照图片。
{{</RelatedPage>}}

{{
  <RelatedPage
    url="/android/maps/examples/display-a-snapshot-notification/"
    title="显示快照通知"
    contentType="example">
}}
在通知中使用地图快照图片。
{{</RelatedPage>}}
