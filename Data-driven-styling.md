---
title: "数据驱动的样式"
description: "使用 Android版 Mapbox Maps SDK实时更改 Mapbox 地图外观的文档。"
prependJs:
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import Note from '@mapbox/dr-ui/note';"
contentType: guide
language:
- Java
- Kotlin
---

使用 Maps SDK 的数据驱动样式功能来创建和显示多种类型的数据。您可以根据特定数据集中的信息实时动态地更改地图的外观。数据驱动的样式很大程度上基于源和层的概念。

{{
  <RelatedPage
    url="https://docs.mapbox.com/mapbox-gl-js/style-spec/"
    title="Mapbox Style Specification"
    contentType="guide">
}}
View a full list of the features in runtime styling not specific to Android.
{{</RelatedPage>}}

{{<Note theme="warning" title="Annotation Plugin for Android">}}
Sources and layers provide nimble options for customizing the look of a Mapbox map and the data displayed on the map. The [Mapbox Annotation Plugin for Android](/android/plugins/overview/annotation/) provides a simplified system for interacting with and customizing Mapbox map layers.
{{</Note>}}


## 源

源保存实际数据，图层参考源。 这就是在Mapbox地图上显示数据的方法。 支持多种不同的源类型，选择正确的源类型取决于您的数据类型。 添加源并不会立即使数据显示在地图上，因为源不包含颜色或宽度等样式细节。 层是指源，并为其提供可视化表示。

使用源需要两个参数。源需要唯一的`String`ID，并且需要某种数据。  

### 矢量

VectorSource切片必须为Mapbox矢量切片格式。使用矢量源的所有图层都必须指定“源图层”值。 对于由 Mapbox 托管的矢量瓦片，URL值的格式应为mapbox：// mapid。


{{
<AndroidActivityToggle
  id="vector-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

   // Adding a vector source layer

   VectorSource vectorSource = new VectorSource("vector-source", "YOUR_TILESET_ID");

   style.addSource(vectorSource);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

	// Adding a vector source layer
	val vectorSource = VectorSource("vector-source", "tileset-id")
	
	it.addSource(vectorSource)
}
`}

/>
}}

使用VectorSource在运行时将新图层添加到地图样式时，必须使用setSourceLayer（）方法指定源图层（即使tileet仅包含一个源图层）。 要查看图块集中的可用源图层，请访问您的Mapbox帐户的图块集页面，然后单击图块集以打开图块信息页面，其中列出了所有源图层。 确定要将哪个源图层的数据归因于地图图层。

所有其他来源类型（包括GeoJSON来源）均禁止使用setSourceLayer（）方法。


{{
<AndroidActivityToggle
  id="vector-source-set-source-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	VectorSource vectorSource = new VectorSource("vector-source", "tileset-id");
	
	style.addSource(vectorSource);
	   
	CircleLayer circleLayer = new CircleLayer("circle-layer-id", "vector-source");
	
	circleLayer.setSourceLayer(NAME_OF_LAYER_INSIDE_TILESET);
	
	style.addLayer(circleLayer);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

	val vectorSource = VectorSource("vector-source", "tileset-id")
	
	it.addSource(vectorSource)
	
	val circleLayer = CircleLayer("circle-layer-id", "vector-source")
	
	circleLayer.setSourceLayer(NAME_OF_LAYER_INSIDE_TILESET)
	
	style.addLayer(circleLayer)
}
`}

/>
}}


### 栅格

如果栅格源tile为TileJSON格式，则可以将其添加到地图中。 如果由Mapbox托管，则URL值的格式应为mapbox：// mapid。

