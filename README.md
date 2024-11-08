
<img src="https://osmbuildings.org/logo.svg" width="100" height="88">

[OSM Buildings](https://osmbuildings.org/) is a JavaScript library for showing building geometry on interactive maps. 

This is the OSMBuildings-classics. One of the most favorable feature of the classic version is its integration with Leaflet and OpenLayers. Sadly, this version has multiple versions at GitHub, [npmjs](https://www.npmjs.com/package/osmbuildings-classic), and [jsdelivr](https://www.jsdelivr.com/package/npm/osmbuildings-classic?path=dist). The data (Building footprint GeoJSON tiles with height attribute) access method has also changed because onegeo.co hosts the data now. It seems some of these versions do not work well the new data source.

This fork is to make necessary changes to make the code work with the OneGeo data source and the latest Leaflet.

## Documentation

### Integration with Leaflet

Link Leaflet and OSM Buildings files in your HTML head section.

~~~ html
<head>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/osmbuildings-classic@3.0.0/dist/OSMBuildings.css" />

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/osmbuildings-classic@3.0.0/dist/OSMBuildings-Leaflet.js"></script>
</head>
~~~

Initialize the map engine and add a map tile layer.<br>
Position is set to Berlin at zoom level 17, I'm using Mapbox tiles here.

~~~ javascript
var map = new L.Map('map').setView([52.52020, 13.37570], 17);
new L.TileLayer('https://{s}.tiles.mapbox.com/v3/<YOUR KEY HERE>/{z}/{x}/{y}.png',
  { attribution: 'Map tiles &copy; <a href="https://mapbox.com">Mapbox</a>', maxZoom: 17 }).addTo(map);
~~~

Add the buildings layer.

~~~ javascript
var osmb = new OSMBuildings(map)
    .load('https://{s}-data.onegeo.co/maps/tiles/{z}/{x}/{y}.json?token={Your Key at OneGeo}');
osmb.style({"wallColor": "rgb(158,158,158)",
            "roofColor": "rgb(218,218,218)",
            "shadows": true})
osmb.date(new Date(2024, 11, 5, 10, 0)););
~~~

As a popular alternative, you could pass a <a href="http://www.geojson.org/geojson-spec.html">GeoJSON</a> FeatureCollection object.<br>
Geometry types Polygon, Multipolygon and GeometryCollection are supported.<br>
Make sure the building coordinates are projected in <a href="http://spatialreference.org/ref/epsg/4326/">EPSG:4326</a>.<br>
Height units m, ft, yd, mi are accepted, no given unit defaults to meters.

~~~ javascript
var geoJSON = {
  "type": "FeatureCollection",
  "features": [{
    "type": "Feature",
    "id": 134,
    "geometry": {
      "type": "Polygon",
      "coordinates": [[
        [13.37356, 52.52064],
        [13.37350, 52.51971],
        [13.37664, 52.51973],
        [13.37594, 52.52062],
        [13.37356, 52.52064]
      ]]
    },
    "properties": {
      "wallColor": "rgb(255,0,0)",
      "roofColor": "rgb(255,128,0)",
      "height": 500,
      "minHeight": 0
    }
  }]
};

new OSMBuildings(map).set(geoJSON);
~~~

## API

### Constructors

<table>
<tr>
<th>Constructor</th>
<th>Description</th>
</tr>

<tr>
<td>new OSMBuildings(map)</td>
<td>Initializes the buildings layer for a given map engine.<br>
Currently Leaflet and OpenLayers are supported.</td>
</tr>
</table>

Constants

<table>
<tr>
<th>Option</th>
<th>Type</th>
<th>Description</th>
</tr>

<tr>
<td>ATTRIBUTION</td>
<td>String</td>
<td>Holds OSM Buildings copyright information.</td>
</tr>

<tr>
<td>VERSION</td>
<td>String</td>
<td>Holds current version information.</td>
</tr>
</table>

Methods

<table>
<tr>
<th>Method</th>
<th>Description</th>
</tr>

<tr>
<td>style({Object})</td>
<td>Set default styles. See below for details.</td>
</tr>

<tr>
<td>date(new Date(2017, 15, 1, 10, 30)))</td>
<td>Set date/time for shadow projection.</td>
</tr>

<tr>
<td>each({Function})</td>
<td>A callback wrapper to override each feature's properties on read. Return false in order to skip a particular feature.<br>
Callback receives a feature object as argument.</td>
</tr>

<tr>
<td>click({Function})</td>
<td>A callback wrapper to handle click events on features.<br>
Callback receives an object { featureId{number,string}, lat{float}, lon{float} } as argument.</td>
</tr>

<tr>
<td>set({GeoJSON FeatureCollection})</td>
<td>Just add GeoJSON data to your map.</td>
</tr>

<tr>
<td>load({Provider})</td>
<td>Without parameter, it loads OpenStreetMap data tiles via an OSM Buildings proxy. This proxy enables transparent data filtering and caching.
Interface of such provider is to be published.</td>
</tr>
</table>

Styles

<table>
<tr>
<th>Option</th>
<th>Type</th>
<th>Description</th>
</tr>

<tr>
<td>color/wallColor</td>
<td>String</td>
<td>Defines the objects default primary color. I.e. #ffcc00, rgb(255,200,200), rgba(255,200,200,0.9)</td>
</tr>

<tr>
<td>roofColor</td>
<td>String</td>
<td>Defines the objects default roof color.</td>
</tr>

<tr>
<td>shadows</td>
<td>Boolean</td>
<td>Enables or disables shadow rendering, default: enabled</td>
</tr>
</table>


## Data

### OSM Tags used

<table>
<tr>
<th>GeoJSON property</th>
<th>OSM Tags</th>
</tr>

<tr>
<td>height</td>
<td>height, building:height, levels, building:levels</td>
</tr>

<tr>
<td>minHeight</td>
<td>min_height, building:min_height, min_level, building:min_level</td>
</tr>

<tr>
<td>color/wallColor</td>
<td>building:color, building:colour</td>
</tr>

<tr>
<td>material</td>
<td>building:material, building:facade:material, building:cladding</td>
</tr>

<tr>
<td>roofColor</td>
<td>roof:color, roof:colour, building:roof:color, building:roof:colour</td>
</tr>

<tr>
<td>roofMaterial</td>
<td>roof:material, building:roof:material</td>
</tr>

<tr>
<td>shape</td>
<td>building:shape[=cylinder,sphere]</td>
</tr>

<tr>
<td>roofShape</td>
<td>roof:shape[=dome]</td>
</tr>

<tr>
<td>roofHeight</td>
<td>roof:height</td>
</tr>
</table>
