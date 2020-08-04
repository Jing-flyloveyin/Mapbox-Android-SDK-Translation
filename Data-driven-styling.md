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

### Image

`ImageSource` allows for a georeferenced raster image to be shown on top of the map. The georeferenced image scales and rotates as the user zooms, tilts, and rotates the map. The geographic location of the raster image content, supplied with `LatLngQuad`, can be non-axis aligned.

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

The `setImage()` method is a convenient way to update the `ImageSource`'s image by passing in a drawable.

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

### Custom geometry

A `CustomGeometrySource` is helpful in situations when you have data which is dynamically generated or needs to be loaded on demand. A `FeatureCollection` with any type and number of GeoJSON geometries can be used in a `CustomGeometrySource`.

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

One example of `CustomGeometrySource` usage is to create a black grid on top of the map. This example's code can be found in [the `GridSourceActivity` of the Maps SDK for Android test application](https://github.com/mapbox/mapbox-gl-native-android/blob/master/MapboxGLAndroidSDKTestApp/src/main/java/com/mapbox/mapboxsdk/testapp/activity/style/GridSourceActivity.java).


### Raster DEM

`RasterDemSource` supports Mapbox Terrain RGB (`mapbox://mapbox.terrain-rgb`) and [Mapzen Terrarium](https://mapzen.com/documentation/terrain-tiles/formats/#terrarium) tile formats. This source should be used with a Maps SDK [`HillshadeLayer`](#hillshade).

The Mapbox terrain tileset is for adding hill terrain to any Mapbox map. Runtime styling can also be used to change the hillshade appearance.

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

## Layers

While sources hold the data, layers are used to style and display the information. Several layer types are offered depending on your source geometry. Except for layers of the background type, each layer needs to refer to a source. You can optionally filter features and then define how those features are styled.

Each layer offers a `setProperties` API which can be used to style the layer in many different ways. Note that instead of creating different layers depending on certain cases inside your source data, it's recommended to use data-driven styling to reduce the number of layers that the map needs to render.

<!-- TODO: talk about mapboxMap.getLayers(); -->
<!-- TODO: talk about mapboxMap.addLayerBelow(); -->

### Background

The background layer type is unique in that it doesn't require a source. Background layers can be a solid color or a pattern.

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


### Fill

Fill layers have an enclosed shape geometry that can be useful for marking areas on a map. Use a `FillLayer` with GeoJSON `Polygon` or `MultiPolygon` geometries.  The geometry is like a line layer consisting of a series of coordinates in a particular order with the first and last points having the same coordinate. The geometry is "enclosed" when the coordinate list starts and ends with the same coordinates. If the geometry isn't enclosed, the `FillLayer` will render but some vertices and sides might be cut off by the tile boundaries.

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

To alter the shape of the geometry once you have added it, the layer can stay with no changes needed, only the source it's using should be updated. The layer will always display the latest updates inside its source.




### Line

{{
  <RelatedPage
    url="/android/maps/examples/create-a-line-layer/"
    title="Create and display line layer"
    contentType="guide">
}}
Build a GeoJSON FeatureCollection with the line geometry and then display it on the map using a line layer.
{{</RelatedPage>}}

A series of coordinates can be combined to create a line segment that shows on a map. Between each pair of coordinates, a line segment's created which gets drawn straight and connects the two points.

<!-- NOTE add something visual here building the line layer? -->

Before beginning, you'll want to make sure that the Source your layer will be using has lineStrings as part of its geometry and you can find an example of this in the GeoJSON source section. Once the source has been created and added to the map, a `lineLayer` can be started, and properties can be set.

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


### Symbol

Symbol layers show a single location on the map with either an icon or text label. Like GL Markers and Marker Views, the symbol layer can represent the same data and offers the most power for in map displaying. To begin with, you will add a marker image to the map and then display it as a symbol layer.

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

Not only can symbol layers mark locations on the map using an image, but they can also display text directly on the map. The SDK handles text symbol layers in a similar process to the image snippet given above, only the properties of the layer change.



<!-- NOTE build an example showing how to display text stored in source property -->

#### Icon anchors and offsets

By default a `SymbolLayer` icon is anchored to the coordinate at the icon's _center_. While this is appropriate for many icons, it may not be appropriate for icons shaped like teardrop markers or pushpins, when you would expect the bottom of the icon to be anchored to the coordinate. In these cases, you can adjust the position of the icon by specifying an **anchor** or setting an **offset**.

{{<Note title="Default marker icon">}}
If you are using the `mapbox_marker_icon_default` image included with the Mapbox Maps SDK for Android, you do not need to adjust the icon anchor. The image includes bottom spacing that's equal to half the pin height, so the point will align with the coordinate.
{{</Note>}}

**Anchor:** Using `PropertyFactory.iconAnchor` is the recommended way to adjust the icon's location. For example, using `PropertyFactory.iconAnchor(Property.ICON_ANCHOR_BOTTOM)` tells the Maps SDK to attach the image's bottom to the coordinate.  

**Offset:** `PropertyFactory.iconOffset` is alternative solution that allows you to specify the distance that you'd like the icon to be moved from the anchor point. For example, using `PropertyFactory.iconOffset(new Float[] {0f, VERTICAL_FLOAT_VALUE})`, where `VERTICAL_FLOAT_VALUE` is some distance, tells the Maps SDK to move the icon that distance (up or down) from the coordinate. The appropriate `VERTICAL_FLOAT_VALUE` will depend on the height of your icon.

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


### Raster

Raster layers are typically a collection of images that display on top of the base map tiles. While vector tiles are preferred, satellite imagery or legacy map styles render as a raster layer.

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


One common use case for a `RasterLayer` is adding a layer of satellite tiles to the map:

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

### Circle

Circle layers have a single center coordinate which comes from the source data. It's a geographically accurate projection of a circle on the Earth's surface drawn on the map. A few default properties are provided but can be overridden when the layer's first created.

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

### Fill extrusion

Fill extrusion layers add 3D polygon "extrusions" to the map. This layer is often used to show 3D buildings, but any type of `Polygon` shape can be extruded with this layer type. Find more information on styling 3D shape extrusions in the [Extrusions guide](/android/maps/overview/extrusions).

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

### Hillshade

A `HillshadeLayer` displays elevation data that comes from Mapbox Terrain RGB tile formats. This layer should be used with a [`RasterDemSource`](/android/maps/overview/data-driven-styling/#raster-dem).

Read more information on accessing elevation data in the [Access elevation data](https://docs.mapbox.com/help/troubleshooting/access-elevation-data/) guide.

[The hillshade section of the official Mapbox Style Specification](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#hillshade) has information on styling the hillshading's shadow color, light direction, and more.

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

{{<RelatedPage url="/android/maps/examples/add-a-hillshade-layer/" title="Hillshading" contentType="example">}}
Add and style terrain hillshading.
{{</RelatedPage>}}

### Heatmap
The Maps SDK's `HeatmapLayer` provides heatmap visualizations and styling options for peformant heatmaps. The two main categories for using this layer are: 
- Dense `Point` data. Heatmaps are a useful way to visualize the density of a particular phenomenon when it would be visually overwhelming to display each point individually.
- Interpolating discrete values over a continuous surface, creating a smooth gradient between those points. This type of heatmap is less common and does not visualize density by aggregating features within a set of boundaries in the way a choropleth map does, but instead displays a continuous gradient between points. For example, your town may only have a few weather stations, but your favorite weather app displays a smooth gradient of temperatures across the entire area of your town. For your local weather service, it is reasonable to assume that, if two adjacent stations report different temperatures, the temperature between them will transition gradually from one to the next. [Read the heatmap section in Mapbox's style specification](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/#heatmap) to learn more about the radius, weight, intensity, color, and opacity options for heatmaps.

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
    title="Heatmap data"
    contentType="example">
}}
Use a HeatmapLayer to visualize data
{{</RelatedPage>}}

{{
  <RelatedPage
    url="/android/maps/examples/add-multiple-heatmap-styles"
    title="Heatmap data"
    contentType="example">
}}
Use expressions to style the HeatmapLayer with precise control
{{</RelatedPage>}}


## Removing sources and layers

A source cannot be removed if it's still used by any layer. The removal will fail and log a console warning. Starting in the `7.0.0` release of the Maps SDK, the SDK changed `remove` methods to return a `boolean` which states whether the removal was successful.

All layers using a particular source must be removed before that source can be removed.

Removing a layer:

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


Removing a source:

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

## Modify properties

Sources and layers aren't immutable so they can be modified anytime during the map render. For example, to alter the fill color of a layer after it's been added to the map, you use the map's `Style` object to get the layer and set the property.

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

In a GeoJSON source, you are able to change, add, remove, or replace the FeatureCollection. You can pass in a `List` of `Feature`s, a single `Feature`, or a `Geometry`:

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

## Capturing click events

Layers are not clickable and don't expose any event listeners for you to handle user input. Instead, the [map querying tools](/android/maps/overview/query) can help you detect when a user has interacted with the map. For example, when a `FillLayer`'s `Polygon` has been tapped on.

Alternatively, you can use the [Mapbox Annotation Plugin](/android/plugins/overview/annotation/#manager), which provides `onClick()` and `onLongClick()` listening.
