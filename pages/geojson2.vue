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
      <!-- <link
          rel="stylesheet"
          href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.css"
          type="text/css"
        /> -->
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
      <!-- <link
          href="https://api.mapbox.com/mapbox-gl-js/v2.10.0/mapbox-gl.css"
          rel="stylesheet"
        /> -->
      <!--  -->
      <!-- <script src="https://js.sentry-cdn.com/9c5feb5b248b49f79a585804c259febc.min.js" crossorigin="anonymous"></script> -->
    </head>
    <!-- <div>
      <pre id="info"></pre>
      <pre id="features"></pre>
    </div> -->
    <v-map class="w-full h-full" :options="state.map" @loaded="getMapData">
      <!-- @loaded="styleView" -->
      <!-- @click="styleView" -->
      <!-- <select
          class="zIndex"
          id="styles"
          @change="styleView"
          v-model="data.layerData"
        >
          <option value="" disabled>Select Style View</option>
  
          <option
            v-for="view in layerValues.options"
            :key="view.name"
            v-bind:value="view.value"
          >
            {{ view.name }}
          </option>
        </select> -->

      <div class="zIndex">
        <pre id="info"></pre>
        <pre id="features"></pre>
      </div>
    </v-map>
    <!-- Polygon draw calculate start -->
    <!-- <div class="calculation-box">
          <p>Click the map to draw a polygon.</p>
          <div id="calculated-area"></div>
        </div> -->
    <!-- polygon ends -->
  </main>
</template>

<script setup lang="ts">
// src="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.js "
// https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.min.js
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
// var MapboxDirections = require("@mapbox/mapbox-gl-directions");
// import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import MapboxDraw from "@mapbox/mapbox-gl-draw";
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

  // Showing coridinates & Geojson object start
  map.on("mousemove", (e) => {
    document.getElementById("info").innerHTML =
      // `e.point` is the x, y coordinates of the `mousemove` event
      // relative to the top-left corner of the map.
      JSON.stringify(e.point) +
      "<br />" +
      // `e.lngLat` is the longitude, latitude geographical position of the event.
      JSON.stringify(e.lngLat.wrap());
  });
  //   // -----------------On click Get features under the mouse pointer---------------
  map.on("click", (e) => {
    const features = map.queryRenderedFeatures(e.point);
    // Limit the number of properties we're displaying for
    // legibility and performance
    const displayProperties = [
      "type",
      "properties",
      "id",
      // "layer",
      "source",
      "sourceLayer",
      "state",
    ];
    const displayFeatures = features.map((feat) => {
      const displayFeat = {};
      displayProperties.forEach((prop) => {
        displayFeat[prop] = feat[prop];
      });
      return displayFeat;
    });
    // Write object as string with an indent of two spaces.
    document.getElementById("features").innerHTML = JSON.stringify(
      displayFeatures,
      null,
      3
    );
  });
  // Showing coridinates & Geojson object end

  // MapBox Draw Tool start
  var Draw = new MapboxDraw();

  // Map#addControl takes an optional second argument to set the position of the control.
  // If no position is specified the control defaults to `top-right`. See the docs
  // for more details: https://docs.mapbox.com/mapbox-gl-js/api/#map#addcontrol

  map.addControl(Draw, "top-right");

  //   map.on("load", function () {
  //     // ALL YOUR APPLICATION CODE
  //   });
  // Mapbax draw tool end

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

  //   //   Directions starts
  //   map.addControl(
  //     new MapboxDirections({
  //       accessToken:
  //         "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
  //     }),
  //     "top-left"
  //   );
  //   // directions Ends

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
.zIndex {
  position: absolute;
  left: 0px;
  background-color: white;
  top: 0px;
  z-index: 1;
}
</style>
