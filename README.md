## leaflet.js in browser print plugin

A [leaflet](http://www.leafletjs.com) plugin which allows users to print full page map directly from the browser [DEMO](https://igor-vladyka.github.io/leaflet.browser.print/)
* Pros:
	- Compatible with Leaflet v0.7.7 and v1+.
	- Any page size from range A0-A10 to B0-B10 can se used.
	- Available 4 print modes, you can chose any you want and even create your own.
	- Everything in browser, no external apps or dependencies, print your map in one click.
	- Any additional page content can be printed together with a map.
	- And many more...

* Cons:
	- 'Custom' print mode not supported in Leaflet v0.7.7 on IE.

### Other examples:
Check out the:
[DEMO v0.7.7](https://igor-vladyka.github.io/leaflet.browser.print/examples/v0.7.7.html),
[DEMO v1.2.0](https://igor-vladyka.github.io/leaflet.browser.print/examples/v1.2.0.html),
[DEMO with localization](https://igor-vladyka.github.io/leaflet.browser.print/examples/localization_v1.2.0.html),
[DEMO with print objects manipulations](https://igor-vladyka.github.io/leaflet.browser.print/examples/manipulations_v1.2.0.html),
[DEMO with map legend printing](https://igor-vladyka.github.io/leaflet.browser.print/examples/print-with-legend_v1.2.0.html),

### Downloads
**NPM**
````
	npm install --save leaflet.browser.print
````

**YARN**
````
	yarn add leaflet.browser.print
````

### Usage
**Step 1.** Include the required js and css files in your document.

```html
	<script src="dist/leaflet.browser.print.min.js"></script>
```

**Step 2.** Add the following line of code to your map script

``` js
	L.control.browserPrint().addTo(map)
```

**Step 3.**
You can pass a number of options to the plugin to control various settings.

| Option        | Type         | Default      | Description   |
| ------------- |--------------|--------------|---------------|
| title         | String       | 'Print map'  | Sets the text which appears as the tooltip of the print button |
| documentTitle | String       | ''  		  | Sets the text which appears as the print page title |
| position      | [Leaflet control position](http://leafletjs.com/reference-0.7.7.html#control-position) | 'topleft' | Position the print button |
| printModes    | Array        | ["Portrait", "Landscape", "Auto", "Custom"] | Collection of page print actions |
| printLayer    | [Leaflet tile layer](http://leafletjs.com/reference-0.7.7.html#tilelayer) | null | A tiles layer to show instead of all current active tile layers |
| closePopupsOnPrint | Boolean | true | Indicates if we need to force popup closing for printed map |
| contentSelector | String | "[leaflet-browser-print-content]" | Content selector for printed map, will select and dynamically inject content on printed maps. For full functionality please check "Printing additional content section" |
| pagesSelector | String | "[leaflet-browser-print-pages]" | Pages selector for printed map, will select and dynamically inject additional pages content on printed maps. |
| manualMode | Boolean | false | Adds a button with id='leaflet-browser-print--manualMode-button' for debugging purpose, also can be used to print map with external button. |

Here's an example of passing through some options:
``` js
var customActionToPrint = function(context) {
	return function() {
		window.alert("We are printing the MAP. Let's do Custom print here!");
		context._printCustom();
	}
}

L.control.browserPrint({
	title: 'Just print me!',
	documentTitle: 'Map printed using leaflet.browser.print plugin',
	printLayer: L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/watercolor/{z}/{x}/{y}.{ext}', {
					attribution: 'Map tiles by <a href="http://stamen.com">Stamen Design</a>, <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> &mdash; Map data &copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
					subdomains: 'abcd',
					minZoom: 1,
					maxZoom: 16,
					ext: 'png'
				}),
	closePopupsOnPrint: false,
	printModes: [
		L.control.browserPrint.mode("Alert", "User specified print action", "A6", customActionToPrint, false),
		L.control.browserPrint.mode.landscape(),
		"Portrait",
		L.control.browserPrint.mode.auto("Automatico", "B4"),
		L.control.browserPrint.mode.custom("Séléctionnez la zone", "B5")
	],
	manualMode: false
}).addTo(map);
```

### Print Mode Details

| Mode          | Description    |
| ------------- | -------------- |
| Portrait      | Print currently visual part of the map with Portrait page dimensions |
| Landscape     | Print currently visual part of the map with Landscape page dimensions |
| Auto          | Track all active map layers (markers, lines, polygons, etc. ) and tries to fit them in print page in Portrait or Landscape page dimensions |
| Custom        | Allows you to select rectangle for printing, and then fit it in Portrait or Landscape page dimensions |

General mode and shortcuts:
``` js
	L.control.browserPrint.mode(
		/*Mode from table above*/,
		/*Text to represent mode on button*/,
		/*Page Size that can basically be in range A0-A10 and B0-B10.*/
		/*Custom function that can be executed to print map*/,
		/*Indicates if we need to force bounds invalidation(true) or just center the map and use current zoom lvl(false)*/
	);

	L.control.browserPrint.mode.landscape();
	L.control.browserPrint.mode.portrait();
	L.control.browserPrint.mode.auto();
	L.control.browserPrint.mode.custom();
```


### Custom Print Mode Configurations

``` js
	var customActionToPrint = function(context) {
		return function() {
			window.alert("We are printing the MAP. Let's do Custom print here!");
			context._printCustom();
		}
	}
	L.control.browserPrint.mode("Alert", "User specified print action", "A6", customActionToPrint, false),

```

### Map Events

| Map Event           | Value           		 			   | Description 													 | Purpose |
| ------------------- | -------------------------------------- | --------------------------------------------------------------- | ------- |
| browser-pre-print   | { pageSize, pageBounds, printObjects } | Fire before print started, allows manipulation with map objects.| For DOM manipulation with real map objects. |
| browser-print-start | { printLayer, printMap, printObjects } | Fire on print started, before all print calculations is done.   | For DOM manipulation with print map and print objects. |
| browser-print       | { printLayer, printMap, printObjects } | Fire right before native print. 								 | For DOM manipulation with print map and print objects. |
| browser-print-end   | { printLayer, printMap, printObjects } | Fire on print end, after we refresh map to show initial view.   | For DOM manipulation with real map objects after print |

Example can be found here: [DEMO with print objects manipulations](https://igor-vladyka.github.io/leaflet.browser.print/examples/manipulations_v1.2.0.html);

### Printing additional content section

To add additional printing content (in addition to a map itself) we need to specify content that should be added. [Demo](https://igor-vladyka.github.io/leaflet.browser.print/);
By default contentSelector: '[leaflet-browser-print-content]' so we need a content with an 'leaflet-browser-print-content' attribute.

Code example:

``` html
<style leaflet-browser-print-content>
	.grid-print-container { // grid holder that holds all content (map and any other content)
		grid-template: auto 1fr auto / 1fr;
		background-color: orange;
	}
	.grid-map-print { // map container itself
		grid-row: 2;
	}
	.title { // Dynamic title styling
		grid-row: 1;
		justify-self: center;
		color: white;
	}
	.sub-content { // Dynamic sub content styling
		grid-row: 5;
		padding-left: 10px;
	}
</style>
<h1 class="title" leaflet-browser-print-content>Leaflet Browser print TITLE</h1>
<h3 class="sub-content" leaflet-browser-print-content>Leaflet Browser print SUB TITLE text</h3>
```

On print, plugin will scan DOM by contentSelector, and will add content to print may.

````
We are using CSS-GRID to position all controls on a print page.
Therefor it's not supportable in all browsers, for more information please visit [caniuse.com](https://caniuse.com/#feat=css-grid).
````

### Angular 2+
````
See chapter 4 of https://github.com/Asymmetrik/ngx-leaflet-tutorial-plugins/tree/master/Leaflet.BrowserPrint
````

### New print layers registration
To add missing print layers you need to explicitly indicate layer, it's identifier and construction function that will return actual layer object.

Example of WMS registration:
``` js
L.Control.BrowserPrint.Utils.registerLayer(
	L.TileLayer.WMS,
	"L.TileLayer.WMS",
	function(layer, utils) {
		return L.tileLayer.wms(layer._url, layer.options);
	}
);
```

List of pre-registered layers available for printing:
* L.SVG
* L.Canvas
* L.MarkerClusterGroup
* L.TileLayer.WMS
* L.TileLayer
* L.ImageOverlay
* L.Marker
* L.Popup
* L.Circle
* L.CircleMarker
* L.Rectangle
* L.Polygon
* L.MultiPolyline
* L.MultiPolygon
* L.Polyline
* L.GeoJSON
* L.FeatureGroup
* L.LayerGroup
* L.Tooltip

If you want to override any of those, please register your own builder for them.

### Important notes
````
Currently if you want to use layerGroup or featureGroup to print groups of layers,
please create a group, add all items to it and only then add a group on the map.
````

````
Unfortunately 'Custom' mode is not working correctly for Leaflet v.0.7.7 in all IE browsers.
````

### Acknowledgements
Thanks to [Rowan Winsemius](https://github.com/rowanwins/leaflet-easyPrint) for general idea with a map print functionality.

Thanks to [Jan Pieter Waagmeester](https://github.com/jieter/leaflet-clonelayer) for an idea that helped with map print functionality.

Also thanks to [IcoMoon](http://icomoon.io/) for the print icon.
