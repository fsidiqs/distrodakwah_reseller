<template>
  <select
    v-bind:name="name"
    v-bind:id="idName"
    v-bind:class="className"
    v-on:change="onChangeCallback"
    v-model="fieldValue"
    v-bind:required="required"
    class="select"
  >
    <option :value="item.value" v-for="item in items" :key="item.value">{{item.text}}</option>
  </select>
</template>

<script>
export default {
  props: {
    name: String,
    idName: String,
    className: String,
    value: Number,
    values: Array,
    options: Array,
    onChange: { type: Function, default: null },
    required: { type: Boolean, default: false }
  },
  data() {
    return {
      items: [],
      fieldValue: this.value
    };
  },
  created() {
    this.processItems();
  },
  watch: {
    values: function(newVal, oldVal) {
      this.processItems();
    },
    value: function(newVal, oldVal) {
      this.fieldValue = this.value;
    }
  },
  methods: {
    processItems() {
      this.items = [];
      // console.log(this.values, this.options, this.name);
      for (let index = 0; index < this.values.length; index++) {
        this.items.push({
          value: this.values[index],
          text: this.options[index]
        });
      }
    },
    onChangeCallback() {
      if (this.onChange != null) {
        this.onChange(this.fieldValue);
      }
    }
  }
};
</script>

<style>
.select {
  background-color: #f5f5f5;
  border-radius: 5px;
  border: 1px solid #f1f1f1;
  padding: 0.5rem 1rem;
  color: #333;
  position: relative;
  cursor: pointer;
}
</style>