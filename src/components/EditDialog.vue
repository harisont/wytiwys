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
      switch (this.field) {
        case "UPOS":
            this.token.UPOS = newValue;
            break;
        case "DEPREL":
            this.token.DEPREL = newValue;
            break;
        default:
            alert("Ooops! We can't change that yet!")
            break;
      }
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
