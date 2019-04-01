<template>
  <div id="map-wrap" style="height: 100%;">
    <no-ssr>
      <l-map
        ref="map"
        :zoom="6.5"
        :center.sync="center"
        style="z-index: 1;"
        :options="$vuetify.breakpoint.smAndDown ? { zoomControl: false } : ''"
      >
        <l-tile-layer
          url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"
        ></l-tile-layer>
        <v-icondefault :image-path="'/statics/leafletImages/'"></v-icondefault>
        <l-marker-cluster>
          <l-marker
            v-for="marker in markers"
            :key="marker.id"
            :lat-lng="marker.position"
          >
            <l-popup>
              <h1 class="title">{{ marker.city }}, {{ marker.state }}</h1>
              <h1 class="body-1">Impressions: {{ marker.impressions }}</h1>
            </l-popup>
          </l-marker>
        </l-marker-cluster>
      </l-map>
    </no-ssr>
  </div>
</template>

<script>
import jsonMarkers from '@/assets/data/results.json'
import { mapGetters } from 'vuex'
import 'leaflet/dist/leaflet.css'
import 'leaflet.markercluster/dist/MarkerCluster.Default.css'
import Vue2LeafletMarkerCluster from 'vue2-leaflet-markercluster'

export default {
  components: {
    'l-marker-cluster': Vue2LeafletMarkerCluster
  },
  data() {
    return {
      jsonMarkers,
      zoom: false,
      show: false,
      center: [29.959694, -90.054932]
    }
  },
  computed: {
    ...mapGetters({ markers: 'getMapMarkers' })
  },
  mounted() {
    this.$nextTick(() => {
      this.map = this.$refs.map.mapObject
    })
  }
}
</script>
