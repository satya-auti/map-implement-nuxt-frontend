<template>
  <!-- @loaded="onMapLoaded" -->
  <main class="w-screen h-screen">
    <head>
      <meta charset="utf-8" />
      <title>Demo: Local search with the Geocoding API</title>
      <meta name="robots" content="noindex, nofollow" />
      <meta name="viewport" content="width=device-width, initial-scale=1" />
      <!-- <script src="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.js"></script> -->
      <!-- mapbox gl css -->
      <link
        href="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.css"
        rel="stylesheet"
      />
      <!-- <script src="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.min.js"></script> -->
      <!-- Mapbox Geocoder (search function) -->
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.css"
        type="text/css"
      />
      <!-- Mapbox Draw -->
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-draw/v1.3.0/mapbox-gl-draw.css"
        type="text/css"
      />
      <!-- Directions css -->
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-directions/v4.1.0/mapbox-gl-directions.css"
        type="text/css"
      />

      <!-- <script src="https://js.sentry-cdn.com/9c5feb5b248b49f79a585804c259febc.min.js" crossorigin="anonymous"></script> -->
    </head>
    <v-map class="w-full h-full" :options="state.map" @loaded="getMapData">
      <!-- @loaded="styleView" -->
      <!-- @click="styleView" -->
      <select
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
        <!-- <option name="view" id="satellite-v9" v-bind:value="satellite-v9" selected>
          <label>satellite</label>
        </option>
        <option name="view" id="light-v10" value="light-v10">
          <label>light</label>
        </option>
        <option name="view" id="dark-v10" value="dark-v10">
          <label>Dark</label>
        </option>
        <option name="view" id="streets-v11" value="streets-v11">
          <label>Streets</label>
        </option>
        <option name="view" id="outdoors-v11" value="outdoors-v11">
          <label>Outdoors</label>
        </option> -->
      </select>

      <div class="zIndex2">
        <input type="file" id="fileData" />
        <!-- @change="uploadCsvFile" -->
        <button @click="uploadCsvFile">Submit</button>
      </div>
    </v-map>
  </main>
</template>

<script setup lang="ts">
// src="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.js "
// https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.min.js
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import MapboxDraw from "@mapbox/mapbox-gl-draw";
import MapboxDirection from "@mapbox/mapbox-gl-directions/dist/mapbox-gl-directions";
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