{{
<AndroidActivityToggle
  id="raster-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	// Adding a raster source layer
	
	RasterSource rasterSource = new RasterSource("raster-source", "mapbox://mapbox.u8yyzaor");
	
	style.addSource(rasterSource);   
  }
});
`}

kotlin={`
mapboxMap.getStyle {

	// Adding a raster source layer
	val rasterSource = RasterSource("raster-source", "mapbox://mapbox.u8yyzaor")
	
	it.addSource(rasterSource)

}
`}

/>
}}

### GeoJson

添加GeoJsonSource可以通过几种不同的方式完成。 您可以提供指向在线托管的GeoJSON原始数据的URL，可以提供指向应用程序的Assets文件夹中本地托管的GeoJSON文件的链接，也可以直接在代码内部构建自己的GeoJSON FeatureCollection。 下面代码段展示了将GeoJSON数据添加到地图的不同方法。

{{
  <RelatedPage
    url="/android/maps/examples/create-a-line-layer/"
    title="Add a GeoJSON source"
    contentType="example">
}}
Create a GeoJSON feature collection dynamically and then add it as a map source.
{{</RelatedPage>}}

从一个URL添加GeoJSON:

{{
<AndroidActivityToggle
  id="geojson-source"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	try {
	  URI geoJsonUrl = new URI("https://url-to-geojson-file.geojson");
	  GeoJsonSource geoJsonSource = new GeoJsonSource("geojson-source", geoJsonUrl);
	  style.addSource(geoJsonSource);
	} catch (URISyntaxException exception) {
	  Log.d(TAG, exception);
	}
  }
});
`}

kotlin={`
mapboxMap.getStyle {

	try {
	    val geoJsonUrl = URI("https://url-to-geojson-file.geojson")
	    val geoJsonSource = GeoJsonSource("geojson-source", geoJsonUrl)
	    it.addSource(geoJsonSource)
	} catch (exception: URISyntaxException) {
	    Log.d(TAG, exception)
	}
}
`}

/>
}}

您还可以使用URI加载本地存储的GeoJSON文件

{{
<AndroidActivityToggle
  id="load-local-geojson"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	try {
	
		GeoJsonSource source = new GeoJsonSource("geojson-source", new URI("asset://local_file_name.geojson"));
	
		style.addSource(source);
	
	} catch (URISyntaxException exception) {
	
		Log.d(TAG, exception);
	
	}
  }
});
`}

kotlin={`
mapboxMap.getStyle {

	try {
	
	    val source = GeoJsonSource("geojson-source", URI("asset://local_file_name.geojson"))
	
	    it.addSource(source)
	
	} catch (exception: URISyntaxException) {
	
	    Log.d(TAG, exception)
	}
}
`}

/>
}}


创建一个GeoJSON FeatureCollection并将其添加到您的地图中：
{{
<AndroidActivityToggle
  id="create-feature-collection"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	// Create a list to store our line coordinates.
	List routeCoordinates = new ArrayList<Point>();
	routeCoordinates.add(Point.fromLngLat(-118.394391, 33.397676));
	routeCoordinates.add(Point.fromLngLat(-118.370917, 33.391142));
	
	// Create the LineString from the list of coordinates and then make a GeoJSON FeatureCollection so that you can add the line to our map as a layer.
	
	LineString lineString = LineString.fromLngLats(routeCoordinates);
	
	FeatureCollection featureCollection = FeatureCollection.fromFeature(Feature.fromGeometry(lineString));
	
	GeoJsonSource geoJsonSource = new GeoJsonSource("geojson-source", featureCollection);
	style.addSource(geoJsonSource);
  }
});
`}

kotlin={`
mapboxMap.getStyle {

	// Create a list to store our line coordinates.
	val routeCoordinates = ArrayList<Point>()
	routeCoordinates.add(Point.fromLngLat(-118.394391, 33.397676))
	routeCoordinates.add(Point.fromLngLat(-118.370917, 33.391142))
	
	// Create the LineString from the list of coordinates and then make a GeoJSON FeatureCollection so that you can add the line to our map as a layer.
	
	val lineString = LineString.fromLngLats(routeCoordinates)
	
	val featureCollection = FeatureCollection.fromFeature(Feature.fromGeometry(lineString))
	
	val geoJsonSource = GeoJsonSource("geojson-source", featureCollection)
	it.addSource(geoJsonSource)
}
`}
/>
}}

