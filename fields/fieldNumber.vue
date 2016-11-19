<script>
  export default {
    template: `
    <input type="number" v-model="value" v-on:input="update($event.target.value)" class="form-control"
           id="exampleInputEmail1">
`,
    props: ['model', 'indexKey'],

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
        console.log(val);
        this.$emit('input', val)
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
