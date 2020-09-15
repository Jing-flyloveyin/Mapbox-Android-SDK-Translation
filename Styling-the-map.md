---
标题: "设置地图样式"
描述: "了解如何使用Android版Mapbox Maps SDK来自定义地图样式或选择Mapbox制图团队制作的预设样式。"
前置:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
内容类型: 指南
语言:
- Java
- Kotlin
---

Android版Mapbox Maps SDK允许完全自定义地图外观。您可以选择一个由我们制图团队设计的Mapbox核心样式，也可以通过调整地图的颜色、图标和字体来设置适合您的应用程序界面或公司品牌的地图样式。

自定义地图外观的方式有两种：使用Mapbox Studio创建自定义地图样式并将其加载到您的应用程序中，或在运行时使用Android版Maps SDK更新地图要素。

通过实时运行样式，您可以动态更改地图样式和观感。例如：基于一天中的不同时段调整地图明暗、根据用户活动更改个性化图标和地图颜色、动态切换语言、或根据用户喜好放大标签以提高可读性。

## Style对象

一个`Style`对象是指在您的应用程序中使用的[Mapbox地图样式](https://www.mapbox.com/help/define-style/)。数据源、图层和图像通过地图样式显示在地图上，因此它们是通过Style对象添加和检索的，而不是将它们添加到实际的MapboxMap对象中。

首先必须创建一个`Style`对象并将其分配给地图，地图才能正确显示。使用以下方法创建一个`Style`对象：

- 在`Style`类常量中找到的默认Mapbox样式
- 在Mapbox帐户自定义地图样式的URL
- 通过`asset://`或`path://`使用原始样式的JSON字符串或引用样式JSON

如果无法加载样式或设置了无效的样式网址，则地图视图将变为空白。系统将在Android logcat中记录一条错误消息，同时`MapView.OnDidFailLoadingMapListener`回调将被触发。

## 更改地图样式

如果您需要加载全新的地图样式，则必须以编程方式更改地图样式URL。调用`mapboxMap.setStyle()`并把样式URL作为参数。

{{
<AndroidActivityToggle
  id="setting-custom-style-url-java"

java={`
mapboxMap.setStyle(new Style.Builder().fromUri(uniqueStyleUrl), new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

	// Custom map style has been loaded and map is now ready


	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.Builder().fromUri(uniqueStyleUrl)) {

	// Custom map style has been loaded and map is now ready

}
`}

/>
}}

## 默认Mapbox样式

除了强大的自定义地图样式功能外，Mapbox还提供了六种专业设计的地图样式：

1. **Mapbox 街道（Mapbox Streets）：** Mapbox街道地图是综合性通用地图，突出清晰的道路和公交网络。
2. **户外（Outdoor）：** Mapbox户外地图针对远足，自行车和极限冒险活动，定制了具有精选数据集和专门样式的通用地图。
3. **明暗（Light and Dark）：** Mapbox明、暗地图是巧妙的全要素地图，旨在提供地理环境，同时突出显示分析仪表板，数据可视化或数据覆盖中的信息。
4. **卫星（Satellite）:** 我们的全球卫星底图，可用作空白画布或您数据的覆盖图。
5. **卫星街道（Satellite Streets）:** 卫星地图结合Mapbox街道地图的矢量数据，包括全面的道路、标签和POI信息（地标、景点等）数据集，明确高分卫星图像的细节及其背景。
6. **交通（Traffic）:** 交通地图使用Mapbox提供的白天或夜晚交通样式来直观地显示交通流量。

Maps SDK的`Style`类具有`private static final String`默认的Mapbox样式URL。当您需要通过`setStyle()`方法或其他方式传递样式URL时，可以在项目中方便地引用这些样式。例如：

{{
<AndroidActivityToggle
  id="setting-default-mapbox-style-url-java"

java={`
mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
	@Override
	public void onStyleLoaded(@NonNull Style style) {

	// Custom map style has been loaded and map is now ready


	}
});
`}

kotlin={`
mapboxMap.setStyle(Style.MAPBOX_STREETS) {

	// Custom map style has been loaded and map is now ready

}
`}

/>
}}


## 检索地图图层

检索地图图层是实时调整地图视觉外观的第一步。每个地图样式都包含大量地图图层，每个图层都有一个ID。在专业设计的 `Mapbox 街道（Mapbox Streets）`样式中，图层有这样一些ID：`土地利用（landuse）`, `居名地（settlement-label）`, `自然点（natural-point-label）`, 和 `建筑（building）`。

{{
<AndroidActivityToggle
  id="getting-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {
    
    Layer singleLayer = style.getLayer(UNIQUE_LAYER_ID);

  }
});
`}

kotlin={`
mapboxMap.getStyle {
	val singleLayer = it.getLayer(UNIQUE_LAYER_ID) 
}
`}

/>
}}

您可以在Mapbox Studio中查看地图样式的图层Z-index顺序和图层ID。查看该信息的另一种方法是将地图加载到设备后，将每个图层ID打印到Android logcat。

{{
<AndroidActivityToggle
  id="printing-layer-ids"

java={`
mapView.getMapAsync(new OnMapReadyCallback() {
@Override
	public void onMapReady(@NonNull final MapboxMap mapboxMap) {

		mapboxMap.setStyle(Style.MAPBOX_STREETS, new Style.OnStyleLoaded() {
			@Override
			public void onStyleLoaded(@NonNull Style style) {
	
				for (Layer singleLayer : style.getLayers()) {
					Log.d(TAG, "onMapReady: layer id = " + singleLayer.getId());
				}
	
			}
		});
	}
});
`}

kotlin={`
mapView?.getMapAsync {
	mapView?.getMapAsync { mapboxMap -> mapboxMap.setStyle(Style.MAPBOX_STREETS) {

			for (singleLayer in it.layers) {
				Log.d(TAG, "onMapReady: layer id = " + singleLayer.id)
			}
	
		}
	}
}
`}

/>
}}

有了单独的地图图层后，您就可以使用[数据驱动样式（data-driven styling ）](/android/maps/overview/data-driven-styling/) 和 [表达式（expressions）](/android/maps/overview/expressions/) 来调整图层的属性。例如，以下是用实时运行样式将`水系（water）`图层的蓝色更改为更深的蓝色的实例：

{{
<AndroidActivityToggle
  id="changing-water-layer"

java={`
button.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View view) {
    mapboxMap.getStyle(new Style.OnStyleLoaded() {
      @Override
      public void onStyleLoaded(@NonNull Style style) {

        Layer waterLayer = style.getLayer("water");
        
        if (waterLayer != null) {
          waterLayer.setProperties(PropertyFactory.fillColor(Color.parseColor("#004f6b")));
        }
    
      }
    });
  }
});
`}

kotlin={`
button.setOnClickListener {
	mapboxMap.getStyle {
	    
	    val waterLayer = it.getLayer("water")
	
	    waterLayer?.setProperties(PropertyFactory.fillColor(Color.parseColor("#004f6b")))
	    
	}
}
`}
/>
}}

## 与地图样式有关的事件

更改地图样式会触发“地图事件”。阅读[地图更改事件文档](https://docs.mapbox.com/android/maps/overview/events/#map-change-events) 以获取更多有关地图事件的信息、以及如何在地图样式加载完成后显示或重新显示数据的方法。
