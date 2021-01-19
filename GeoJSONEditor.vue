<template>
  <v-container fluid>
    <v-row>
      <v-col cols="4" v-for="(feature, n) in featureArray" :key="`${n}${featureArrayKey[n]}`">
        <feature-editor v-model="featureArray[n]">
          <template #activator="{ on }">
            <v-card>
              <v-card-title>Lokasi &#35;{{n + 1}}</v-card-title>
              <v-card-actions class="d-flex justify-end">
                <v-btn text v-on="on">Edit</v-btn><v-btn @click="removeFeature(n)" text>Hapus</v-btn>
              </v-card-actions>
            </v-card>
          </template>
        </feature-editor>
      </v-col>
    </v-row>
    <v-row justify="center">
      <feature-editor :key="adderKey" @input="newFeature" @cancel="refreshAdderKey">
        <template #activator="{ on }">
          <v-btn color="accent" v-on="on"><v-icon left>mdi-plus-circle</v-icon>Tambah Lokasi</v-btn>
        </template>
      </feature-editor>
    </v-row>
  </v-container>
</template>

<script>
import FeatureEditor from './FeatureEditor.vue'

export default {
  name: 'GeoJSONEditor',

  components: {
    'feature-editor': FeatureEditor
  },

  props: {
    value: {
      type: String,
      default: () => JSON.stringify({
        type: "FeatureCollection",
        features: []
      })
    }
  },

  data () {
    return ({
      internalValue: JSON.parse(this.value),
      featureArray: JSON.parse(this.value).features,
      featureArrayKey: [],
      adderKey: null
    })
  },

  watch: {
    value () {
      this.refreshInternalValue()
    },
    featureArray () {
      this.refreshInternalValue ()
    }
  },

  mounted () {
    this.refreshAdderKey()
    this.$data.featureArrayKey = this.$data.featureArray.map(() => (new Date()).getTime())
  },

  methods: {
    refreshInternalValue () {
      this.$data.internalValue.features = this.$data.featureArray
    },

    refreshAdderKey () {
      this.$data.adderKey = (new Date()).getTime()
    },

    newFeature (feature) {
      console.log(feature)
      this.$data.featureArray.push(feature)
      this.$data.featureArrayKey.push((new Date()).getTime())
      this.refreshInternalValue()
      this.refreshAdderKey()
      this.emit()
    },

    removeFeature (n) {
      this.$data.featureArray.splice(n, 1)
      this.$data.featureArrayKey.splice(n, 1)
      this.refreshInternalValue()
      this.emit()
    },

    emit () {
      this.$emit('input', JSON.stringify(this.$data.internalValue))
    }
  }
}
</script>
