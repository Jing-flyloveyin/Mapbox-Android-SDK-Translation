---
标题: "用户界面（UI）设置"
描述: "了解如何使用Android版Mapbox Maps SDK调整屏幕上的用户界面（UI），例如指南针，Mapbox徽标和属性。"
前置:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
内容类型: 指南
语言:
- Java
- Kotlin
---


Android版Mapbox Maps SDK 能调整地图用户界面（UI）的某些元素。开发人员只要正确设置`MapboxMap`，就能获取它的`UiSettings`对象。

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

拥有`UiSettings`对象后，就可以更改手势识别，指南针，Mapbox徽标和属性。更改通过`UiSettings`立即反映在地图上。

您还可以使用[Maps SDK 的XML属性](/android/maps/overview/#mapview-xml-attributes) 进行用户界面（UI）设置，而不是用`UiSettings`类的方法。

所有UI设置还具有“获取（get）”方法，这些方法使您可以查看当前设置（例如，如果启用了指南针，徽标的边距是多少，或者自定义指南针图像是什么）。

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

## 手势

Android版Mapbox Maps SDK使用[Android版Mapbox手势库](/android/maps/overview/gestures/)来检测手势。

调整或完全禁用缩放，倾斜，滚动，旋转，快速缩放和双击手势识别。禁用地图手势不会影响您通过编程方式更改相机位置。

例如，以下是通过双击地图来禁用增加缩放级别的方法：

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

有关手势如何影响相机位置的更多信息，请参见[地图相机位置文档](/android/maps/overview/camera/#camera-position) 。

## 指南针

Maps SDK包含一个指南针图像，当地图沿任何方向旋转时，该指南针图像都会显示在地图上。罗盘并不代表物理设备与磁北极的关系，指南针图像根据屏幕上的地图旋转而旋转。

当用户单击指南针图像时，相机会以动画方式返回默认的北向（方位为0），不久后指南针图像就会消失。

默认情况下，指南针显示在的右上角`MapView`，但是可以调整其边距。您可以将自定义可绘制图像传递到Maps SDK，以替换默认图像。

或选择完全禁用指南针：

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


## 徽标和属性

请阅读 [Mapbox的属性条例](/android/maps/overview/#attribution) ，以获取有关显示Mapbox字标和属性的信息。

徽标的属性（例如边距和重力）可以使用XML或`UiSettings`对象进行调整。图标的属性值`i`（例如色调，边距和重力）也可以使用XML或`UiSettings`对象进行调整。

{{
  <RelatedPage
    url="/android/maps/examples/change-attribution-color/"
    title="Attribution icon color"
    contentType="example">
}}
了解如调整图标的属性值`i`，如颜色，以匹配或凸显于地图样式中。
{{</RelatedPage>}}
