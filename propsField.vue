<template>
  <div class="el-tabs__inner">
    <div ref="fieldProperties">
      <div
        class="row"
        v-show="activeForm.hasOwnProperty('label')"
      >
        <div class="col">
          <q-field>
            <label class="label">Nome do Campo</label>
            <q-input
              hide-underline
              v-model="activeForm.label"
            ></q-input>
          </q-field>
        </div>
      </div>
      <hr v-show="activeForm.hasOwnProperty('label')">

      <!-- Valor Default -->
      <div
        class="row"
        v-if="mostrarInput"
      >
        <div class="col">
          <label class="label">Valor Padrão</label>
          <q-option-group
            type="radio"
            inline
            :style="{'margin-top': '5px'}"
            color="primary"
            v-model="activeForm.data.radio_value"
            :options=" !activeForm.hasOwnProperty('options') ?
              [{ label: 'Sim', value: true, color: 'positive' },
              { label: 'Não', value: false, color: 'negative' }] : activeForm.options "
          />
        </div>
      </div>
      <hr v-if="mostrarInput">

      <!-- Show only when 'isPlacehodlerVisible' key exist -->
      <div
        class="row"
        v-show="activeForm.hasOwnProperty('isPlaceholderVisible')"
      >
        <div class="col">
          <q-field label="Placeholder">
            <q-toggle v-model="activeForm.isPlaceholderVisible"></q-toggle>
          </q-field>
          <q-field>
            <q-input
              v-show="activeForm.isPlaceholderVisible"
              v-model="activeForm.placeholder"
            >
            </q-input>
          </q-field>
        </div>
      </div>
      <hr v-show="activeForm.hasOwnProperty('isPlaceholderVisible')">

      <div
        class="row"
        v-show="activeForm.hasOwnProperty('buttonText')"
      >
        <div class="col">
          <q-field label="Button text">
            <q-input v-model="activeForm.buttonText">
              {{activeForm.buttonText}}
            </q-input>
          </q-field>
        </div>
      </div>
      <hr v-show="activeForm.hasOwnProperty('buttonText')">

      <q-field
        label="Code view"
        v-show="activeForm.hasOwnProperty('fieldText')"
      >
        <q-input
          v-model="activeForm.fieldText"
          type="textarea"
          :rows="10"
        >
          {{activeForm.fieldText}}
        </q-input>
      </q-field>

      <!-- Alinihamento do campo -->
      <div
        class="row"
        v-show="activeForm.hasOwnProperty('alignText')"
      >
        <div class="col">
          <label> Alinhar Campo </label>
          <q-option-group
            type="radio"
            label="Alinhar Campo"
            inline
            color="primary"
            v-model="activeForm.alignText"
            :options="[
                { label: 'Direita', value: 'text-left' },
                { label: 'Centro', value: 'text-center'},
                { label: 'Esquerda', value: 'text-right'}
              ]"
          />
        </div>
      </div>
      <hr v-show="activeForm.hasOwnProperty('alignText')">

      <!-- Campo é obrigatório -->
      <div
        class="row"
        v-show="activeForm.hasOwnProperty('isRequired')"
      >
        <div class="col">
          <q-field label="Obrigatório">
            <q-toggle v-model="activeForm.isRequired"></q-toggle>
          </q-field>
        </div>
      </div>
      <hr v-show="activeForm.hasOwnProperty('isRequired')">

      <div
        class="row"
        v-show="activeForm.hasOwnProperty('isHelpBlockVisible')"
      >
        <div class="col">
          <q-field label="Texto de Ajuda">
            <q-toggle v-model="activeForm.isHelpBlockVisible"></q-toggle>
          </q-field>
          <q-field>
            <q-input
              hide-underline
              v-show="activeForm.isHelpBlockVisible"
              v-model="activeForm.helpBlockText"
            >
            </q-input>
          </q-field>
        </div>
      </div>
      <hr v-show="activeForm.hasOwnProperty('isHelpBlockVisible')">

      <div class="row">
        <div class="col-12">
          <label class="label">Opções</label>
          <ul>
            <li
              v-for="(item, index) in activeForm.options"
              :key="index"
              class="properties__optionslist"
            >

              <div class="row">
                <div class="col-xs-10 col-xl=10">
                  <q-input v-model="item.label"></q-input>
                </div>
                <div class="col-xs-2 col-xl-2">
                  <q-btn
                    icon="mdi-trash-can-outline"
                    flat
                    round
                    @click="deleteOption(activeForm.options, index)"
                    v-show="activeForm.options.length > 1 && index > 1"
                  >
                  </q-btn>
                </div>
              </div>
            </li>
          </ul>

          <div class="row justify-center">
            <div class="col-12">
              <q-btn
                class="full-width"
                icon="mdi-playlist-plus"
                color="primary"
                label="Nova Opção"
                @click="addOption(activeForm.options)"
              >
              </q-btn>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Properties',
  props: {
    activeForm: {
      type: [Object, Array],
      default: () => { }
    }
  },
  data () {
    return {
      labelPosition: 'top',
      fieldProperties: {},
      rules: {}
    }
  },
  computed: {
    mostrarInput () {
      const obj = this.activeForm
      if (obj.hasOwnProperty('data')) {
        return obj.data.hasOwnProperty('radio_value')
      } else {
        return false
      }
    }
  },
  methods: {
    deleteOption (option, index) {
      this.$delete(option, index)
    },

    addOption (option) {
      let count = option.length + 1
      option.push({
        label: 'Opcao ' + (count + Math.floor(Math.random() * 100)),
        value: Math.floor(Math.random() * 100) + 'op' + (count + Math.floor(Math.random() * 100))
      })
    }
  }
}
</script>

<style scoped>
.properties__optionslist {
  margin-bottom: 5px;
}
</style>