{{
  <RelatedPage
    url="https://docs.mapbox.com/help/tutorials/android-runtime-styling-intro/"
    title="Runtime styling for Android"
    contentType="tutorial">
}}
Read an explanation and view the code for using runtime styling to change a map's water color.
{{</RelatedPage>}}

将数据包含在GeoJSON源中的好处是，您可以随时在源中更新，删除或添加其他功能，从而提供了一种通过运行时样式API在地图中对数据进行动画处理的解决方案。 例如，Android ValueAnimator可以通过在GeoJSON数据内更新其坐标来移动要素。

### 图像

ImageSource允许将地理栅格图像显示在地图顶部。 图像会随着用户缩放，倾斜和旋转地图而随之同步转变。 LatLngQuad提供的栅格图像内容的地理位置可以是非轴对齐的。

{{
<AndroidActivityToggle
  id="image-source"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

    // Set the latitude and longitude coordinates of the image's four corners
    LatLngQuad quad = new LatLngQuad(
      new LatLng(46.437, -80.425),
      new LatLng(46.437, -71.516),
      new LatLng(37.936, -71.516),
      new LatLng(37.936, -80.425));
    
    // Add the source to the map layer
    style.addSource(new ImageSource(ID_IMAGE_SOURCE_ID, quad, DRAWABLE_IMAGE));
    
    // Add layer
    RasterLayer layer = new RasterLayer(ID_IMAGE_LAYER, IMAGE_SOURCE_ID);
    style.addLayer(layer);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

	// Set the latitude and longitude coordinates of the image's four corners
	val quad = LatLngQuad(
	        LatLng(46.437, -80.425),
	        LatLng(46.437, -71.516),
	        LatLng(37.936, -71.516),
	        LatLng(37.936, -80.425))
	
	it.addSource(ImageSource(ID_IMAGE_SOURCE, quad, DRAWABLE_IMAGE_HERE))
	
	// Add layer
	val layer = RasterLayer(ID_IMAGE_LAYER, ID_IMAGE_SOURCE)
	it.addLayer(layer)
}
`}

/>
}}

setImage（）方法通过传入可绘制对象来更新ImageSource图像。


{{
<AndroidActivityToggle
  id="set-image"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	ImageSource imageSource =  style.getSourceAs(ID_IMAGE_SOURCE);
	if (imageSource != null) {
		imageSource.setImage(DESIRED_IMAGE);
	}
  }
});
`}

kotlin={`
mapboxMap.getStyle {

	val imageSource = style.getSourceAs<ImageSource>(ID_IMAGE_SOURCE)
	
	imageSource?.setImage(DESIRED_IMAGE)}
`}

/>
}}

### 自定义几何

当您拥有动态生成的数据或需要按需加载的数据时，CustomGeometrySource会很有用。 可以在CustomGeometrySource中使用任何类型和数量的GeoJSON几何形状的FeatureCollection。

{{
<AndroidActivityToggle
  id="custom-geometry"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

    CustomGeometrySource customSource = new CustomGeometrySource(CUSTOM_SOURCE_ID,
    GeometryTileProvider);
    if (customSource != null){
    	style.addSource(customSource);
    }
  }
});
`}

kotlin={`
mapboxMap.getStyle {

	 val customSource = CustomGeometrySource(CUSTOM_SOURCE_ID, GeometryTileProvider())
	
	 it.addSource(customSource)

}
`}

/>
}}

CustomGeometrySource用法的一个示例是在地图顶部创建黑色网格。示例可以通过这个链接查看 [the `GridSourceActivity` of the Maps SDK for Android test application](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDKTestApp/src/main/java/com/mapbox/mapboxsdk/testapp/activity/style/GridSourceActivity.java).


### DEM栅格

RasterDemSource支持Mapbox Terrain RGB（mapbox：//mapbox.terrain-rgb）和Mapzen Terrarium切片格式。 该来源应与Maps SDK HillshadeLayer一起使用。

Mapbox地形图块集用于将山地地形添加到任意Mapbox地图中。 运行时样式还可以用于更改山体阴影外观。

{{
<AndroidActivityToggle
  id="raster-dem"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	RasterDemSource rasterDemSource = new RasterDemSource("source-id", "mapbox://mapbox.terrain-rgb");
	style.addSource(rasterDemSource);
	
	// Create hillshade layer source to map
	HillshadeLayer hillshadeLayer = new HillshadeLayer("hillshade-layer-id", "source-id").withProperties(
	  hillshadeHighlightColor(Color.parseColor(HILLSHADE_HIGHLIGHT_COLOR)),
	  hillshadeShadowColor(Color.BLACK)
	);
	
	// Add hillshade layer to map
	style.addLayer(hillshadeLayer);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

	val rasterDemSource = RasterDemSource("source-id", "mapbox://mapbox.terrain-rgb")
	style.addSource(rasterDemSource)
	
	// Create hillshade layer source to map
	val hillshadeLayer = HillshadeLayer("hillshade-layer-id", "source-id").withProperties(
	        hillshadeHighlightColor(Color.parseColor(HILLSHADE_HIGHLIGHT_COLOR)),
	        hillshadeShadowColor(Color.BLACK)
	)
	
	// Add hillshade layer to map
	it.addLayer(hillshadeLayer)
}
`}

