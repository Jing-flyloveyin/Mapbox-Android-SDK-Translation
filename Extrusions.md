---
标题: "拉伸"
描述: "了解如何使用Android版Mapbox Maps SDK在地图上添加和自定义3D形状"
前置:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import Note from '@mapbox/dr-ui/note';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
内容类型: 指南
语言:
- Java
- Kotlin
---

Mapbox样式规范n使用*拉伸（extrusion）*来指代地图上显示的3D形状。地图上常见的拉伸多以建筑物的形式呈现，但任何类型的`多边形（Polygon）`形状都可以被拉伸。建筑物的拉伸形状遵循建筑物底部形状，就像二维`多边形`从地表垂直拉伸出指定的高度（以米为单位）。

与Mapbox地图的其他视觉功能一样，用户可以在运行时根据源数据中数据字段的值来设置拉伸样式。

添加和设置拉伸样式可以通过Maps SDK for Android的`FillExtrusionLayer`来完成。与其他Maps SDK图层类似，`FillExtrusionLayer`用一个唯一ID、和一个唯一或共享的源ID进行初始化。然后在将图层添加到地图的`Style`对象之前，定义`FillExtrusionLayer`图层的属性。


##样式

Android Maps SDK拉伸样式选项遵循[官方Mapbox样式规范](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#fill-extrusion)。

请阅读样式规范属性，以了解有关拉伸颜色、不透明度、图案、坐标偏移等的更多信息。
 
[Mapbox Android演示应用程序的与拉伸有关的示例](/android/maps/examples/#extrusions)展示了对拉伸进行样式设置的可行性。

{{
<AndroidActivityToggle
  id="basic-extrusion-setup"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		FillExtrusionLayer fillExtrusionLayer = new FillExtrusionLayer("extrusion-layer-id", "source-id");
	
		fillExtrusionLayer.setProperties(
		  fillExtrusionHeight(HEIGHT_NUMBER)
		);
	
		style.addLayer(fillExtrusionLayer);
	}
});
`}

kotlin={`
mapboxMap.getStyle {

	val fillExtrusionLayer = FillExtrusionLayer("extrusion-layer-id", "source-id")
	
	fillExtrusionLayer.setProperties(
		fillExtrusionHeight(HEIGHT_NUMBER)
	)
	
	it.addLayer(fillExtrusionLayer)
}
`}
/>
}}

##光线

Mapbox样式规范的一部分是_光线（light）_的概念。您可以调整光线照射到地图上的颜色、强度和角度，就像调整太阳的位置一样。

样式中的光线属性为整个样式提供_整体（global）_光源。由于该属性应用于所有图层，所以`光线`是样式规范中的[根属性（root property）](https://docs.mapbox.com/mapbox-gl-js/style-spec/root/#light),而不仅作用于单个[填充-拉伸图层（fill-extrusion layer）](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#fill-extrusion)中定义的绘画或布局属性。


{{<小贴士>}}

您可以考虑使用Mapbox Studio来迭代地图样式的光线选项。与在 Android 项目中迭代相比，在Mapbox Studio中操作可以更快速地让您体验更改各光照属性样式进带来的视觉影响。当您在Mapbox Studio中找到创建目标拉伸造型的属性值以后，可以在Android项目的代码中使用相同的最终属性值。

[阅读更多关于球面坐标系的内容](https://en.wikipedia.org/wiki/Spherical_coordinate_system)可以帮助您更好地理解光的径向、方位和极点位置。

{{</小贴士>}}

{{
  <RelatedPage
    url="/android/maps/examples/adjust-light-location-and-color/"
    title="光线调整"
    contentType="example">
}}
调整光线的位置和颜色，体验它对拉伸的影响。
{{</RelatedPage>}}

##技巧

通过Maps SDK的[表达式（expressions）](/android/maps/overview/expressions/)和[数据驱动的样式（data-driven styling）](/android/maps/overview/data-driven-styling/)可以增强拉伸样式。它们的组合可以带来有趣的效果，并实现特定的数据可视化以匹配您的用例。

###相对高度

当使用拉伸作为数据可视化策略时，如果使用与空间中物体的物理高度无关的数据（例如，对人口普查区域中地人口数据进行可视化），则数据字段的数值可能不适合作为拉升高度（以米为单位）。为了实现拉伸出对象在不同缩放级别上的可见性，您可以使用Maps SDK表达式使所有的拉伸对象在地图上显得更高，*并且*保持它们的相对高度差相同。这样，当地图camera离地图表面较远时，在较低的缩放级别上依然可以看到拉伸对象。


`fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))));` is the key to this effect.
实现上述效果的关键部分包括：`fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))));` 

{{
<AndroidActivityToggle
  id="extrusion-height"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		FillExtrusionLayer fillExtrusionLayer = new FillExtrusionLayer("extrusion-layer-id", "source-id");
	
		fillExtrusionLayer.setProperties(
		  fillExtrusionColor(COLOR.RED),
		  fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
		);
	
		style.addLayer(fillExtrusionLayer);
	}
});
`}

kotlin={`
mapboxMap.getStyle {
	val fillExtrusionLayer = FillExtrusionLayer("extrusion-layer-id", "source-id")

	fillExtrusionLayer.setProperties(
	        fillExtrusionColor(Color.RED),
	        fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
	)
	
	it.addLayer(fillExtrusionLayer)
}
`}

/>
}}

{{
  <RelatedPage
    url="/android/maps/examples/display-3d-building-height-based-on-vector-data/"
    title="矢量数据高度"
    contentType="example">
}}
根据数据集设置拉伸高度。
{{</RelatedPage>}}

###基础与高度

调整`fillExtrusionBase`和`fillExtrusionHeight`之间的差异也会产生有趣的视觉效果。如果差异值很小，例如3米，则视觉效果将是看起来像漂浮在空中的、一小块拉伸物体。这可以是一种不错的基本方式，可以用来可视化飞行数据、或其他没有连接到地图平面的数据。

{{
<AndroidActivityToggle
  id="base-vs-height"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

		FillExtrusionLayer fillExtrusionLayer = new FillExtrusionLayer("extrusion-layer-id", "source-id");
	
		fillExtrusionLayer.setProperties(
			fillExtrusionColor(Color.RED),
			fillExtrusionBase(get("HEIGHT_FEATURE_PROPERTY_KEY")),
	      fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
		);
	
		style.addLayer(fillExtrusionLayer);
	}
});

`}

kotlin={`
mapboxMap.getStyle {

	val fillExtrusionLayer = FillExtrusionLayer("extrusion-layer-id", "source-id")
	
	fillExtrusionLayer.setProperties(
	    fillExtrusionColor(Color.RED),
	    fillExtrusionBase(get("HEIGHT_FEATURE_PROPERTY_KEY")),
	    fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))
	)
	
	it.addLayer(fillExtrusionLayer)
}
`}

/>
}}
