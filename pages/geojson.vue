<template>
  <main class="w-screen h-screen">
    <head>
      <meta charset="utf-8" />
      <title>Demo: Local search with the Geocoding API</title>
      <meta name="robots" content="noindex, nofollow" />
      <meta name="viewport" content="width=device-width, initial-scale=1" />
      <!-- <script src="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.js"></script> -->
      <link
        href="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.css"
        rel="stylesheet"
      />
      <!-- <script src="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.min.js"></script> -->
      <!-- GeoCoder CSS -->
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.css"
        type="text/css"
      />
      <!-- Directions css -->
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-directions/v4.1.0/mapbox-gl-directions.css"
        type="text/css"
      />
      <!-- Mapbox Draw Tool -->
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-draw/v1.3.0/mapbox-gl-draw.css"
        type="text/css"
      />
      <!--  -->
      <!-- MapBox GL -->
      <link
        href="https://api.mapbox.com/mapbox-gl-js/v2.10.0/mapbox-gl.css"
        rel="stylesheet"
      />
      <!--  -->
      <!-- <script src="https://js.sentry-cdn.com/9c5feb5b248b49f79a585804c259febc.min.js" crossorigin="anonymous"></script> -->
    </head>
    <v-map class="w-full h-full" :options="state.map" @loaded="getMapData">
      <!-- <pre id="info"></pre>
      <pre id="features"></pre> -->
    </v-map>
  </main>
</template>

<script setup lang="ts">
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
// var MapboxDirections = require("@mapbox/mapbox-gl-directions");
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
// import MapboxDraw from "@mapbox/mapbox-gl-draw";
import MapboxDirections from "@mapbox/mapbox-gl-geocoder";
// import { MapboxDraw } from "@mapbox/mapbox-gl-geocoder";

const data = reactive({
  mapData: [],
  allMapDataPoints: [],
  findMapData: [],
  mark: [],
  lat: 0,
  lon: 0,
  layerData: "",
  temp1: {},
  map1: {},
  findString: "",
});

const layerValues = reactive({
  options: [
    {
      name: "Satellite",
      value: "satellite-v9",
    },
    {
      name: "Light",
      value: "light-v10",
    },
    {
      name: "Street",
      value: "streets-v11",
    },
    {
      name: "Outdoor",
      value: "outdoors-v11",
    },
    {
      name: "Dark",
      value: "dark-v10",
    },
  ],
});

const state = reactive({
  map: {
    accessToken:
      "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
    //   "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ",
    style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    // style: "https://basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json",
    // center: [444.04931277036667, 26.266912177018096] as number[], //uses longitude, latitude
    center: [74.04931277036667, 19.266912177018096],
    zoom: 6,
    maxZoom: 22,
    // container: "map",
  } as mapboxgl.MapboxOptions,
});

// async function styleView(map: mapboxgl.Map) {
//   console.log("model data ", data.layerData);
//   let layerId = data.layerData;
//   //   let setMapStyle = "mapbox://styles/mapbox/" + layerId;
//   console.log("map", map);
//   //   data.temp1.setStyle("mapbox://styles/mapbox/" + layerId);
//   data.map1.setStyle("mapbox://styles/mapbox/" + layerId);
//   //       map.setStyle("mapbox://styles/mapbox/" + layerId);
// }

// function onMapLoaded(map) {
//   ///---------Open marker on perticular location--------------
//   // const marker = new mapboxgl.Marker({
//   //     color: "#000000",
//   //     draggable: true
//   // }).setLngLat([30.5, 50.5])
//   //     .addTo(map);
//   //   var col = ["#FF0000", "0000FF", "#00FFFF"];
//   //   // col.forEach((ele) => {
//   //   // color: '#000000',
//   //   map.on("click", (e) => {
//   //     // map.setCenter(data.mapData[0].geom.coordinates);
//   //     console.log(e);
//   //     console.log(e.lngLat);
//   //     // console.log(ele);
//   //     new mapboxgl.Marker({
//   //       draggable: true,
//   //       color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
//   //     })
//   //       .setLngLat([e.lngLat.lng, e.lngLat.lat])
//   //       //   .setLngLat([data.lat, data.lon])
//   //       //   .setPopup(new mapboxgl.Popup().setHTML("<h1>Hello World!</h1>"))
//   //       .addTo(map);
//   //     new mapboxgl.Popup()
//   //       .setLngLat([e.lngLat.lng, e.lngLat.lat])
//   //       //   .setLngLat([data.lat, data.lon])
//   //       .setHTML("hello world")
//   //       .addTo(map);
//   //   });
// }