/>
}}

## 图层

在源保存数据的同时，图层用于样式化和显示信息。 根据您的源几何形状，提供了几种图层类型。 除了背景类型的图层外，每个图层都需要引用源。 您可以选择过滤功能，然后定义这些功能的样式。

每个图层都提供一个setProperties API，该API可用于以多种不同方式对图层进行样式设置。 请注意，建议您使用数据驱动的样式来减少地图需要渲染的层数，而不是根据源数据中的某些情况来创建不同的层。

<!-- TODO: talk about mapboxMap.getLayers(); -->
<!-- TODO: talk about mapboxMap.addLayerBelow(); -->

### 背景

背景层类型是唯一的，因为它不需要源。 背景层可以是纯色或图案

{{
<AndroidActivityToggle
  id="background-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

    BackgroundLayer backgroundLayer = new BackgroundLayer("background-layer");
    backgroundLayer.setProperties(PropertyFactory.backgroundColor(Color.BLUE));
    
    // Add background layer to map
    style.addLayer(backgroundLayer);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

	val backgroundLayer = BackgroundLayer("background-layer")
	backgroundLayer.setProperties(PropertyFactory.backgroundColor(Color.BLUE))
	
	// Add background layer to map
	it.addLayer(backgroundLayer)
}
`}

/>
}}


### 填充

填充图层具有封闭的几何形状，可用于在地图上标记区域。 将FillLayer与GeoJSON Polygon或MultiPolygon几何一起使用。 几何形状就像是由一系列坐标按特定顺序组成的线层，第一点和最后一点具有相同的坐标。 当坐标列表以相同坐标开始和结束时，几何将被“封闭”。 如果未封闭几何图形，则FillLayer将渲染，但某些顶点和边可能会被图块边界切除。

{{
<AndroidActivityToggle
  id="fill-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

    FillLayer fillLayer = new FillLayer("layer-id", "source-id");
    fillLayer.setProperties(PropertyFactory.fillColor(Color.GREEN));
    
    // Add fill layer to map
    style.addLayer(fillLayer);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

    val fillLayer = FillLayer("layer-id", "source-id")
    fillLayer.setProperties(PropertyFactory.fillColor(Color.GREEN))
    
    // Add fill layer to map
    it.addLayer(fillLayer)
}
`}

/>
}}

要在添加几何形状后更改其形状，可以保留该层而无需进行任何更改，仅应更新其使用的源。 图层将始终在其源中显示最新更新。




### 线段

{{
  <RelatedPage
    url="/android/maps/examples/create-a-line-layer/"
    title="Create and display line layer"
    contentType="guide">
}}
Build a GeoJSON FeatureCollection with the line geometry and then display it on the map using a line layer.
{{</RelatedPage>}}

可以组合一系列坐标来创建在地图上显示的线段。 在每对坐标之间，将创建一条直线段，将其直线绘制并连接两个点。

