<template>
  <!-- @loaded="onMapLoaded" -->
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
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.css"
        type="text/css"
      />
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
      </select>
      <!-- <div class="zIndex2">
          <input type="file" @change="uploadCsvFile" />
          <button>Submit</button>
        </div> -->
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
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
// import { MapboxDraw } from "@mapbox/mapbox-gl-geocoder";

const data = reactive({
  mapData: [],
  polygonData: [],
  allMapDataPoints: [],
  findMapData: [],
  allPolygonDataPoints: [],
  findPolygonData: [],
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

  //       map.setStyle("mapbox://styles/mapbox/" + layerId);
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
  let pt = [];
  console.log("load ", map);
  data.map1 = map;

  //   map.setStyle("mapbox://styles/mapbox/dark-v10");
  // http://localhost:3030/map
  //   http://localhost:3040/map
  //   Getting Map Data
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

  //   polygon Data
  data.polygonData = await $fetch("http://localhost:3040/polygon");
  //   temp1[0].geom.coordinates[0]
  console.log("all polygon data", data.polygonData);

  let pointsPolygonData = [];
  let copoint = [];
  data.polygonData.map((ele) => {
    // console.log("poly demo coordinates- ", ele.geom.cooridinates);
    // console.log("poly demo - ", ele);
    console.log("poly demo geom- ", ele.geom);
    pointsPolygonData.push(ele.geom.coordinates);
    ele.geom.coordinates.map((points) => {
      console.log("Geom coordinates", points);
      copoint.push(points);
      points.map((co) => {
        console.log("co point ", co);
        // copoint.push(co);
        co.map((a) => {
          console.log("a", a);
          //    copoint = a;
          //   copoint.push(a);
        });
      });

      //   data.allPolygonDataPoints.push(pointsPolygonData);
    });
    // pointsPolygonData.push(copoint);
    // console.log("Polygon Data-->", copoint);
    // console.log("Polygon Data-->", pointsPolygonData);
    // data.allPolygonDataPoints.push(pointsPolygonData);
    // let pointsPolygonData = {
    //   lat: ele.lat,
    //   lon: ele.lon,
    // };
    // data.allPolygonDataPoints.push(pointsPolygonData);
  });
  console.log("Polygon Data-->", copoint[0]);
  //   pointsPolygonData.push(copoint[0]);
  console.log("Polygon Data1-->", pointsPolygonData);
  //   data.allPolygonDataPoints.push(pointsPolygonData);
  //   console.log("only Polygon points ", data.allPolygonDataPoints);

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
  styleView(map);

  //   //   Polygon Starts
  let pointsData = [];
  copoint.map((ele) => {
    console.log("poly poly", ele);
    ele.map((poi) => {
      //   let arrFormat = [poi];
      pointsData.push(poi);
    });
    // let arrFormat = [ele[0]];
    // // let arrFormat = [ele[0], ele[1]];

    // pointsData.push(arrFormat);
  });

  let pt2 = [];
  console.log("coordinates", pointsData);
  pointsData.map((ele1) => {
    console.log("f", ele1);
    pt2 = [ele1[0], ele1[1]];
    pt.push(pt2);
  });
  console.log("pt", pt);

  data.allPolygonDataPoints = pt;
  console.log("do", data.allPolygonDataPoints);

  //   let pointsData = [];
  //   data.allMapDataPoints.map((ele) => {
  //     let arrFormat = [ele.lat, ele.lon];
  //     pointsData.push(arrFormat);
  //   });
  //   console.log("coordinates", pointsData);

  //   polygon point
  let polly = [];
  pt.map((ele) => {
    polly.push(ele);
  });
  console.log("polly", polly);

  pt.map((ele) => {
    console.log("elele", ele);

    new mapboxgl.Marker({
      draggable: true,
      color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
    })
      //   .setLngLat([e.lngLat.lng, e.lngLat.lat])
      // .setLngLat([ele.lat, ele.lon])
      .setLngLat(ele)
      //   .setLngLat([74.04931277036667, 19.266912177018096])
      .addTo(map);
  });
  //

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
              //   pointsData,
              polly,
              //   pointsData[0],
              //   copoint[0],
              //   pointsPolygonData,
              //   ('POLYGON((73.6373 18.0750,73.6374 18.0750,73.6374 18.0749,73.63 18.07491,73.6373 18.0750))
              //   [
              //     //     [73.70703125, 18.96818922264095],
              //     //     [72.771484375, 16.46818922264095],
              //     //     [72.771484375, 19.5271348225978],
              //     //     [-73.70703125, 16.5271348225978],
              //     //     // [74.04931277036667, 19.266912177018096],
              //     //     [74.70703125, 20.46818922264095],

              //     //
              //     [73.6373, 18.075],
              //     [74.6374, 19.075],
              //     [72.6374, 17.0749],
              //     [73.93, 17.07491],
              //     [73.6373, 18.075],
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
  console.log("elele");
  data.map1 = map;
  let pt = [];
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
    console.log("back");

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

  //   //   polygon point
  //   pt.map((ele) => {
  //     console.log("elele", ele);

  //     new mapboxgl.Marker({
  //       draggable: true,
  //       color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
  //     })
  //       //   .setLngLat([e.lngLat.lng, e.lngLat.lat])
  //       // .setLngLat([ele.lat, ele.lon])
  //       //   .setLngLat([ele])
  //       .setLngLat([74.04931277036667, 19.266912177018096])
  //       .addTo(map);
  //   });
  //   });

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
  //   // Geo Coder search Ends

  // Geo Polygon end

  //   Draw Polygon code
  //   const draw = new MapboxDraw({
  //     displayControlsDefault: false,
  //     // Select which mapbox-gl-draw control buttons to add to the map.
  //     controls: {
  //       polygon: true,
  //       trash: true,
  //     },
  //     // Set mapbox-gl-draw to draw by default.
  //     // The user does not have to click the polygon control button first.
  //     defaultMode: "draw_polygon",
  //   });
  //   map.addControl(draw);

  //   map.on("draw.create", updateArea);
  //   map.on("draw.delete", updateArea);
  //   map.on("draw.update", updateArea);

  //   function updateArea(e) {
  //     const data = draw.getAll();
  //     const answer = document.getElementById("calculated-area");
  //     if (data.features.length > 0) {
  //       const area = turf.area(data);
  //       // Restrict the area to 2 decimal points.
  //       const rounded_area = Math.round(area * 100) / 100;
  //       answer.innerHTML = `<p><strong>${rounded_area}</strong></p><p>square meters</p>`;
  //     } else {
  //       answer.innerHTML = "";
  //       if (e.type !== "draw.delete") alert("Click the map to draw a polygon.");
  //     }
  //   }
  //   // Geo Draw Polygon ends

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
  left: 600px;
  top: 0px;
  z-index: 1;
}
</style>