async function getMapData(map: mapboxgl.Map) {
  console.log("load ", map);
  data.map1 = map;
  //   map.setStyle("mapbox://styles/mapbox/dark-v10");
  // http://localhost:3030/map
  //   http://localhost:3040/map
  data.mapData = await $fetch("http://localhost:3040/map");
  console.log("all data", data.mapData);

  data.findMapData = data.mapData;
  data.mapData.map((ele) => {
    let pointsData = {
      lat: ele.lat,
      lon: ele.lon,
    };
    data.allMapDataPoints.push(pointsData);
  });

  console.log("only points ", data.allMapDataPoints);

  console.log("MapData - ", data.mapData[0]);

  //   // MapBox Draw Tool start
  //   var Draw = new MapboxDraw();

  //   // Map#addControl takes an optional second argument to set the position of the control.
  //   // If no position is specified the control defaults to `top-right`. See the docs
  //   // for more details: https://docs.mapbox.com/mapbox-gl-js/api/#map#addcontrol

  //   map.addControl(Draw, "top-left");

  //   map.on("load", function () {
  //     // ALL YOUR APPLICATION CODE
  //   });
  //   // Mapbax draw tool end

  // Showing coridinates & Geojson object start
  map.on("click", (e) => {
    document.getElementById("info").innerHTML =
      // `e.point` is the x, y coordinates of the `mousemove` event
      // relative to the top-left corner of the map.
      JSON.stringify(e.point) +
      "<br />" +
      // `e.lngLat` is the longitude, latitude geographical position of the event.
      JSON.stringify(e.lngLat.wrap());
  });
  //   // -----------------On click Get features under the mouse pointer---------------
  //   map.on("click", (e) => {
  //     const features = map.queryRenderedFeatures(e.point);
  //     // Limit the number of properties we're displaying for
  //     // legibility and performance
  //     const displayProperties = [
  //       "type",
  //       "properties",
  //       "id",
  //       // "layer",
  //       "source",
  //       "sourceLayer",
  //       "state",
  //     ];
  //     const displayFeatures = features.map((feat) => {
  //       const displayFeat = {};
  //       displayProperties.forEach((prop) => {
  //         displayFeat[prop] = feat[prop];
  //       });
  //       return displayFeat;
  //     });
  //     // Write object as string with an indent of two spaces.
  //     document.getElementById("features").innerHTML = JSON.stringify(
  //       displayFeatures,
  //       null,
  //       3
  //     );
  //   });
  // Showing coridinates & Geojson object end

  // Geo Coder search start
  // Add the control to the map
  //   mapboxgl.accessToken = state.map.accessToken;
  // map.addControl(
  //   new MapboxGeocoder({
  //     accessToken: mapboxgl.accessToken,
  //     //   "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
  //     mapboxgl: mapboxgl,
  //   })
  // );

  //     let geocoder = new MapboxGeocoder({
  //     accessToken: mapboxgl.accessToken,
  //     mapboxgl: mapboxgl
  // });
  // map.addControl(geocoder);

  // Geo Coder search ends

  // //   Directions starts
  // map.addControl(
  //     new MapboxDirections({
  //     accessToken:
  //         "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
  //     }),
  //     "top-left"
  // );
  // // directions Ends

  //   Line Start- >
  map.addSource("demoLine", {
    type: "geojson",
    data: {
      type: "Feature",
      properties: {},
      geometry: {
        type: "LineString",
        coordinates: [
          [72.685546875, 19.145168196205297],
          [74.091796875, 15.961329081596647],
        ],
      },
    },
  });
  map.addLayer({
    id: "outline",
    type: "line",
    source: "demoLine",
    layout: {},
    paint: {
      "line-color": "#00F",
      "line-width": 3,
    },
  });
  // Line end

  // // Circle try
  //   map.on("load", () => {
  //     // Add the vector tileset as a source.
  //     map.addSource("ethnicity", {
  //       type: "vector",
  //       url: "mapbox://examples.8fgz4egr",
  //     });
  //     map.addLayer({
  //       id: "population",
  //       type: "circle",
  //       source: "ethnicity",
  //       "source-layer": "sf2010",
  //       paint: {
  //         // Make circles larger as the user zooms from z12 to z22.
  //         "circle-radius": {
  //           base: 1.75,
  //           stops: [
  //             [12, 2],
  //             [22, 180],
  //           ],
  //         },
  //         // Color circles by ethnicity, using a `match` expression.
  //         "circle-color": [
  //           "match",
  //           ["get", "ethnicity"],
  //           "White",
  //           "#fbb03b",
  //           "Black",
  //           "#223b53",
  //           "Hispanic",
  //           "#e55e5e",
  //           "Asian",
  //           "#3bb2d0",
  //           /* other */ "#ccc",
  //         ],
  //       },
  //     });
  //   });

  mapMarker(map);
  //   styleView(map);

  //   //   Polygon Starts
  //   map.addSource("MyPolygon", {
  //     type: "geojson",
  //     data: {
  //       type: "FeatureCollection",
  //       features: [
  //         {
  //           type: "Feature",
  //           properties: {},
  //           geometry: {
  //             type: "Polygon",
  //             coordinates: [
  //               [
  //                 [74.70703125, 20.46818922264095],
  //                 [80.771484375, 20.46818922264095],
  //                 [80.771484375, 24.5271348225978],
  //                 [74.70703125, 24.5271348225978],
  //                 [74.04931277036667, 19.266912177018096],
  //                 [74.70703125, 20.46818922264095],
  //               ],
  //             ],
  //           },
  //         },
  //       ],
  //     },
  //   });
  //   map.addLayer({
  //     id: "MyPolygon",
  //     type: "fill",
  //     source: "MyPolygon", // reference the data source
  //     layout: {},
  //     paint: {
  //       "fill-color": "#0080FF", // blue color fill
  //       "fill-opacity": 0.5,
  //     },
  //   });
  //   // Polygon Ends
}

