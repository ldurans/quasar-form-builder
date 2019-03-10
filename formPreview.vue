<template>
  <div>
    <div class="row q-mr-sm">
      <div
        class="col-xs-12 col-sm-8"
        ref="FormPreview"
      >
        <div
          v-for="(form, index) in forms"
          :key="index"
          v-bind="form"
        >
          <div>
            <component
              :is="form.fieldType"
              :currentField="form"
              :uid="gerarUID()"
            >
            </component>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'
import { uid } from 'quasar'
import draggable from 'vuedraggable'
import propsField from '../../components/ccFormBuilder/propsField'
import TextInput from '../../components/ccFormBuilder/textInputBuilder'
import RadioObs from '../../components/ccFormBuilder/radio-Obs'
import TextTitle from '../../components/ccFormBuilder/textTitle'
import RadioOptions from '../../components/ccFormBuilder/radio-options'

export default {
  name: 'FormPreview',
  props: {
    forms: {
      type: Array,
      required: true,
      default: () => []
    }
  },
  components: {
    draggable,
    TextInput,
    RadioObs,
    TextTitle,
    propsField,
    RadioOptions
  },

  methods: {
    gerarUID () {
      return uid()
    }
  }
}
</script>

<style>
</style>
