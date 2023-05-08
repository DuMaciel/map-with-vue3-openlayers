<script setup lang="ts">
import { ref } from 'vue';

// default projection is EPSG:3857, but to use latitude and longitude in degrees the projection must be EPSG:4326.
const projection = ref('EPSG:4326');
// coordinates are represented by array: [longitude, latitude].
const center = ref([-53.711536836218585, -24.617732283197068]);
// zoom ranges from 0 to 28, with 28 being as close as possible by default.
const zoom = ref(8);
// the rotation is defined as a circle perimeter, so it goes from 0 to 2PI, to rotate 90 degrees put the rotation in PI/2.
const rotation = ref(0);

const markers = ref([
  {id: 1, name: 'Marker 1', lat: -24.526, lng: -53.985, color: '#ff2255'},
  {id: 2, name: 'Marker 2', lat: -24.352, lng: -54.200, color: '#2288ff'},
  {id: 3, name: 'Marker 3', lat: -24.589, lng: -53.698, color: '#22ff55'},
  {id: 4, name: 'Marker 4', lat: -24.825, lng: -53.428, color: '#ffff22'},
  {id: 5, name: 'Marker 5', lat: -24.985, lng: -53.222, color: '#ff22ff'},
])

const popUp = {
  position: ref([]),
  data: ref(''),
};

const atEveryMove = (evt: any) => {
  // every event emitted by the map contains a reference to it
  if (evt.map) {
    //"forEachFeatureAtPixel" is a map method that checks and returns if there is a Feature linked to the pixel passed as a parameter
    const feature = evt.map.forEachFeatureAtPixel(evt.pixel, (feat: any) => {
      return feat;
    });
    if (feature && feature.get('type') === 'Marker') {
      //"getGeometry()" returns the geometry linked to Feature, and "flatCoordinates" and the coordinate in the format we use
      popUp.position.value = feature.getGeometry().flatCoordinates;
      popUp.data.value = feature.get('name');
    } else {
      popUp.position.value = [];
      popUp.data.value = '';
    }
  }
};

</script>
<template>
    <ol-map
    @pointermove="atEveryMove"
    >
      <ol-view
        :projection="projection"
        :center="center"
        :zoom="zoom"
        :rotation="rotation"
      />
      <ol-tile-layer>
        <ol-source-osm />
      </ol-tile-layer>
      <ol-vector-layer>
        <ol-source-vector>
          <!-- add in ":properties" the data you want to retrieve by the "get('key')" method that the Feature object provides -->
          <ol-feature v-for="marker in markers"
          :key="marker.id"
          :properties="{ name: marker.name, type: 'Marker' }"
          >
            <ol-geom-point :coordinates="[marker.lng, marker.lat]"></ol-geom-point>
            <ol-style>
              <ol-style-icon
                src="src/assets/marker.svg"
                :color="marker.color"
                :scale="1"
              >
              </ol-style-icon>
            </ol-style>
          </ol-feature>
        </ol-source-vector>
      </ol-vector-layer>

      <ol-overlay
        v-show="popUp.position.value.length"
        :position="popUp.position.value"
      >
        <template v-slot>
          <div style="background-color: white; padding: 2px">
            <span>{{ popUp.data.value }}</span>
          </div>
        </template>
      </ol-overlay>

      <ol-attribution-control
        :collapsible="true"
        tipLabel="Atribuições"
      />
      <ol-zoom-control
        zoomInTipLabel="Aproximar"
        zoomOutTipLabel="Afastar"
      />
      <ol-scaleline-control />
      <ol-rotate-control 
      tipLabel="Redefinir rotação para o norte"/>
      <ol-fullscreen-control 
      tipLabel="Alterar modo de tela cheia"/>
      <ol-mouseposition-control/>
      <ol-overviewmap-control 
      tipLabel="Abrir mapa geral">
        <ol-tile-layer>
          <ol-source-osm />
        </ol-tile-layer>
      </ol-overviewmap-control>
    </ol-map>
</template>

