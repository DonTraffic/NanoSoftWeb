<template>
  <section class="mapCompany">
    <div ref="mapContainer" class="mapCompany__map"></div>

    <div class="mapCompany-container">

      <div class="mapCompany__block mapCompany__block--top">
        <h2 class="mapCompany__block-title title-h2">КОМПАНИИ, КОТОРЫЕ НАМ ДОВЕРЯЮТ</h2>
      </div>

      <div class="mapCompany__block mapCompany__block--bottom">
        <h2 class="mapCompany__block-title title-h2">потому что, у нас куча преимуществ</h2>
      </div>

      <!-- <div class="mapCompany__buttons">
        <button
          v-for="(org, key) in organizations"
          :key="key"
          class="mapCompany__buttons-btn"
          @click="flyTo(org.coords, key)"
        >
          {{ org.name }}
        </button>
      </div> -->

      <div v-if="selectedOrg" class="mapCompany__popup">
        <div class="mapCompany__popup-block mapCompany__popup-block-top title-h3">{{ selectedOrg.titleCompany }}</div>

        <div class="mapCompany__popup-content">
          <div class="mapCompany__popup-content-container">
            <div class="mapCompany__popup-content-company-shadow mapCompany__popup-content-company-shadow--top shadow shadow-top"></div>

            <div class="mapCompany__popup-content-company">
              <img 
                class="mapCompany__popup-content-company-img"
                :src="`/images/companys/${selectedOrg.name}.png`"
                :alt="'marker-' + selectedOrg.name"
              >

              <p class="mapCompany__popup-content-company-text text-medium" v-html="selectedOrg.textCompany"></p>
            </div>

            <div class="mapCompany__popup-content-company-shadow mapCompany__popup-content-company-shadow--bottom shadow shadow-bottom"></div>
          </div>

          <div class="mapCompany__popup-content-scroll">
            <div class="mapCompany__popup-content-scroll-thumb"></div>
          </div>

          <div class="mapCompany__popup-content-desc">
            <div class="mapCompany__popup-content-desc-shadow mapCompany__popup-content-desc-shadow--top shadow shadow-top"></div>

            <p class="mapCompany__popup-content-desc-title title-h3">{{ selectedOrg.title }}</p>
            <p class="mapCompany__popup-content-desc-text text-medium" v-html="selectedOrg.text"></p>

            <div class="mapCompany__popup-content-desc-shadow mapCompany__popup-content-desc-shadow--bottom shadow shadow-bottom"></div>
          </div>
        </div>

        <div class="mapCompany__popup-block mapCompany__popup-block-bottom text-big" @click="closePopup">Закрыть</div>
      </div>
    </div>
  </section>
</template>


<script setup>
import { onMounted, ref } from 'vue'
import 'ol/ol.css'
import Map from 'ol/Map'
import View from 'ol/View'
import TileLayer from 'ol/layer/Tile'
import OSM from 'ol/source/OSM'
import { fromLonLat } from 'ol/proj'
import Feature from 'ol/Feature'
import Point from 'ol/geom/Point'
import VectorSource from 'ol/source/Vector'
import VectorLayer from 'ol/layer/Vector'
import { Icon, Style } from 'ol/style'

import organizations from '@/assets/data/organizations.json'

const mapContainer = ref(null)
const selectedOrg = ref(null)
let map, view, vectorLayer
const featuresMap = {} 

// универсальный стиль маркера
function createMarkerStyle(src) {
  return new Style({
    image: new Icon({
      anchor: [0.5, 1],
      src,
      scale: 0.07
    })
  })
}

onMounted(() => {
  const features = Object.entries(organizations).map(([key, org]) => {
    const feature = new Feature({
      geometry: new Point(fromLonLat(org.coords)),
      orgKey: key
    })
    // обычная иконка = marker-map-{name}.png
    feature.setStyle(createMarkerStyle(`/images/markers/marker-map-${org.name}.png`))
    featuresMap[key] = feature
    return feature
  })

  vectorLayer = new VectorLayer({
    source: new VectorSource({ features })
  })

  view = new View({
    center: fromLonLat([39.8978, 59.2205]),
    zoom: 12
  })

  map = new Map({
    target: mapContainer.value,
    layers: [new TileLayer({ source: new OSM() }), vectorLayer],
    view
  })

  map.on('singleclick', evt => {
    map.forEachFeatureAtPixel(evt.pixel, feature => {
      const key = feature.get('orgKey')
      selectOrg(key)
    })
  })
})

function selectOrg(key) {
  // сброс предыдущего маркера
  if (selectedOrg.value) {
    featuresMap[selectedOrg.valueKey].setStyle(
      createMarkerStyle(`/images/markers/marker-map-${selectedOrg.value.name}.png`)
    )
  }

  // активный маркер = marker-map-icon.png
  selectedOrg.value = organizations[key]
  selectedOrg.valueKey = key
  featuresMap[key].setStyle(createMarkerStyle('/images/markers/marker-map-icon.png'))
}

function closePopup() {
  if (selectedOrg.value) {
    featuresMap[selectedOrg.valueKey].setStyle(
      createMarkerStyle(`/images/markers/marker-map-${selectedOrg.value.name}.png`)
    )
  }
  selectedOrg.value = null
}

function flyTo(coords, key) {
  selectOrg(key)
  const duration = 2000
  const zoom = 14
  const target = fromLonLat(coords)

  view.animate(
    { center: target, duration },
    { zoom, duration }
  )
}
</script>
