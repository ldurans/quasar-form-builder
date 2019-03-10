<template>
  <div>
    <div class="row">
      <div class="col">
        <q-field
          :helper="$attrs.currentField.isHelpBlockVisible ? $attrs.currentField.helpBlockText: ''"
          error-label="Obrigatório"
          :error="errorCampo"
        >
          <label
            class="label"
            v-if="$attrs.currentField.label !== ''"
          > {{ $attrs.currentField.label }} </label>
          <q-option-group
            type="radio"
            :style="{'margin-top': '5px'}"
            color="primary"
            v-model="$attrs.currentField.data.radio_value"
            :options="[
              { label: 'Sim', value: true, color: 'positive' },
              { label: 'Não', value: false, color: 'negative' }
            ]"
          />
          <q-input
            v-show="$attrs.currentField.data.radio_value"
            hide-underline
            :placeholder="placeholder"
            v-model="$attrs.currentField.data.value"
            @input="campoObrigatorio"
            @blur="campoObrigatorio"
          />
        </q-field>
      </div>
    </div>
  </div>
</template>

<script>
import { required } from 'vuelidate/lib/validators/'

export default {
  name: 'radio-Obs',
  data () {
    return {
      errorCampo: false
    }
  },
  methods: {
    campoObrigatorio () {
      if (this.$attrs.currentField.isRequired && this.$attrs.currentField.data.radio_value) {
        if (this.$attrs.currentField.data.value == '' || this.$attrs.currentField.data.value == null) {
          this.errorCampo = true
        } else {
          this.errorCampo = false
        }
      }
    }
  },
  computed: {
    placeholder () {
      const data = this.$attrs.currentField
      if (data.isPlaceholderVisible) {
        return data.placeholder
      } else {
        return ''
      }
    }
  },
  // watch: {
  //   '$attrs.currentField.default' (v) {
  //     this.radio_value = v
  //   }
  // },
  props: {
    uid: [String, Number],
    label: String
  }
}
</script>

<style scoped>
</style>
