<template>
  <v-dialog v-model="dialogOpen">
    <template #activator="slotProps">
      <slot name="activator" v-bind="slotProps">
      </slot>
    </template>
    <v-card width="100%" height="100%">
      <v-card-title>Edit Lokasi</v-card-title>
      <v-card-text>
        <div style="height: 100%" class="d-flex align-stretch">
          <div style="width: 30%; height: 100%; x-overflow: scroll">
            <div class="d-flex justify-center"><geometry-picker v-model="geometryType" /></div>
            <coordinate-array
              :geometry-type="emitValue.geometry.type"
              v-model="coordinates"
              @input="handleChanges"
              :key="`FeatureEditor${coordinateArrayKey}`"
            />
          </div>
          <div style="width: 70%">
            <l-map id="myMap" ref="map" @ready="resetMap"
              :center="center"
              :zoom="zoom"
              :options="{
                zoomControl: true,
                attributionControl: true
              }"
            >
              <l-tile-layer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />
              <l-geojson :geojson="emitValue" />
            </l-map>
          </div>
        </div>
      </v-card-text>
      <v-card-actions>
        <v-btn @click="ok" :disabled="!validFeature" color="primary">OK</v-btn>
        <v-btn @click="cancel">Cancel</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import { LMap, LTileLayer, LGeoJson } from 'vue2-leaflet'
import { Icon } from 'leaflet'

import 'leaflet/dist/leaflet.css'
delete Icon.Default.prototype._getIconUrl

Icon.Default.mergeOptions({
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
})

import GeometryPickerVue from './GeometryPicker.vue'
import CoordinateArrayVue from './CoordinateArray.vue'

export default {
  name: 'FeatureEditor',

  components: {
    'l-map': LMap,
    'l-tile-layer': LTileLayer,
    // 'l-marker': LMarker,
    // 'l-polyline': LPolyline,
    // 'l-polygon': LPolygon,
    'l-geojson': LGeoJson,

    'geometry-picker': GeometryPickerVue,
    'coordinate-array': CoordinateArrayVue
  },

  props: {
    value: {
      type: Object,
      default: () => ({
        type: "Feature",
        geometry: {
          type: "Point",
          coordinates: [124.841261, 1.493056]
        },
        properties: {}
      })
    },
    center: {
      type: Array,
      default: () => ([1.493056, 124.841261])
    },
    zoom: {
      type: Number,
      default: () => 12
    }
  },

  computed: {
    validFeature () {
      var valid = true
      // function validPoint (x) {
      //   if (x === null || x === undefined) return false
      //   if (x.constructor !== Array && x.constructor !== Object) return false
      //   if (x.constructor === Array) {
      //     if (x.length > 2) return false
      //     if (x[0] > 180) return false
      //     if (x[1] > 90) return false
      //   }
      // }

      if (this.$data.geometryType === 'LineString' && this.$data.coordinates.length < 2) valid = false
      if (this.$data.geometryType === 'Polygon' && this.$data.coordinates.length < 4) valid = false

      return valid
    }
  },

  data () {
    return ({
      dialogOpen: false,
      defaultCoordinates: [124.841261, 1.493056],
      coordinateArrayKey: (new Date()).getTime(),

      coordinates: this.value.geometry.type === 'Polygon' ? this.value.geometry.coordinates[0] : this.value.geometry.coordinates,
      geometryType: this.value.geometry.type,

      emitValue: this.value
    })
  },

  // mounted () {
  //   console.log('Mounted')
  //   window.setTimeout(() => {
  //     this.$nextTick(() => {
  //       this.$refs.map.mapObject.invalidateSize()
  //     })
  //   }, 100)
  // },

  watch: {
    geometryType (n) {
      this.$data.emitValue.geometry.type = n
      var o
      if (n === 'Point') {
        o = [124.841261, 1.493056]
      } else if (n === 'LineString') {
        o = [[124.841261, 1.493056], [124.801261, 1.413056]]
      } else if (n === 'Polygon') {
        o = [[124.841261, 1.493056], [124.801261, 1.433056], [124.811261, 1.423056], [124.841261, 1.493056]]
      }
      this.$data.coordinateArrayKey = (new Date()).getTime()
      this.$data.coordinates = o
      this.handleChanges()
    }
  },

  methods: {
    resetMap () {
      this.$nextTick(() => {
        this.$refs.map.mapObject.invalidateSize()
      })
    },
    convertCoordinatesArray () {
      var o = this.$data.coordinates
      // var geometryType = this.$data.geometryType
      // if (geometryType === 'Polygon') {
      //   o.push(o[0])
      //   o = [o]
      // }
      if (this.$data.geometryType === 'Polygon') o = [o]
      return o
    },
    handleChanges () {
      this.$nextTick(() => {
        this.$refs.map.mapObject.invalidateSize()
      })
      this.$data.emitValue.geometry.coordinates = this.convertCoordinatesArray()
      // this.$emit('input', this.$data.emitValue)
    },
    ok () {
      this.$emit('input', this.$data.emitValue)
      this.$data.dialogOpen = false
    },

    cancel () {
      this.$emit('cancel')
      this.$data.dialogOpen = false
    }
  }
}
</script>
