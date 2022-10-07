<template>
  <div>
    <button @click="loadFlightData">Get Data</button>
  </div>
</template>
<script setup lang="ts">
// Real-Time Flights

// https://api.aviationstack.com/v1/flights
// ? access_key = YOUR_ACCESS_KEY
// 35218282d38eeb94bbfe644b52b1d5c7
async function loadFlightData() {
  //   let data1 = await $fetch("https://airlabs.co/api/v9/ping.json");
  //   console.log("response", data1);
  // }
  let response = await $fetch(
    "http://api.aviationstack.com/v1/flights?access_key=35218282d38eeb94bbfe644b52b1d5c7"
  );
  console.log("response", response);
}
</script>
