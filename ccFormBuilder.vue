<template>
  <div>
    <q-tabs
      animated
      swipeable
      inverted
      color="secondary"
      text-color="primary"
    >
      <q-tab
        style="border: 1px solid #ececec"
        default
        slot="title"
        label="Criar Formulário"
        name="formCreate"
      />
      <q-tab
        style="border: 1px solid #ececec"
        slot="title"
        label="Lista de Formulários"
        name="formList"
      />
      <q-tab-pane name="formCreate">
        <div class="row q-mr-sm">
          <div
            class="col-xs-12 col-sm-8"
            ref="FormBuilder"
          >
            <draggable
              :list="forms"
              class="dragArea"
              :options="sortElementOptions"
            >
              <!-- Form com os elementos -->
              <div
                v-for="(form, index) in forms"
                :key="index"
                v-bind="form"
                class="form__group"
                :class="{ 'is--active': form === activeForm  }"
              >

                <span class="form__selectedlabel">{{ form.fieldType }}</span>

                <div @click="editElementProperties(form)">
                  <component
                    :is="form.fieldType"
                    :currentField="form"
                    :uid="form.uuid = gerarUID()"
                    class="form__field"
                  >
                  </component>
                </div>

                <!-- Actions list -->
                <div class="form__actiongroup">
                  <q-btn
                    round
                    size="sm"
                    color="primary"
                    icon="mdi-pan-vertical"
                    class="form__actionitem--move"
                  ></q-btn>

                  <q-btn-group class="form__actionlist">
                    <q-btn
                      size="sm"
                      color="primary"
                      icon="mdi-plus"
                      @click="cloneElement(index, form)"
                      v-show="!form.isUnique"
                    ></q-btn>
                    <q-btn
                      size="sm"
                      color="primary"
                      icon="delete"
                      @click="deleteElement(index)"
                    ></q-btn>
                  </q-btn-group>
                </div>
              </div>
            </draggable>
          </div>
          <div class="col-xs-12 col-sm-4 q-pl-md ">
            <q-tabs
              animated
              swipeable
              inverted
              two-lines
              no-pane-border
              v-model="activeTabForFields"
              align="justify"
              style="border: 2px dotted #000"
            >
              <!-- Tabs - notice slot="title" -->
              <q-tab
                default
                slot="title"
                name="Elementos"
                label="Elementos"
                icon="mdi-group"
              />
              <q-tab
                slot="title"
                name="Propriedades"
                label="Atributos"
                icon="mdi-settings-outline"
              />
              <!-- Targets -->
              <q-tab-pane name="Elementos">
                <div class="row gutter-md">
                  <div class="col-12">
                    <draggable
                      :list="fields"
                      :options="dropElementOptions"
                      :clone="clone"
                      @start="onStart"
                    >
                      <q-btn
                        v-for="(field, index) in fields"
                        color="secondary"
                        :key="index"
                        class="button__sidebar q-mr-xs"
                        no-caps
                      >
                        {{field.text}}
                      </q-btn>
                    </draggable>
                  </div>
                </div>
              </q-tab-pane>
              <q-tab-pane name="Propriedades">
                <propsField :activeForm="activeForm"></propsField>
              </q-tab-pane>
            </q-tabs>
            <div class="row q-mt-sm justify-between">
              <div class="col">
                <q-btn
                  color="dark"
                  icon="pageview"
                  label="Visualizar"
                  @click="visualizarForm"
                ></q-btn>
              </div>
              <div class="col text-right">
                <q-btn
                  color="positive"
                  icon="check"
                  label="Salvar"
                  @click="confirmarNomeSalvarForm"
                ></q-btn>
              </div>
            </div>

          </div>
        </div>
      </q-tab-pane>
      <q-tab-pane name="formList">
        <div class="row justify-center">
          <div class="col-8">
            <q-field helper="Formulários já Cadastrados">
              <label class="label">Selecione o Formulário</label>
              <q-select
                v-model="formSelecionado"
                radio
                :options="formularios"
                @input="formularioSelecionado"
              />
            </q-field>

          </div>
          <div class="col">
            <q-btn
              style="margin-top: 24px;"
              color="secondary"
              icon="update"
              label="Atualizar"
              @click="listarForms"
            />
          </div>
        </div>
        <div class="row">
          <div class="col">
            <hr
              class="hrText q-ma-md"
              data-content="Visualização do Formulário"
            >
            <formPreview :forms="formulario"></formPreview>
          </div>
        </div>
      </q-tab-pane>
    </q-tabs>

    <div>
      <pre>
        <code>
          {{ forms }}
        </code>
      </pre>
    </div>

    <q-dialog
      v-model="salvarModal"
      prevent-close
    >
      <!-- This or use "title" prop on <q-dialog> -->
      <span slot="title">Qual o nome do Formulário?</span>
      <!-- This or use "message" prop on <q-dialog> -->
      <span
        slot="message"
        class="wrap bg-amber-2 shadow-2"
      >
        Uma vez que o formulário seja salvo, não poderá ser alterado.
      </span>

      <div slot="body">
        <q-input
          hide-underline
          v-model="st_nome_form"
        />
      </div>
      <template
        slot="buttons"
        slot-scope="props"
      >
        <q-btn
          :disabled="disablitarAcaoModal"
          color="info"
          label="Cancelar"
          @click="salvarModal=false"
        />
        <q-btn
          :disabled="disablitarAcaoModal"
          :loading="disablitarAcaoModal"
          color="warning"
          label="Confirmar"
          @click="salvarForm"
        />
      </template>
    </q-dialog>

  </div>
