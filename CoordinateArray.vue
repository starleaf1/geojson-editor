<template>
  <v-list>
    <div v-if="geometryType === 'Point'">
      <v-list-item>
        <coordinate-input v-model="emitValue" />
      </v-list-item>
    </div>
    <div v-else>
      <v-list-item v-for="(coordinate, n) in emitValue" :key="`${n}${objectTracker[n]}`">
        <v-list-item-content>
          <coordinate-input v-model="emitValue[n]"  />
        </v-list-item-content>
        <v-list-item-action>
          <v-btn :disabled="emitValue.length <= (geometryType === 'LineString' ? 2 : 4)" @click="removeCoordinate(n)" icon><v-icon>mdi-delete</v-icon></v-btn>
        </v-list-item-action>
      </v-list-item>
      <div class="text-center">
        <v-btn @click="newPoint" rounded color="accent" large>
          <v-icon left>mdi-plus-circle-outline</v-icon>Titik Baru
        </v-btn>
      </div>
    </div>
  </v-list>
</template>

<script>
import CoordinateInputVue from './CoordinateInput.vue'

export default {
  name: 'CoordinateArray',

  props: {
    value: {
      type: Array,
      default: () => []
    },
    'geometry-type': {
      type: String,
      default: () => 'Point'
    }
  },

  components: {
    'coordinate-input': CoordinateInputVue
  },

  data () {
    return ({
      emitValue: this.value,
      objectTracker: this.geometryType !== 'Point' ? [] : null,
      // emitValue: this.value
    })
  },

  mounted () {
    if (this.geometryType !== 'Point') this.$data.objectTracker = this.$data.emitValue.map( () => Math.random() )
  },

  methods: {
    removeCoordinate (n) {
      this.$data.emitValue.splice(n, 1)
      this.$data.objectTracker.splice(n, 1)
      this.handleInput()
    },
    handleInput () {
      this.$emit('input', this.emitValue)
    },
    newPoint () {
      this.$data.emitValue.push([0, 0])
      this.$data.objectTracker.push((new Date).getTime())
      this.handleInput()
    }
  }
}
</script>
