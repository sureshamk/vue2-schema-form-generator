<template>
  <div>
    <fieldset v-if="schema != null" class="vue-form-generator">
      <template v-for="(field,index) in former.properties">

        <div class="form-group" :class="{'has-error':hasError(index) , 'has-feedback':hasError(index)}">
          <label>{{ field.title || index}}</label>
          <div class="field-wrap">
            <component :is="getFieldType(field)" :model='model' :field="field" :indexKey='index'
                       @modelUpdated='modelUpdated'>
            </component>
            <span v-if="hasError(index)" class="glyphicon glyphicon-remove form-control-feedback"></span>
            <div v-if="hasError(index)" class="help">
              <span class="help-block">{{errors[index][0]}}</span>
            </div>
            <div v-else="field.description" class="help">
              <span class="help-block">{{field.description}}</span>
            </div>
            <div v-if="field.buttons && field.buttons.length > 0" class="buttons">
              <button v-for="btn in field.buttons" @click="btn.onclick(model, field)" :class="btn.classes">
                {{ btn.label }}
              </button>
            </div>
          </div>
          <div v-if="field.hint" class="hint">{{ field.hint }}</div>
          <div v-if="field.errors && field.errors.length &gt; 0" class="errors">
            <span v-for="error in field.errors" track-by="$index">{{ error }}</span>
          </div>
        </div>
      </template>
    </fieldset>
  </div>
</template>

<script>
  import _ from "lodash";
  var validate = require("validate.js");
  // Load all fields from '../fields' folder
  let Fields = require.context("./fields/");
  let fieldComponents = {};
  _.each(Fields.keys(), (key) => {
    let compName = _.upperFirst(key.replace(/^\.\//, "").replace(/\.vue/, ""));
    // console.log(compName);
    fieldComponents[compName] = Fields(key);
  });
  console.log(fieldComponents);

  export default {
    components: fieldComponents,
    props: {
      schema: Object,
      model: Object,
      formOptions: Array,
      noValidate: Boolean,
      liveValidate: Boolean
    },
    data () {
      return {
        schemaForm: {},
        validateConstrains: {
          firstName: {
            presence: true,
            length: {
              minimum: 6,
              message: "must be at least 6 characters"
            }
          },
          lastName: {
            presence: true,
            email: true,
            length: {
              minimum: 6,
              maximum: 10
            }
          },
        },
        errors: {}
      }
    },
    created: function () {
      console.log(this.schema)
    },
    computed: {
      former(){
        let that = this;
        that.schemaForm['properties'] = {};
        if (!_.isUndefined(this.formOptions)) {
          _.each(this.formOptions, function (obj) {
            if (_.has(that.schema, "properties." + obj.key) || _.has(that.schema, "properties." + obj)) {
              if (_.isObject(obj)) {
                if (!_.isUndefined(obj.key)) {
                  that.schemaForm['properties'][obj.key] = _.get(that.schema, "properties." + obj.key);
                  that.schemaForm['properties'][obj.key]['filedOption'] = obj;
                }
              } else {
                that.schemaForm['properties'][obj] = _.get(that.schema, "properties." + obj);
                that.schemaForm['properties'][obj]['filedOption'] = '';
              }
            }
          });
          return that.schemaForm;
        } else {
          _.each(this.schema['properties'], function (obj, index) {
            obj['filedOption'] = '';
            that.schemaForm['properties'][index] = obj;
          });
          return that.schemaForm;
        }

      }
    },
    methods: {
      hasError(index){
        return !!(_.has(this.errors, index) && this.errors[index].length > 0);
      },
      closeMyself () {
        this.$emit('on-close')
      },
      getFieldType(field){
        if (field.type == 'string') {
          return "field-text";
        }
        if (field.type == 'integer' || field.type == 'number') {
          return "field-number";
        }
        if (field.type == 'boolean') {
          return "field-boolean";
        }
      },
      modelUpdated(){
        this.errors = validate(this.model, this.validateConstrains);
        //  console.warn(this.errors);
      },
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .form-control-feedback {
    top: 26px;
  }
</style>
