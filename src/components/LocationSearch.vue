<script setup lang="ts">
import ChartA from './ChartA.vue'
import { ref } from 'vue'

const name = ref('Berlin')
const count = ref(10)
const format = 'json'
const results: any = ref([])
const longit = ref(13.41053)
const latit = ref(52.52437)
const weather: any = ref([])
const today = new Date().toISOString().split('T')[0]
const startD = ref(today)
const endD = ref(today)

const url = 'https://geocoding-api.open-meteo.com/v1/search'
function getRresults() {
  const gnURL = `${url}?name=${name.value}&count=${count.value}&format=${format}`
  fetch(gnURL).then((response) => {
    response.json().then((data) => {
      results.value = data.results
      latit.value = data.results[0].latitude
      longit.value = data.results[0].longitude
    })
  })
}
const urlw = 'https://api.open-meteo.com/v1/forecast'
function getWather() {
  weather.value = []
  const gnURLw = `${urlw}?latitude=${latit.value}&longitude=${longit.value}&hourly=temperature_2m&start_date=${startD.value}&end_date=${endD.value}`
  fetch(gnURLw).then((response) => {
    response.json().then((wdata) => {
      weather.value = wdata.hourly
    })
  })
}
</script>
<template>
  <div class="dates">
    <div class="inputs">
      <fieldset>
        <legend>Start Date</legend>
        <input
          type="date"
          class="dateinp"
          step="0.000001"
          :max="endD"
          name="start"
          id="start"
          v-model="startD"
        />
      </fieldset>
      <fieldset>
        <legend>End Date</legend>
        <input
          type="date"
          class="dateinp"
          name="end"
          id="end"
          :max="today"
          :min="startD"
          v-model="endD"
        />
      </fieldset>
    </div>
  </div>
  <ChartA :temperatures="weather.temperature_2m" :time="weather.time" v-if="weather.time" />
  <br />
  <div class="geo-location">
    <div class="selection-panel">
      City: <input type="text" v-model="name" /> results:
      <select name="count" id="count" v-model="count">
        <option value="1">1</option>
        <option value="10">10</option>
        <option value="15">15</option>
        <option value="20">20</option>
      </select>
      <button @click="getRresults">Show Location</button>
      longitude:
      <input type="number" v-model="longit" step="0.00001" min="-180" max="180" name="longitude" />
      latitude:
      <input type="number" v-model="latit" step="0.00001" min="-90" max="90" name="latitude" />
      <button @click="getWather">Show Weather Forecast</button>
    </div>
    <div class="header">
      <div class="rName">Name</div>
      <div class="rCountry">Country</div>
      <div class="rLatitude">Latitude</div>
      <div class="rLongitude">Longitude</div>
    </div>
    <div
      v-for="result in results"
      :key="result.id"
      @click="
        () => {
          latit = result.latitude
          longit = result.longitude
          weather = []
          getWather()
        }
      "
    >
      <div class="row" :class="{ isActive: latit == result.latitude }">
        <div class="rName">{{ result.name }}</div>
        <div class="rCountry">{{ result.country }}</div>
        <div class="rLatitude">{{ result.latitude }}</div>
        <div class="rLongitude">{{ result.longitude }}</div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.row,
.header {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  border: 1px solid darkgrey;
  margin: 2px 0;
  text-align: center;
  cursor: pointer;
}
.row:hover {
  border: 1px solid rgb(35, 91, 137);
}
.row:nth-child(2n + 1) {
  background-color: rgba(194, 201, 208, 0.15);
}
.row.isActive {
  background-color: rgba(255, 255, 255, 0.5);
  border: 3px solid darkcyan;
  color: black;
}
input,
select,
option {
  background-color: var(--color-background);
  border: 1px solid var(--color-border);
  border-radius: 0.4rem;
  outline: none;
  color: var(--color-text);
  user-select: all;
  text-rendering: auto;
  cursor: pointer;
  padding: 0.5rem;
  font-size: 1rem;
  margin: 0.5rem;
  width: 120px;
}
input:active,
input:focus {
  border: 1px solid rgb(170, 170, 170);
  box-shadow: 0px 0px 10px 1px rgba(0, 157, 255, 0.7);
}
button {
  background-color: rgba(0, 157, 255, 0.15);
  border: 1px solid var(--color-border);
  border-radius: 0.3rem;
  outline: none;
  color: var(--color-text);
  cursor: pointer;
  padding: 0.5rem;
  font-size: 1rem;
  margin: 0.5rem;
}
button:hover,
input:hover,
select:hover {
  background-color: var(--color-background-mute);
  box-shadow: 0px 0px 2px 2px darkcyan;
}
.dateinp {
  background-color: var(--color-background);
  border: 1px solid var(--color-border);
  border-radius: 1.4rem;
  outline: none;
  color: var(--color-text);

  text-rendering: auto;
  font-size: 1rem;
  width: fit-content;
  color: var(--color-text);
  text-align: center;
}

.dateinp:active,
.dateinp:focus {
  border: 1px solid var(--color-border-hover);
  box-shadow: 0px 0px 5px 1px rgba(108, 125, 156, 0.8);
}
.inputs {
  position: relative;
  display: flex;
}
.selection-panel {
  display: flex;
  align-items: center;
  justify-content: space-evenly;
  margin-bottom: 1rem;
}
fieldset {
  border: 1px solid darkcyan;
  border-radius: 0.5rem;
  width: fit-content;
  margin: 0 1rem;
}
legend {
  padding: 0 2rem;
}
</style>