</template>

<script>
import _ from 'lodash'
import { uid } from 'quasar'
// import * as api from '../../service/sistema/formBuilder'
import draggable from 'vuedraggable'
import propsField from './propsField'
import formPreview from './formPreview'
import TextInput from './textInputBuilder'
import RadioObs from './radio-Obs'
import TextTitle from './textTitle'
import RadioOptions from './radio-options'

export default {
  name: 'ccPrescricao',
  data () {
    return {
      formSelecionado: null,
      formulario: [],
      listaForms: [],
      salvarModal: false,
      disablitarAcaoModal: false,
      id_usuario_criacao: this.$store.state.user.userInfo.id,
      id_empresa_sistema: this.$store.state.user.empresaSelecionada.id,
      st_nome_form: '',
      forms: [
        // {
        //   fieldType: '',
        //   helpBlockText: '',
        //   isHelpBlockVisible: false,
        //   isPlaceholderVisible: false,
        //   isRequired: false,
        //   isUnique: false,
        //   label: '',
        //   placeholder: ''
        // }
      ],
      activeTabForFields: 'Elementos',
      activeForm: '',
      fields: [{
        'name': 'TextInput',
        'text': 'Texto Simples',
        'group': 'form', // form group
        'hasOptions': false,
        'isRequired': false,
        'isHelpBlockVisible': false,
        'isPlaceholderVisible': true,
        'isUnique': false,
        'data': {
          'value': ''
        }
      },
      {
        'name': 'RadioObs',
        'text': 'Radio + Obs',
        'group': 'form', // form group
        'hasOptions': false,
        'isHelpBlockVisible': false,
        'isPlaceholderVisible': true,
        'isDefault': true,
        'data': {
          'value': '',
          'radio_value': true
        }
      },
      {
        'name': 'RadioOptions',
        'text': 'OpcoesRadio',
        'group': 'form', // form group
        'hasOptions': true,
        'notRequired': true,
        'isHelpBlockVisible': false,
        'isPlaceholderVisible': false,
        'isDefault': true,
        'data': {
          'radio_value': 'opcao1'
        }
      },
      {
        'name': 'TextTitle',
        'text': 'Título',
        'group': 'static'
      }
      ],
      sortElementOptions: {
        group: {
          name: 'formbuilder',
          pull: false,
          put: true
        },
        sort: true,
        handle: '.form__actionitem--move'
      },

      dropElementOptions: {
        group: {
          name: 'formbuilder',
          pull: 'clone',
          put: false
        },
        sort: false,
        ghostClass: 'sortable__ghost',
        filter: '.is-disabled'
      }
    }
  },

  components: {
    draggable,
    propsField,
    formPreview,
    TextInput,
    RadioObs,
    TextTitle,
    RadioOptions
  },
  computed: {
    formularios () {
      let data = this.listaForms
      return this.$util.renameItemSelect(data, '"st_nome_form":', '"id":')
    }
  },
  methods: {
    clone (field) {
      var newField = {
        fieldType: field.name,
        isUnique: field.isUnique
      }
      if (field.data) {
        newField['data'] = field.data
      }
      // Show placeholder
      if (field.isPlaceholderVisible) {
        newField['isPlaceholderVisible'] = false
        newField['placeholder'] = 'Informe o texto aqui...'
      }
      // Decide whether display label, required field, helpblock
      if (field.group == 'form') {
        newField['label'] = 'Nome do Campo/Pergunta'
        newField['isHelpBlockVisible'] = false
        newField['helpBlockText'] = 'Favor inserir o texto de apoio aqui...'
        newField['isRequired'] = false
      }
      if (field.notRequired) {
        delete newField.isRequired
      }
      if (field.group == 'button') {
        newField['buttonText'] = 'Enviar'
      }
      if (field.name == 'TextEditor') {
        newField['fieldText'] = 'Informe o tipo'
      }

      if (field.group === 'static') {
        newField['label'] = 'Nome do Campo/Pergunta/Título'
        newField['alignText'] = 'left'
      }
      // Add dummy options for loading the radio/checkbox
      if (field.hasOptions) {
        newField['options'] = [{
          label: 'Opção 1',
          value: 'opcao1'
        },
        {
          label: 'Opção 2',
          value: 'opcao2'
        }
        ]
      }
      return newField
    },
    onStart () { },
    checkStopDragCondition (field) {
      var form = this.forms,
        formArray = []
      for (var key in form) {
        formArray.push(form[key]['fieldType'])
      }
      // Check if the fieldname exist in formArray
      // And when the field.isUnique too
      return _.includes(formArray, field.name) && field.isUnique
    },
    deleteElement (index) {
      this.activeForm = []
      this.$delete(this.forms, index)
      this.activeTabForFields = 'Elementos'
    },

    cloneElement (index, form) {
      var cloned = _.cloneDeep(form) // clone deep lodash
      this.forms.splice(index, 0, cloned)
    },

    editElementProperties (form) {
      this.activeForm = form
      this.activeTabForFields = 'Propriedades'
    },
    confirmarNomeSalvarForm () {
      this.salvarModal = true
    },
    salvarForm () {
      this.disablitarAcaoModal = true
      let data = {}
      data.json_form = [...this.forms]
      data.id_usuario_criacao = this.id_usuario_criacao
      data.id_empresa_sistema = this.id_empresa_sistema
      data.st_nome_form = this.st_nome_form
      data.bl_compartilhar = true
      data.cd_especialidade = null

      api.cadastrarFormulario(data)
        .then(res => console.log(res.data))
        .catch(err => {
          this.$toast.error(`<p>${JSON.stringify(err.response.data)}</p>`,
            'Ops! Encontramos algum erro...'
          )
        })
        .finally(f => {
          this.disablitarAcaoModal = false
        })
    },
    listarForms () {
      let data = {}
      data.id_usuario_criacao = this.id_usuario_criacao
      data.id_empresa_sistema = this.id_empresa_sistema
      api.listarFormularios(data)
        .then(res => {
          console.log(res.data)
          this.listaForms = res.data
        })
    },
    visualizarForm () {
      console.log('visualizarForm')
    },
    gerarUID () {
      return uid()
    },
    formularioSelecionado () {
      this.formulario = []
      const data = this.listaForms.filter((obj) => {
        console.log('filtro', obj, obj.id == this.formSelecionado, this.formSelecionado)
        return obj.id == this.formSelecionado
      })
      if (data.length > 0) {
        this.formulario = data[0].json_form
      } else {
        this.formulario = []
      }
      // .hasOwnProperty('json_form') ? data[0].json_form : []
    }
  },

  mounted () {
    this.listarForms()
  }
}
</script>

<style>
.button__sidebar {
  width: 48%;
  margin-bottom: 10px;
}
</style>