async function mapMarker(map: mapboxgl.Map) {
  data.map1 = map;

  map.on("click", (e) => {
    console.log("clicked ", e);

    new mapboxgl.Marker({
      draggable: true,
      color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
    })
      //   .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .setLngLat([e.lngLat.lng, e.lngLat.lat])
      //   .setLngLat([74.04931277036667, 19.266912177018096])
      .addTo(map);
  });

  data.allMapDataPoints.map((ele) => {
    new mapboxgl.Marker({
      draggable: true,
      color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
    })
      //   .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .setLngLat([ele.lat, ele.lon])
      //   .setLngLat([74.04931277036667, 19.266912177018096])
      .addTo(map);
  });
  //   [74.04931277036667, 19.266912177018096],
  //   //   Geo Coder search start

  //   // Add the control to the map.
  //   map.addControl(
  //     new MapboxGeocoder({
  //       accessToken:
  //         "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
  //       mapboxgl: mapboxgl,
  //     })
  //   );
  //   // Geo Coder Ends

  // Geo Polygon end

  //   //   Search by GeoCoderr
  //   const marker = new mapboxgl.Marker() // Initialize a new marker
  //     // .setLngLat([-122.25948, 37.87221]) // Marker [lng, lat] coordinates
  //     .setLngLat([73.816931277036667, 18.556912177018096])
  //     .addTo(map); // Add the marker to the map

  //   const geocoder = new MapboxGeocoder({
  //     // Initialize the geocoder
  //     accessToken:
  //       "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw", // Set the access token
  //     mapboxgl: mapboxgl, // Set the mapbox-gl instance
  //     marker: false, // Do not use the default marker style
  //     placeholder: "Search for places", // Placeholder text for the search bar
  //     // bbox: [-122.30937, 37.84214, -122.23715, 37.89838], // Boundary
  //     bbox: [74.0493127, 19.1669121, 74.2493127, 19.2269121], // Boundary for Berkeley
  //     proximity: {
  //       //   longitude: -122.25948,
  //       //   latitude: 37.87221,

  //       longitude: 74.14931277036667,
  //       latitude: 19.266912177018096,
  //     }, // Coordinates of UC Berkeley
  //   });

  //   // Add the geocoder to the map
  //   map.addControl(geocoder);

  //   // After the map style has loaded on the page,
  //   // add a source layer and default styling for a single point
  //   map.on("load", () => {
  //     map.addSource("single-point", {
  //       type: "geojson",
  //       data: {
  //         type: "FeatureCollection",
  //         features: [],
  //       },
  //     });

  //     map.addLayer({
  //       id: "point",
  //       source: "single-point",
  //       type: "circle",
  //       paint: {
  //         "circle-radius": 10,
  //         "circle-color": "#448ee4",
  //       },
  //     });

  //     // Listen for the `result` event from the Geocoder // `result` event is triggered when a user makes a selection
  //     //  Add a marker at the result's coordinates
  //     geocoder.on("result", (event) => {
  //       map.getSource("single-point").setData(event.result.geometry);
  //     });
  //   });
  //   //   Geocode End here

  //   //   Polygon start

  //   let pointsData = [];
  //   data.allMapDataPoints.map((ele) => {
  //     let arrFormat = [ele.lat, ele.lon];
  //     pointsData.push(arrFormat);
  //   });
  //   console.log("coordinates", pointsData);

  //   map.on("load", () => {
  //     // Add a data source containing GeoJSON data.
  //     map.addSource("single-point", {
  //       type: "geojson",
  //       data: {
  //         type: "Feature",
  //         geometry: {
  //           type: "Polygon",
  //           // These coordinates outline Maine.
  //           coordinates: [pointsData],
  //           // [
  //           //   [-67.13734, 45.13745],
  //           //   [-66.96466, 44.8097],
  //           //   [-68.03252, 44.3252],
  //           //   [-69.06, 43.98],
  //           //   [-70.11617, 43.68405],
  //           //   [-70.64573, 43.09008],
  //           //   [-70.75102, 43.08003],
  //           //   [-70.79761, 43.21973],
  //           //   [-70.98176, 43.36789],
  //           //   [-70.94416, 43.46633],
  //           //   [-71.08482, 45.30524],
  //           //   [-70.66002, 45.46022],
  //           //   [-70.30495, 45.91479],
  //           //   [-70.00014, 46.69317],
  //           //   [-69.23708, 47.44777],
  //           //   [-68.90478, 47.18479],
  //           //   [-68.2343, 47.35462],
  //           //   [-67.79035, 47.06624],
  //           //   [-67.79141, 45.70258],
  //           //   [-67.13734, 45.13745],
  //           // ],
  //           //   ],
  //         },
  //       },
  //     });

  //     // Add a new layer to visualize the polygon.
  //     map.addLayer({
  //       id: "point",
  //       type: "fill",
  //       source: "single-point", // reference the data source
  //       layout: {},
  //       paint: {
  //         "fill-color": "#0080ff", // blue color fill
  //         "fill-opacity": 0.5,
  //       },
  //     });
  //     // Add a black outline around the polygon.
  //     map.addLayer({
  //       id: "outline",
  //       type: "line",
  //       source: "maine",
  //       layout: {},
  //       paint: {
  //         "line-color": "#000",
  //         "line-width": 3,
  //       },
  //     });
  //   });
  // Polygon Ends
}
</script>

<style>
html,
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.w-screen {
  width: 100vw;
}
.h-screen {
  height: 100vh;
}
.h-full {
  height: 100%;
}
.w-full {
  width: 100%;
}
</style>