async function styleView(map: mapboxgl.Map) {
  console.log("model data ", data.layerData);
  let layerId = data.layerData;
  //   console.log("set- >", "mapbox://styles/mapbox/" + layerId);
  //   state.map.style = "mapbox://styles/mapbox/" + layerId;
  //   const options: any = document.getElementsByTagName("option");
  //   console.log("options ", options);
  //   let setMapStyle = "mapbox://styles/mapbox/" + layerId;
  console.log("map", map);
  //   data.temp1.setStyle("mapbox://styles/mapbox/" + layerId);
  data.map1.setStyle("mapbox://styles/mapbox/" + layerId);
  //   state.map.style = setMapStyle;

  //   const layerList = document.getElementById("styles");
  //   console.log("layer list ", layerList);

  //   const options: any = layerList.getElementsByTagName("option");
  //   console.log("options ", options);
  //   //   options.forEach((a) => console.log("map - > ", a));
  //   for (const option of options) {
  //     console.log("in for loop" + option);
  //     console.log("demo - > ", option.value);

  //     option.onclick = (layer) => {
  //       console.log("click on select" + layer);
  //       console.log("id ", layer.value);

  //       const layerId = layer.value;
  //       map.setStyle("mapbox://styles/mapbox/" + layerId);
  //     };
  //   }
}

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

  //   Geo Coder search start
  // Add the control to the map.
  map.addControl(
    new MapboxGeocoder({
      accessToken:
        "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
      mapboxgl: mapboxgl,
    })
  );
  // Geo Coder search Ends

  // Live locater
  map.addControl(
    new mapboxgl.GeolocateControl({
      positionOptions: {
        enableHighAccuracy: true,
      },
      // When active the map will receive updates to the device's location as it changes.
      trackUserLocation: true,
      // Draw an arrow next to the location dot to indicate which direction the device is heading.
      showUserHeading: true,
    })
  );
  // Live locater end

  // MapBox Draw Tool start
  var Draw = new MapboxDraw();

  // Map#addControl takes an optional second argument to set the position of the control.
  // If no position is specified the control defaults to `top-right`. See the docs
  // for more details: https://docs.mapbox.com/mapbox-gl-js/api/#map#addcontrol

  map.addControl(Draw, "top-right");
  // MapBox Draw Tool end

  //   Directions starts
  map.addControl(
    new MapboxDirection({
      accessToken:
        "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
    }),
    "bottom-left"
  );
  // directions Ends

  mapMarker(map);
  styleView(map);

  //   //   Polygon Starts
  let pointsData = [];
  data.allMapDataPoints.map((ele) => {
    let arrFormat = [ele.lat, ele.lon];
    pointsData.push(arrFormat);
  });
  console.log("coordinates", pointsData);

  map.addSource("MyPolygon", {
    type: "geojson",
    data: {
      type: "FeatureCollection",
      features: [
        {
          type: "Feature",
          properties: {},
          geometry: {
            type: "Polygon",
            coordinates: [
              pointsData,
              //   [
              //     [73.70703125, 18.96818922264095],
              //     [72.771484375, 16.46818922264095],
              //     [72.771484375, 19.5271348225978],
              //     [-73.70703125, 16.5271348225978],
              //     // [74.04931277036667, 19.266912177018096],
              //     [74.70703125, 20.46818922264095],
              //   ],
            ],
          },
        },
      ],
    },
  });
  map.addLayer({
    id: "MyPolygon",
    type: "fill",
    source: "MyPolygon", // reference the data source
    layout: {},
    paint: {
      "fill-color": "#0080FF", // blue color fill
      "fill-opacity": 0.5,
    },
  });
  //   // Polygon Ends

  // // Fly the map to the location.
  // map.flyTo({
  //   center: [73.70703125, 18.96818922264095],
  //   speed: 0.5,
  // });
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
  // let lat = e.lngLat.lat;
  // let lon = e.lngLat.lng;
  // let lat = data.lat;
  // let lon = data.lon;
  // console.log("lat lon - " + lat, lon);

  data.allMapDataPoints.map((ele) => {
    new mapboxgl.Marker({
      draggable: true,
      color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
    })
      //   .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .setLngLat([ele.lat, ele.lon])
      //   .setLngLat([74.04931277036667, 19.266912177018096])
      .addTo(map);
    // new mapboxgl.Popup()
    //   .setLngLat([74.04931277036667, 19.266912177018096])
    //   //   .setLngLat([data.lat, data.lon])
    //   //   .setHTML("hello world")
    //   .addTo(map);
  });
  //   });
  //   [74.04931277036667, 19.266912177018096],
}

async function uploadCsvFile(event) {
  console.log("event", event);
  let formData = new FormData();
  //   let file = fileData as HTMLInputElement | null;
  let file = document.getElementById("fileData") as HTMLInputElement | null;
  //   {
  //     csv: event.target.files[0],
  //   };
  formData.append("csv", file.files[0]);
  console.log("file", file);
  //   console.log("file", file.files[0]);

  // http://localhost:3040/map/upload
  let response = await $fetch("http://localhost:3040/map/upload", {
    method: "POST",
    // body: payload,
    body: formData,
  });

  console.log("res", response);
}
// }
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
  top: 0px;
  z-index: 1;
}
.zIndex1 {
  position: relative;
  left: 300px;
  top: 0px;
  z-index: 1;
}
.zIndex2 {
  position: relative;
  left: 400px;
  top: 0px;
  z-index: 1;
}
</style>
