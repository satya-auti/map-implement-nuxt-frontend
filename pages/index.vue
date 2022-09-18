<template>
  <!-- @loaded="onMapLoaded" -->
  <main class="w-screen h-screen">
    <v-map class="w-full h-full" :options="state.map" @loaded="getMapData">
      <!-- @loaded="styleView" -->
      <!-- @click="styleView" -->
      <select
        class="zIndex"
        id="styles"
        @click="styleView"
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
    </v-map>
  </main>
</template>

<script setup lang="ts">
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
// const mapView = reactive({
//   displayMap: "",
// });

const data = reactive({
  mapData: [],
  allMapDataPoints: [],
  mark: [],
  lat: 0,
  lon: 0,
  layerData: "",
  temp1: {},
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
  state.map.style = "mapbox://styles/mapbox/" + layerId;
  //   const options: any = document.getElementsByTagName("option");
  //   console.log("options ", options);

  //   let setMapStyle = "mapbox://styles/mapbox/" + layerId;
  console.log("map", map);
  //   data.temp1.setStyle("mapbox://styles/mapbox/" + layerId);
  //   map.setStyle("mapbox://styles/mapbox/" + layerId);
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

  // http://localhost:3030/map
  data.mapData = await $fetch("http://localhost:3030/map");
  console.log("all data", data.mapData);

  data.mapData.map((ele) => {
    let pointsData = {
      lat: ele.lat,
      lon: ele.lon,
    };
    data.allMapDataPoints.push(pointsData);
  });

  console.log("only points ", data.allMapDataPoints);

  console.log("MapData - ", data.mapData[0]);
  //   data.lon = data.mapData[0].geom.coordinates[1];
  //   data.lat = data.mapData[0].geom.coordinates[0];
  //   console.log(" lat ", data.lat);
  //   console.log(" lon ", data.lon);

  //   data.temp1 = map;

  mapMarker(map);
  styleView(map);
  //   const layerList = document.getElementById("styles");
  //   console.log("layer list ", layerList);
  //   const options: any = layerList.getElementsByTagName("option");
  //   console.log("options ", options);

  //   const demo1: any = document.getElementsByName("view");
  //   console.log("demo1 ", demo1);
  //   console.log("model data ", data.layerData);

  //   demo1.onclick = (dat) => {
  //     console.log("click on select" + dat);
  //   };
}

async function mapMarker(map: mapboxgl.Map) {
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
</style>