<!-- NOTE add something visual here building the line layer? -->

开始之前，您需要确保图层将使用的Source包含lineStrings作为其几何图形的一部分，您可以在GeoJSON source部分中找到此示例。 创建源并将其添加到地图后，就可以启动lineLayer并设置属性。

{{
<AndroidActivityToggle
  id="line-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	LineLayer lineLayer = new LineLayer("line-layer", "line-source");
	
	// The layer properties for our line. This is where you can make the line
	dotted, set the color, etc.
	lineLayer.setProperties(
	  PropertyFactory.lineDasharray(new Float[]{0.01f, 2f}),
	  PropertyFactory.lineCap(Property.LINE_CAP_ROUND),
	  PropertyFactory.lineJoin(Property.LINE_JOIN_ROUND),
	  PropertyFactory.lineWidth(5f),
	  PropertyFactory.lineColor(Color.parseColor("#e55e5e"))
	);
	
	style.addLayer(lineLayer);
  }
});
`}

kotlin={`
mapboxMap.getStyle {

	val lineLayer = LineLayer("line-layer", "line-source")
	
	// The layer properties for our line. This is where you can make the line dotted, set the color, etc.
	lineLayer.setProperties(
	        PropertyFactory.lineDasharray(arrayOf(0.01f, 2f)),
	        PropertyFactory.lineCap(Property.LINE_CAP_ROUND),
	        PropertyFactory.lineJoin(Property.LINE_JOIN_ROUND),
	        PropertyFactory.lineWidth(5f),
	        PropertyFactory.lineColor(Color.parseColor("#e55e5e"))
	)
	it.addLayer(lineLayer)
}
`}

/>
}}


### 符号

符号层使用图标或文本标签在地图上显示单个位置。 像GL标记和标记视图一样，符号层可以表示相同的数据，并为地图显示提供最大的功能。 首先，将标记图像添加到地图，然后将其显示为符号图层。

{{
<AndroidActivityToggle
  id="symbol-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

		Bitmap icon = BitmapUtils.getBitmapFromDrawable(getResources().getDrawable(R.drawable.red_marker));
style.addImage("icon-id", icon);

		SymbolLayer symbolLayer = new SymbolLayer("layer-id", "source-id");
		symbolLayer.setProperties(PropertyFactory.iconImage("icon-id");
	
		style.addLayer(symbolLayer);
	}
});
`}

kotlin={`
mapboxMap.getStyle {

	val icon = BitmapFactory.decodeResource(resources, R.drawable.my_marker_icon)
	it.addImage("icon-id", icon)
	
	val symbolLayer = SymbolLayer("layer-id", "source-id")
	symbolLayer.setProperties(PropertyFactory.iconImage("icon-id"))
	
	it.addLayer(symbolLayer)
}
`}

/>
}}

符号层不仅可以使用图像在地图上标记位置，而且还可以直接在地图上显示文本。 SDK会以与上述图像片段类似的方式处理文本符号图层，只是该图层的属性会更改。



<!-- NOTE build an example showing how to display text stored in source property -->

#### 图标锚点和偏移

默认情况下，SymbolLayer图标锚定在图标中心的坐标上。 虽然这适用于许多图标，但如果希望将图标的底部锚定到坐标，则可能不适用于形状像泪滴标记或图钉的图标。 在这些情况下，您可以通过指定 **anchor** 或设置 **offset**.来调整图标的位置。

{{<Note title="默认标记图标">}}
如果您使用的是 Android Mapbox Maps SDK附带的mapbox_marker_icon_default图像，则无需调整图标锚点。 图像的底部间距等于销钉高度的一半，因此该点将与坐标对齐。
{{</Note>}}

**锚点:** 建议使用PropertyFactory.iconAnchor来调整图标的位置。 例如，使用PropertyFactory.iconAnchor（Property.ICON_ANCHOR_BOTTOM）告诉Maps SDK将图像的底部附加到坐标上。

