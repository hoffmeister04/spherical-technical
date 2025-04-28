<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import { ref, onMounted } from 'vue'
import mapboxgl from 'mapbox-gl'

onMounted(() => {
  mapboxgl.accessToken = import.meta.env.VITE_TOKEN

  // initial map
  const map = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/streets-v12',
    center: [-82.879143, 40.021568],   // gahanna, ohio
    zoom: 12
  })

  // add a pin with description to the map
  map.on('click', (e) => {
    const pin = e.lngLat
    const description = prompt('Add a description for your pin:')

    if (description) {
      const marker = new mapboxgl.Marker()
        .setLngLat([pin.lng, pin.lat])
        .setPopup(new mapboxgl.Popup().setText(description))
        .addTo(map)
    }
  })
})
</script>

<template>
  <div id="app">
    <div id="map" class="map-container"></div>
  </div>
</template>

<style scoped>
html, body, #app, .map-container {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
}

.map-container {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%
}
</style>
