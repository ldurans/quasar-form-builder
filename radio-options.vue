<template>
  <div>
    <div class="row">
      <div class="col">
        <q-field :helper="$attrs.currentField.isHelpBlockVisible ? $attrs.currentField.helpBlockText: ''">
          <label
            class="label"
            v-if="$attrs.currentField.label !== ''"
          > {{ $attrs.currentField.label }} </label>
          <q-option-group
            type="radio"
            inline
            :style="{'margin-top': '5px'}"
            color="primary"
            v-model="$attrs.currentField.data.radio_value"
            :options="$attrs.currentField.options"
          ></q-option-group>
        </q-field>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'radio-options',
  data () {
    return {
    }
  }
}
</script>

<style scoped>
</style>