**偏移:** PropertyFactory.iconOffset是一种替代解决方案，允许您指定希望图标从锚点移动的距离。 例如，使用PropertyFactory.iconOffset（new Float [] {0f，VERTICAL_FLOAT_VALUE}）（其中VERTICAL_FLOAT_VALUE为一定距离），告诉Maps SDK将图标从坐标移动该距离（向上或向下）。 适当的VERTICAL_FLOAT_VALUE将取决于图标的高度。

{{
<AndroidActivityToggle
  id="icon-offset-symbol-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

    SymbolLayer symbolLayer = new SymbolLayer("SYMBOL_LAYER_ID", "SYMBOL_LAYER_SOURCE_ID");
    
    symbolLayer.setProperties(
        iconImage("SYMBOL_LAYER_ICON_ID"),
        iconAnchor(Property.ICON_ANCHOR_BOTTOM),
        // or iconOffset(new Float[] {0f, -9f})
    );
    
    style.addLayer(symbolLayer);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

	val symbolLayer = SymbolLayer("SYMBOL_LAYER_ID", "SYMBOL_LAYER_SOURCE_ID")
	
	symbolLayer.setProperties(
		iconImage("SYMBOL_LAYER_ICON_ID"),
		iconAnchor(Property.ICON_ANCHOR_BOTTOM),
		// or iconOffset(arrayOf(0f, -9f))
	)
	
	it.addLayer(symbolLayer)
}

))
`}
/>
}}


### 栅格

栅格图层通常是显示在基础地图图块顶部的图像的集合。 虽然首选矢量图块，但卫星图像或旧地图样式将渲染为栅格图层。

{{
<AndroidActivityToggle
  id="raster-source"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

		RasterSource rasterSource = new RasterSource("source-id", "mapbox://mapbox.u8yyzaor");
		style.addSource(rasterSource);
	
		RasterLayer rasterLayer = new RasterLayer("layer-id", "source-id");
		style.addLayer(rasterLayer);
	}
});
`}

kotlin={`
mapboxMap.getStyle {

	val rasterSource = RasterSource("source-id", "mapbox://mapbox.u8yyzaor")
	it.addSource(rasterSource)
	
	val rasterLayer = RasterLayer("layer-id", "source-id")
	it.addLayer(rasterLayer)
}
`}
/>
}}


RasterLayer的一个常见用例是在地图上添加一层卫星图块：

{{
<AndroidActivityToggle
  id="raster-source-satellite"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

		// Adding a raster source layer
		RasterSource satelliteRasterSource = new RasterSource("satellite-raster-source", "mapbox://mapbox.satellite",512);
	
		style.addSource(satelliteRasterSource);	}
});
`}

kotlin={`
mapboxMap.getStyle {

	// Adding a raster source layer
	val satelliteRasterSource = RasterSource("satellite-raster-source", "mapbox://mapbox.satellite", 512)
	
	it.addSource(satelliteRasterSource)
}
`}
/>
}}

### 圈

圆形图层有一个来自源数据的中心坐标。 这是在地图上绘制的地球表面上的圆形的地理精确投影。 提供了一些默认属性，但是在首次创建图层时可以将其覆盖。

{{ <AndroidActivityToggle id="circle-layer"

java={` 

mapboxMap.getStyle(new Style.OnStyleLoaded() { @Override public void onStyleLoaded(@NonNull Style style) {
	
		CircleLayer circleLayer = new CircleLayer("layer-id", "source-id");
	
		circleLayer.setProperties(
		  PropertyFactory.circleRadius(FLOAT_OR_EXPRESSION),
		  PropertyFactory.circleColor(COLOR_OR_EXPRESSION)
		);
	
		style.addLayer(circleLayer);
	}
}); 
`}

kotlin={` 

