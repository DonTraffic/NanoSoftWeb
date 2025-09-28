<template>
    <footer id="footer" class="footer">
        <div ref="mapContainer" class="footer__map"></div>

        <div class="footer__info-container">
            <div class="footer__info-wrapper wrapper">
                <div class="footer__info">
                    <h2 class="title-h3">Контакты</h2>

                    <p class="text-medium">ООО "НАНО СОФТ"</p>
                    <p class="text-small text-bodily">ИНН: 3500005965</p>
                    <p class="text-small text-bodily">КПП: 350001001</p>
                    <p class="text-small text-bodily">ОГРН: 1243500004000</p>
                    <br>
                    <p class="text-medium">Юридический адрес: </p>
                    <p class="text-small text-bodily">Вологодская область, г. Вологда, ул. Фрязиновская, д. 27А/16</p>
                    <br>
                    <p class="text-medium">Фактический адрес:</p>
                    <p class="text-small text-bodily">Вологодская область, г. Вологда, ул. Ленинградская, д. 136</p>
                    <br>
                    <p class="text-medium">Телефон:</p>
                    <a class="text-small text-bodily" href="tel:+78124071767">+7 (812) 407-17-67</a>
                    <p class="text-medium">Email:</p>
                    <a class="text-small text-bodily" href="mailto:nanosoft-1c@yandex.ru">nanosoft-1c@yandex.ru</a>
                </div>
            </div>
        </div>

        <div class="footer__signature">
            <p class="footer__signature-text">© 2025 Nano-Soft<br> Все права защищены</p>
        </div>
    </footer>
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
import { Icon, Style, Text, Fill, Stroke } from 'ol/style'

const mapContainer = ref(null)

onMounted(() => {
  // Координаты Вологды, ул. Ленинградская, д. 136
  const coords = fromLonLat([39.842717, 59.208751])

  // Создаем фичу (точку)
  const pointFeature = new Feature({
    geometry: new Point(coords)
  })

  // Стили: иконка + подпись
  pointFeature.setStyle(
    new Style({
      image: new Icon({
        anchor: [0.5, 1],
        src: '/images/markers/marker-map-icon.png',
        scale: 0.7
      }),
      text: new Text({
        text: 'Вологодская область,\nг. Вологда, ул. Ленинградская, д. 136',
        offsetY: -60, // смещение текста вверх
        font: 'bold 14px Arial',
        fill: new Fill({ color: '#000' }),
        stroke: new Stroke({ color: '#fff', width: 3 })
      })
    })
  )

  const vectorLayer = new VectorLayer({
    source: new VectorSource({
      features: [pointFeature]
    })
  })

  // Создаем карту
  new Map({
    target: mapContainer.value,
    layers: [
      new TileLayer({ source: new OSM() }),
      vectorLayer
    ],
    view: new View({
      center: coords,
      zoom: 16
    })
  })
})
</script>

<style scoped>
.map {
  width: 100%;
  height: 500px;
  border-radius: 12px;
  overflow: hidden;
}
</style>
