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

</script>
<template>
    <ol-map
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

