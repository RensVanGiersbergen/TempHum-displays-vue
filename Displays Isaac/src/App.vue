<script setup>
import TempHum from './components/TempHum.vue'
</script>

<template>
  <main>
    <!-- Change sensor to the topic the sensor publishes too
    (for example if the sensor publishes to /3/3/3/temperature, the value is 3/3/3) -->
    <TempHum sensor="3/30/7"/>
  </main>
</template>

<style>
body{
  background-color:#35495e;
}
</style>
