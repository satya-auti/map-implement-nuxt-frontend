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
      <!-- Directions css -->
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-directions/v4.1.0/mapbox-gl-directions.css"
        type="text/css"
      />
      <link
        href="https://api.mapbox.com/mapbox-gl-js/v2.10.0/mapbox-gl.css"
        rel="stylesheet"
      />
      <!--  -->
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
      </select>

      <!-- <input
          type="search"
          class="zIndex1"
          name="search"
          v-model="data.findString"
          placeholder="Search Location"
          @keyup="getSpecificMapData(data.findString)"
        /> -->
      <!-- <div class="zIndex2">
          <input type="file" @change="uploadCsvFile" />
          <button>Submit</button>
        </div> -->
    </v-map>
    <!-- Polygon draw calculate start -->
    <div class="calculation-box">
      <p>Click the map to draw a polygon.</p>
      <div id="calculated-area"></div>
    </div>
    <!-- polygon ends -->
  </main>
</template>

<script setup lang="ts">
// src="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.js "
// https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.min.js
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import MapboxDirections from "@mapbox/mapbox-gl-geocoder";
// import { MapboxDraw } from "@mapbox/mapbox-gl-geocoder";

// const mapView = reactive({
//   displayMap: "",
// });

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
  //   data.map1.setStyle("mapbox://styles/mapbox/" + layerId);
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

  // Geo Coder search start
  // Add the control to the map
  mapboxgl.accessToken = state.map.accessToken;
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

  //   Directions starts
  map.addControl(
    new MapboxDirections({
      accessToken:
        "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
    }),
    "top-right"
  );
  // directions Ends

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

  // live Location mark start

  map.on("style.load", () => {
    map.setFog({});
  });

  // map.on("load", async () => {
  // Get the initial location of the International Space Station (ISS).
  const geojson = await getLocation();
  // Add the ISS location as a source.
  map.addSource("iss", {
    type: "geojson",
    data: geojson,
  });
  // Add the rocket symbol layer to the map.
  map.addLayer({
    id: "iss",
    type: "symbol",
    source: "iss",
    layout: {
      // This icon is a part of the Mapbox Streets style.
      // To view all images available in a Mapbox style, open
      // the style in Mapbox Studio and click the "Images" tab.
      // To add a new image to the style at runtime see
      // https://docs.mapbox.com/mapbox-gl-js/example/add-image/
      // "icon-image": "cat",
      "icon-image": "rocket-15",
    },
  });

  // Update the source from the API every 2 seconds.
  const updateSource = setInterval(async () => {
    const geojson = await getLocation(updateSource);
    map.getSource("iss").setData(geojson);
  }, 2000);

  async function getLocation(updateSource) {
    // const response = await fetch(
    //   "https://api.wheretheiss.at/v1/satellites/25544",
    //   { method: "GET" }
    // );
    // "http://localhost:3040/map"
    // const { latitude, longitude } = await response.json();
    // console.log("live response", response);

    // Make a GET request to the API and return the location of the ISS.
    try {
      const response = await fetch(
        "https://api.wheretheiss.at/v1/satellites/25544",
        { method: "GET" }
      );
      // const response = await fetch("http://localhost:3040/map", {
      //   method: "GET",
      // });
      // const demoStore = await response.json();
      // console.log("store", demoStore);

      const { latitude, longitude } = await response.json();
      console.log("lat long", latitude, longitude);

      console.log("live response", response);

      // Fly the map to the location.
      map.flyTo({
        center: [longitude, latitude],
        speed: 0.5,
      });
      // Return the location of the ISS as GeoJSON.
      return {
        type: "FeatureCollection",
        features: [
          {
            type: "Feature",
            geometry: {
              type: "Point",
              coordinates: [longitude, latitude],
            },
          },
        ],
      };
    } catch (err) {
      // If the updateSource interval is defined, clear the interval to stop updating the source.
      if (updateSource) clearInterval(updateSource);
      throw new Error(err);
    }
  }
  // });
  // live location mark end

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
  // let lat = e.lngLat.lat;
  // let lon = e.lngLat.lng;
  // let lat = data.lat;
  // let lon = data.lon;
  // console.log("lat lon - " + lat, lon);

  data.allMapDataPoints.map((ele) => {
    console.log("ele123", ele);

    new mapboxgl.Marker({
      draggable: true,
      color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
    })
      //   .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .setLngLat([ele.lat, ele.lon])
      //   .setLngLat([74.04931277036667, 19.266912177018096])
      .addTo(map);
    map.on("mouseover", (e) => {
      new mapboxgl.Popup()
        .setLngLat([ele.lat, ele.lon])
        //   //   .setLngLat([data.lat, data.lon])
        .setHTML("values" + ele.lat + " , " + ele.lon)
        .addTo(map);
    });

    // new mapboxgl.Popup()
    //   .setLngLat([74.04931277036667, 19.266912177018096])
    //   //   .setLngLat([data.lat, data.lon])
    //   //   .setHTML("hello world")
    //   .addTo(map);
  });
  map.on("mouseout", (e) => {});
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
  //   // Geo Coder Ends

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

async function getSpecificMapData(find) {
  console.log("abc");
  if (find != null) {
    data.findMapData = data.mapData.filter((ele) => {
      let city1 = find.toLocaleLowerCase();
      let city2 = ele.city.toLocaleLowerCase();
      console.log("data find ", city1, city2);

      let name1 = find.toLocaleLowerCase();
      let name2 = ele.name.toLocaleLowerCase();

      if (name2.includes(name1) || city2.includes(city1)) {
        console.log("data Found - >", ele);
        return ele;
      }
    });
  }
  if (find == "") {
    data.findMapData = data.mapData;
  }
}

// async function uploadCsvFile(event) {
//   console.log("event", event);

//   let file = {
//     csv: event.target.files[0].name,
//   };
//   console.log("file", file);
//   let payload = {
//     csv: file,
//     // csv: event.target.files[0],
//   };

//   // http://localhost:3040/map/upload
//   let response = await $fetch("http://localhost:3040/map/upload", {
//     method: "POST",
//     body: file,
//   });

//   console.log("res", response);
// }
</script>
<!-- <script src="https://api.tiles.mapbox.com/mapbox-gl-js/v2.9.2/mapbox-gl.js"></script>
  <script src="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.min.js"></script> -->

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
