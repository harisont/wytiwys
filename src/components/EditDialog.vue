<template>
  <div v-if="dialog" style="display: inline-block">
    <label for="">New value: </label>
    <input
      ref="input"
      type="text"
      v-on:keyup.enter="onValidate"
      v-model="tagValue"
    />
  </div>
</template>

<script>
export default {
  name: "EditDialog",
  props: ["sentenceBus"],
  data() {
    return {
      dialog: false,
      tagValue: "",
      params: {}
    };
  },
  mounted() {
    this.sentenceBus.$on("open:editDialog", params => {
      this.dialog = true;
      this.token = params;
      this.field = params["FIELD"];
      this.focusInput();
    });
    this.sentenceBus.$on("reset:allDialog", () => {
      this.dialog = false;
      this.token = {};
    });
  },
  methods: {
    onValidate() {
      let newValue = this.tagValue;
      if (newValue === "") {
        newValue = "_";
      }
      this.token[this.field] = newValue;
      this.sentenceBus.$emit("update:token", this.token);
      this.token = {};
      this.dialog = false;
    },
    focusInput() {
      setTimeout(() => {
        this.$refs.input.focus();
      }, 100);
    }
  }
};
</script>

<style></style>
