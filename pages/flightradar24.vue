<template>
  <div>
    Hello
    <button @click="loadFlightData">Get Data</button>
  </div>
</template>
<script setup lang="ts">
import { request } from "http";
import radar from "flightradar24-client/lib/radar";
import flight from "flightradar24-client/lib/flight";

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

  // const radar = require("flightradar24-client/lib/radar");

  let radar1 = radar(53, 13, 52, 14)
    .then((res) => console.log("work", res))
    .catch(console.error);

  console.log("radar", radar1);

  // const flight = require("flightradar24-client/lib/flight");

  let fly = flight("e3147c6").then(console.log).catch(console.error);
  console.log("fly", fly);
}
</script>
