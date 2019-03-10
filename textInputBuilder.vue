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
          >{{ $attrs.currentField.label }}</label>
          <q-input
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
  name: 'TextInput',
  data () {
    return {
      errorCampo: false,
      value: ''
    }
  },
  props: {
    uid: [String, Number],
    label: String,
    type: {
      type: String,
      default: 'text'
    }
  },
  methods: {
    campoObrigatorio () {
      if (this.$attrs.currentField.isRequired) {
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
  mounted () {
    console.log('TesteComp', this.$data)
  }
}
</script>

<style scoped>
</style>