mapboxMap.getStyle {

val circleLayer = CircleLayer("layer-id", "source-id")

circleLayer.setProperties(
    PropertyFactory.circleRadius(FLOAT_OR_EXPRESSION),
    PropertyFactory.circleColor(COLOR_OR_EXPRESSION)
)

it.addLayer(circleLayer)
} `} /> }}

### 填充拉伸

填充拉伸图层将3D多边形“拉伸”添加到地图。 该图层通常用于显示3D建筑物，但是可以使用此图层类型来挤压任何类型的多边形。 在“Extrusions guide”中找到有关样式3D形状拉伸的更多信息。(/android/maps/overview/extrusions).

{{
<AndroidActivityToggle
  id="basic-extrusion-layer-setup"

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

### 山体阴影

HillshadeLayer将显示来自Mapbox Terrain RGB瓦片格式的高程数据。该层应与 [`RasterDemSource`](/android/maps/overview/data-driven-styling/#raster-dem)一起使用.

阅读有关访问海拔数据的更多信息，可以通过[Access elevation data](https://docs.mapbox.com/help/troubleshooting/access-elevation-data/) 了解.

[The hillshade section of the official Mapbox Style Specification](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#hillshade) 有关于如何设置山顶阴影的阴影颜色及光方向等信息。

{{
<AndroidActivityToggle
  id="hillshade-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	RasterDemSource rasterDemSource = new RasterDemSource("source-id", "mapbox://mapbox.terrain-rgb");
	style.addSource(rasterDemSource);
	
	HillshadeLayer hillshadeLayer = new HillshadeLayer("hillshade-layer-id", "source-id").withProperties(
	  hillshadeHighlightColor(Color.parseColor(HILLSHADE_HIGHLIGHT_COLOR)),
	  hillshadeShadowColor(Color.BLACK)
	);


	style.addLayer(hillshadeLayer);

  }
});
`}

kotlin={`
mapboxMap.getStyle {

	val rasterDemSource = RasterDemSource("source-id", "mapbox://mapbox.terrain-rgb")
	style.addSource(rasterDemSource)
	
	val hillshadeLayer = HillshadeLayer("hillshade-layer-id", "source-id").withProperties(
	        hillshadeHighlightColor(Color.parseColor(HILLSHADE_HIGHLIGHT_COLOR)),
	        hillshadeShadowColor(Color.BLACK)
	)
	
	it.addLayer(hillshadeLayer)
}
`}

/>
}}

{{<RelatedPage url="/android/maps/examples/add-a-hillshade-layer/" title="山体阴影" contentType="example">}}
添加并设置地形山体阴影的样式。.
{{</RelatedPage>}}

### 热图
Maps SD K的 HeatmapLayer 提供了热图可视化和样式选项，可用于生成热图。 使用此层的两个主要类别是：
- 密集点数据。 热图是一种可视化特定现象的密度的有用方法，当单独展示每个点在视觉展示效果并不好时。
- 在连续的曲面上插值离散值，在这些点之间创建平滑的渐变。 这种类型的热图不那么常见，并且无法通过像拟峰图那样在一组边界内聚集特征来可视化密度，而是显示点之间的连续渐变。 例如，您的城镇可能只有几个气象站，但是您最喜欢的气象应用程序显示了整个城镇区域内温度的平滑梯度。 对于您当地的气象服务，可以合理地假设，如果两个相邻的站点报告的温度不同，则它们之间的温度将逐渐从一个站点过渡到下一个站点。 [ 阅读Mapbox样式规范中的热图部分](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#heatmap) ，以了解有关热图的半径，重量，强度，颜色和不透明度选项的更多信息。

{{
<AndroidActivityToggle
  id="heatmap-layer"
java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

		HeatmapLayer heatmapLayer = new HeatmapLayer("layer-id", "source-id");
	
		heatmapLayer.setProperties(
		  PropertyFactory.heatmapColor(COLOR_OR_EXPRESSION),
		  PropertyFactory.heatmapWeight(FLOAT_OR_EXPRESSION)
		);
	
		style.addLayer(heatmapLayer);
	
	}
});
`}
kotlin={`
mapboxMap.getStyle {

	val heatmapLayer = HeatmapLayer("layer-id", "source-id")
	
	heatmapLayer.setProperties(
	    PropertyFactory.heatmapColor(COLOR_OR_EXPRESSION),
		 PropertyFactory.heatmapWeight(FLOAT_OR_EXPRESSION)
	)
	
	it.addLayer(heatmapLayer)
}
`}
/>
}}

{{
  <RelatedPage
    url="/android/maps/examples/display-a-heatmap"
    title="热图数据"
    contentType="example">
}}
使用HeatmapLayer可视化数据
{{</RelatedPage>}}

{{
  <RelatedPage
    url="/android/maps/examples/add-multiple-heatmap-styles"
    title="热图数据"
    contentType="example">
}}
使用表达式来精确控制HeatmapLayer的样式
{{</RelatedPage>}}


## 删除源和图层

如果任何层仍在使用源，则无法将其删除。 删除将失败并记录控制台警告。 从Maps SDK的7.0.0版本开始，SDK更改了remove方法，以返回一个布尔值，该布尔值指示删除是否成功。

必须先删除使用特定源的所有图层，然后才能删除该源。

删除层：

{{
<AndroidActivityToggle
  id="removing-a-layer"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

  		style.removeLayer("layer-id");

	}
});
`}

