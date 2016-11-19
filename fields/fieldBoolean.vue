<script>
  export default {
    template: `
     <input type="checkbox"
            v-model="value"
            v-on:input="update($event.target.value)"
            id="exampleInputEmail1"
            :maxlength="field.maxLength"
         		:list="field.list"
            :max="field.max"
            :min="field.min"
            :multiple="field.multiple"
            :name="field.inputName"
            :pattern="field.pattern"
            :placeholder="field.filedOption.placeholder"
            :readonly="field.readonly"
            :required="field.required"
		>
`,
    props: ['model', 'indexKey', 'field'],
    created(){
      // console.log(this.field);
    },

    computed: {
      value: {
        get: function () {
          return this.model[this.indexKey];
        },
        // setter
        set: function (newValue) {
          this.$set(this.model, this.indexKey, newValue);
        }
      }
    },
    methods: {
      update(val) {
        // console.log(val);
        this.$emit('input', val)
        this.$emit('modelUpdated')
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
