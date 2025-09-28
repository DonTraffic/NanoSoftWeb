<template>
  <section class="companys">
    <!-- <div id="cases" class="listCompanys wrapper">
      <div class="listCompanys__header">
        <h2 class="listCompanys__header-title title-h2">Сделаем ваш бизнес реальным</h2>
        <p class="listCompanys__header-text text-big">Уже множество клиентов выбрали именно нас! <br> Дак почему ты ещё не выбрал ?</p>
      </div>

      <div 
        id="listCompanys"
        class="DTScroll slider listCompanys-slider listCompanys__slider"
        points='{"0":5, "1024": 4, "768": 3, "425": 1}'
        touche="true"
        anchor="true"
      >

        <div class="listCompanys-line listCompanys__line">
          <div 
            class="listCompanys-item listCompanys__item"
            v-for="(organization, key) in organizations" :key="key"
            @click="flyTo(organization.coords, key)"
          >
            <img
              class="listCompanys__item-img"
              :src="`/images/companys/${organization.name}.png`"
              :alt="'organization-' + organization.name"
            >
          </div>
        </div>
      </div>
    </div> -->


    <div class="mapCompanys">
      <div ref="mapContainer" class="mapCompanys__map"></div>

      <div class="mapCompanys-container">

        <div class="mapCompanys__block mapCompanys__block--top">
          <h2 class="mapCompanys__block-title title-h2">КОМПАНИИ, КОТОРЫЕ НАМ ДОВЕРЯЮТ</h2>
          
          <svg class="mapCompanys__block-corner corner corner-black corner-left corner-top-inside corner-rotate-180">
            <use xlink:href="@/assets/sprites/sprite.svg#corner"></use>
          </svg>

          <svg class="mapCompanys__block-corner corner corner-black corner-right-outside corner-bottom-outside corner-rotate-180">
            <use xlink:href="@/assets/sprites/sprite.svg#corner"></use>
          </svg>
        </div>

        <div class="mapCompanys__block mapCompanys__block--bottom">
          <h2 class="mapCompanys__block-title title-h2">потому что, у нас куча преимуществ</h2>

          <svg class="mapCompanys__block-corner corner corner-black corner-left corner-bottom corner-rotate-270">
            <use xlink:href="@/assets/sprites/sprite.svg#corner"></use>
          </svg>

          <svg class="mapCompanys__block-corner corner corner-black corner-right-outside corner-top corner-rotate-270">
            <use xlink:href="@/assets/sprites/sprite.svg#corner"></use>
          </svg>
        </div>

        <div 
          id="listCompanys"
          class="DTScroll slider listCompanys-slider listCompanys__slider"
          points='{"0":5}'
          direction="vertical"
          touche="true"
          anchor="true"
        >

          <svg class="listCompanys-corner corner corner-black corner-right corner-top-inside corner-rotate-90">
            <use xlink:href="@/assets/sprites/sprite.svg#corner"></use>
          </svg>

          <svg class="listCompanys-corner corner corner-black corner-right corner-bottom corner-rotate-0">
            <use xlink:href="@/assets/sprites/sprite.svg#corner"></use>
          </svg>

          <div class="listCompanys-line listCompanys__line">
            <div 
              class="listCompanys-item listCompanys__item"
              v-for="(organization, key) in organizations" :key="key"
              @click="flyTo(organization.coords, key)"
            >
              <img
                class="listCompanys__item-img"
                :src="`/images/companys/${organization.name}.png`"
                :alt="'organization-' + organization.name"
              >
            </div>
          </div>
        </div>

        <div v-show="selectedOrg" class="mapCompanys__popup">
          <div 
            id="mapCompanysPopup"
            class="DTScroll slider mapCompanysPopup-slider mapCompanys__popup-container" 
            direction="vertical"
            touche="true"
          >
            <div class="mapCompanysPopup-line mapCompanys__popup-content">
              <div class="mapCompanysPopup-item mapCompanys__popup-content-item">
                <p v-if="selectedOrg" class="mapCompanys__popup-content-item-title title-h3">{{ selectedOrg.titleCompany }}</p>
                
                <img
                  v-if="selectedOrg"
                  class="mapCompanys__popup-content-img"
                  :src="`/images/companys/${selectedOrg.name}.png`"
                  :alt="'marker-' + selectedOrg.name"
                >

                <p v-if="selectedOrg" class="mapCompanys__popup-content-text text-medium" v-html="selectedOrg.textCompany"></p>

                <br>

                <p v-if="selectedOrg" class="mapCompanys__popup-content-additionally-text title-h3">{{ selectedOrg.title }}</p>
                <p v-if="selectedOrg" class="mapCompanys__popup-content-additionally-text text-medium" v-html="selectedOrg.text"></p>
              </div>
            </div>
          </div>

          <button class="mapCompanys__popup-closed text-medium" @click="closePopup">Закрыть</button>
        </div>
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
import { DTScroll } from '@/assets/scripts/slider'

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
      scale: 1.25
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

  new MutationObserver((item) => {
    DTScroll.sliderUpdateDeep('listCompanys')
  }).observe(
    document.querySelector('#listCompanys').querySelector('.listCompanys__line'), 
    { childList: true, subtree: true }
  ); DTScroll.initScroll('listCompanys')
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

  setTimeout(() => {
    DTScroll.initScroll('mapCompanysPopup')
  }, 500);
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
  const zoom = 10
  const target = fromLonLat(coords)

  view.animate(
    { center: target, duration },
    { zoom, duration }
  )
}
</script>