kotlin={`
mapboxMap.getStyle {

	it.removeLayer("layer-id")

}
`}
/>
}}


删除源：

{{
<AndroidActivityToggle
  id="removing-a-source"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

  		style.removeSource("source-id");

	}
});
`}

kotlin={`
mapboxMap.getStyle {

	it.removeSource("source-id")

}
`}
/>
}}

## 修改属性

源和图层不是一成不变的，因此可以在地图渲染期间随时对其进行修改。 例如，要在将图层添加到地图后更改其填充颜色，可以使用地图的Style对象获取图层并设置属性。

{{
<AndroidActivityToggle
  id="modify-properties"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

  		FillLayer fillLayer = style.getLayerAs("fill-layer-id");

  		if (fillLayer != null) {
  		  fillLayer.setProperties(PropertyFactory.fillColor(Color.GREEN));
		}

	}
});
`}

kotlin={`
mapboxMap.getStyle {

	val fillLayer = style.getLayerAs<FillLayer>("fill-layer-id")
	
	fillLayer?.setProperties(PropertyFactory.fillColor(Color.GREEN))
}
`}
/>
}}

在GeoJSON源中，您可以更改，添加，删除或替换FeatureCollection。 您可以传入要素列表，单个要素或几何：

{{
<AndroidActivityToggle
  id="change-geojson"

java={`
mapboxMap.getStyle(new Style.OnStyleLoaded() {
  @Override
  public void onStyleLoaded(@NonNull Style style) {

	GeoJsonSource geoJsonSource = style.getSourceAs("geojson-source-id");
	
	FeatureCollection newFeatureCollectionFromFeatureList = FeatureCollection.fromFeatures(FEATURE_LIST);
	
	Feature newFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0));
	
	LineString newLineString = LineString.fromLngLats(POINT_LIST);
	
	if (geoJsonSource != null) {
	geoJsonSource.setGeoJson(newFeatureCollectionFromFeatureList);
	
	// or geoJsonSource.setGeoJson(newFeature);
	
	// or geoJsonSource.setGeoJson(newLineString);
	
	}
});
`}

kotlin={`
mapboxMap.getStyle {

	val geoJsonSource = style.getSourceAs<GeoJsonSource>("geojson-source-id")
	
	val newFeatureCollectionFromFeatureList: FeatureCollection = FeatureCollection.fromFeatures(FEATURE_LIST)
	
	val newFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0))
	
	val newLineString: LineString = LineString.fromLngLats(POINT_LIST)
	
	geoJsonSource?.setGeoJson(newFeatureCollectionFromFeatureList)
}
`}
/>
}}

## 捕获点击事件

图层不可单击，并且不会公开任何事件侦听器来处理用户输入。 而是，地图查询工具可以帮助您检测用户何时与地图进行了交互。 例如，当点击FillLayer的多边形时。

另外，您可以使用[Mapbox注释插件](/android/plugins/overview/annotation/#manager)，该插件提供onClick（）和onLongClick（）侦听。
