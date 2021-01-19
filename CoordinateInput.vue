<template>
  <div class="d-flex">
    <v-text-field :readonly="readonly" type="number" step="any" label="Lintang" :value="emitValue[1]" @input="changeHandler(1, $event)" />
    <div style="width: 1em" />
    <v-text-field :readonly="readonly" type="number" step="any" label="Bujur" :value="emitValue[0]" @input="changeHandler(0, $event)" />
  </div>
</template>

<script>
export default {
  name: 'CoordinateInput',

  props: {
    value: {
      type: Array,
      default: () => [0, 0]
    },
    readonly: {
      type: Boolean,
      default: () => false
    }
  },

  data () {
    return ({
      emitValue: this.value
    })
  },

  methods: {
    changeHandler(where, v) {
      var c = this.$data.emitValue
      c[where] = parseFloat(v)
      this.$data.emitValue = c
      this.$emit('input', this.$data.emitValue)
    }
  }
}
</script>