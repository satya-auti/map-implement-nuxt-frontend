<template>
  <div>
    Hello
    <button @click="loadFlightData">Get Data</button>
  </div>
</template>
<script setup lang="ts">
import { request } from "http";
// https://airlabs.co/api/v9/ping?
// https://airlabs.co/api/v9/ping?api_key=e0a94830-8b1c-4f9e-a57d-65e44fd1a2a5

// const request = require("request");
// const api_key = "e0a94830-8b1c-4f9e-a57d-65e44fd1a2a5";
// const api_base = "https://airlabs.co/api/v9/";

// function apiCall(method, params, cb) {
//   params.api_key = api_key;
//   request.post({ url: `${api_base}${method}`, form: params }, cb);
// }

// apiCall("ping", { param1: "value1" }, (err, res) => {
//   console.log(res);
// });

async function loadFlightData() {
  //   let data1 = await $fetch("https://airlabs.co/api/v9/ping.json");
  //   console.log("response", data1);
  // }
  let response = await $fetch(
    "https://airlabs.co/api/v9/ping.json?api_key=e0a94830-8b1c-4f9e-a57d-65e44fd1a2a5"
  );
  console.log("response", response);
}
</script>
