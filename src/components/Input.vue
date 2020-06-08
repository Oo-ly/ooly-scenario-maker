<template>
  <div class="form-group">
    <label :for="toSnakeCase(name)">{{ name }}</label>
    <input type="text" :id="toSnakeCase(name)" class="form-control" v-bind:value="value" v-on:input="$emit('input', $event.target.value)" />
  </div>
</template>

<script>
export default {
  name: 'Input',
  props: ['name', 'value'],
  watch: {
    value() {
      this.$emit('input', this.value);
    },
  },
  methods: {
    toSnakeCase(str) {
      return (
        str &&
        str
          .match(/[A-Z]{2,}(?=[A-Z][a-z]+[0-9]*|\b)|[A-Z]?[a-z]+[0-9]*|[A-Z]|[0-9]+/g)
          .map((x) => x.toLowerCase())
          .join('_')
      );
    },
  },
};
</script>
