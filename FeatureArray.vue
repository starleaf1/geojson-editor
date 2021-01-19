<template>
  <v-container fluid class="ma-0 pa-0">
  </v-container>
</template>

<script>
import FeatureEditor from './FeatureEditor'

export default {
  name: 'FeatureArray',

  components: {
    'feature-array': FeatureArray
  },

  props: {
    value: {
      type: Array,
      default: () => []
    }
  },

  data () {
    return ({
    })
  },

  computed: {
    emitValue () {
      return this.value
    }
  }

  watch: {
    value (v) {
      this.$
    }
  }
}
</script>
