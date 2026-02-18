<template>
    <span class="checkbox">
      <input v-on:click="toggleFeature" type="checkbox" name="shown_features" value='{{ feat }}' v-model="checked">
      <label for='{{ feat }}'>{{ feat }}</label>
    </span>
</template>

<script>
export default {
  name: "FeatureCheckbox",
  props: {
    feat: String,
    checked: Boolean
  },
  methods: {
    toggleFeature() {
      console.log("toggleFeature")
      // somehow this message is sent, but not received
      this.$emit("updateFeatureVisibility", this.feat);
    }
  }
}
</script>
