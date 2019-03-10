<template>
  <div>
    <div class="row">
      <div class="col">
        <pre :class="classTitle"> {{$attrs.currentField.label}} </pre>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'TextTitle',
  data () {
    return {
      value: null
    }
  },
  computed: {
    classTitle () {
      return 'q-title ' + this.$attrs.currentField.alignText
    }
  },
  props: {
    uid: [String, Number],
    label: String,
    type: {
      type: String,
      default: 'text'
    }
  }
}
</script>

<style scoped>
</style>
