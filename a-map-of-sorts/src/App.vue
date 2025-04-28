<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import { ref, onMounted } from 'vue'
import mapboxgl from 'mapbox-gl'

// public access token for mapbox
mapboxgl.accessToken = import.meta.env.VITE_TOKEN

// initialize map and array of user's pins
const map = ref(null)
const pins = ref([])

onMounted(() => {
  // map in a box
  map.value = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/streets-v12',
    center: [-82.879143, 40.021568],   // gahanna, ohio
    zoom: 12
  })

  // display all added pins (local storage only)
  const addedPins = JSON.parse(localStorage.getItem('pins')) || []
  pins.value = addedPins
  pins.value.forEach(pin => {
    createMarker(pin)
  })

  // add a pin with description to the map
  map.value.on('click', (e) => {
    const pin = {
      lng: e.lngLat.lng,
      lat: e.lngLat.lat,
      description: prompt('Describe your pin')
    }

    if (pin.description) {
      pins.value.push(pin)  // add to array for reload
      localStorage.setItem('pins', JSON.stringify(pins.value))
      createMarker(pin)
    }
  })
})

// creates a marker given a pin. expects longitude, latitude, and description
function createMarker(pin) {
  const colors = ['#4169E1', '#FFCC00']  // color options for pins

  const marker = new mapboxgl.Marker({
    color: colors[Math.floor(Math.random() * colors.length)]
  }).setLngLat([pin.lng, pin.lat])
    .addTo(map.value)

  const popup = new mapboxgl.Popup({
    closeButton: false,
    closeOnClick: false
  }).setText(pin.description)

  // event listeners: description attached to a pin appears when user hovers over the pin
  marker.getElement().addEventListener('mouseenter', () => {
    popup.addTo(map.value)
    popup.setLngLat([pin.lng, pin.lat])
  })

  marker.getElement().addEventListener('mouseleave', () => {
    popup.remove()
  })
}

// removes all pins and reloads the page
function deletePins() {
  localStorage.removeItem('pins')
  location.reload()
}
</script>

<template>
  <div id="app">
    <button id="delete-pins" @click="deletePins">Clear Pins</button>
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
  left: 0;
  right: 0;
  width: 100%
}

#delete-pins {
  position: absolute;
  top: 10px;
  right: 10px;
  z-index: 1;
  padding: 8px 12px;
  background-color: #f34f4f;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

#delete-pins:hover {
  background-color: #f80e0e;
}
</style>
