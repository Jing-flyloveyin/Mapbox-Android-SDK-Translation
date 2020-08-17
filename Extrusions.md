---
标题: "挤塑"
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

Mapbox样式规范使用术语*挤塑*来表示地图上显示的三维形状。挤塑模型经常以建筑的形式出现在地图上，但是任何类型的`多边形`形状都可以挤塑。建筑的挤塑形状遵循建筑物底部形状，就像二维`多边形`从地表垂直拉伸出指定的高度（以米为单位）。

与Mapbox地图的其他视觉方面一样，可以在运行时根据源数据中数据字段的值来设置拉伸样式。

可以通过Android版的`FillExtrusionLayer`Maps SDK添加和设置拉伸样式。与其他Maps SDK图层类似，`FillExtrusionLayer`使用唯一的ID和唯一或共享的源ID进行初始化。然后定义`FillExtrusionLayer`图层的属性，再将该图层添加到地图的`Style`对象。

##样式

Android版Maps SDK拉伸样式选项遵循[官方Mapbox样式规范](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#fill-extrusion)。

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

##光

Mapbox样式规范的一部分是_光_的概念。您可以调整光线照射到地图上的颜色、强度和角度，就像调整太阳的位置一样。

样式的light属性为整个样式提供_球形_光源。因为这些属性应用于所有图层，所以`光`是样式规范中的[根属性](https://docs.mapbox.com/mapbox-gl-js/style-spec/root/#light),而不是在单个[填充-挤塑层](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#fill-extrusion)中定义的绘画或布局属性。


{{<Note>}}
Consider using Mapbox Studio to iterate on a map style's light options. Working in Mapbox Studio allows you to more quickly explore the visual impact of changes to your style's light properties compared to iterating in your Android project. Once you find the values in Mapbox Studio that create the extrusion styling that you want, then use the same final values in your Android project's code.

[Read more about the spherical coordinate system](https://en.wikipedia.org/wiki/Spherical_coordinate_system) to understand light's radial, azimuthal, and polar positioning.
{{</Note>}}

{{
  <RelatedPage
    url="/android/maps/examples/adjust-light-location-and-color/"
    title="Light adjustment"
    contentType="example">
}}
Adjust the light's location and color to see how it affects the extrusion.
{{</RelatedPage>}}

##技巧

通过Maps SDK的[表达式](/android/maps/overview/expressions/)和[数据驱动的样式](/android/maps/overview/data-driven-styling/)可以增强挤塑样式。它们的组合可以带来有趣的效果，并实现特定的数据可视化以匹配您的用例。

###相对高度

如果将挤塑模型作为与空间对象物理高度无关的数据实行数据可视化策略（例如，可视化人口普查块组中的种群），则数据字段的值可能不适合作为拉伸高度（以米为单位）。要使突出显示在各种缩放级别下可见，您可以使用Maps SDK表达式使所有突出显示在地图上的高度更高，*并且*保持它们的相对高度差相同。 这样，当地图相机距离地图表面较远时，可以在低得多的缩放级别上看到突出部分。

`fillExtrusionHeight(sum(literal(CONSTANT_NUMBER), get("HEIGHT_FEATURE_PROPERTY_KEY")))));` is the key to this effect.

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
    title="Vector data height"
    contentType="example">
}}
Set extrusion height based on a dataset.
{{</RelatedPage>}}

###基础与高度

调整`fillExtrusionBase`和`fillExtrusionHeight`之间的差异也会产生有趣的视觉效果。如果差异很小，例如3米，则视觉效果将是看起来像漂浮在空中的一小部分挤塑模型。这是可视化飞行数据或未连接到地图平坦表面的其他数据的一种不错的基本方法。

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
