<template>
  <div class="meta__shown_features" ref="sft">
    <span class="checkbox"> 👁 </span>
  </div>
</template>

<script>
import Vue from 'vue'
import FeatureCheckbox from './FeatureCheckbox.vue';

export default {
  name: "ShownFeaturesToolbar",
  props: {
    shownFeatures: []
  },


  mounted() {
    const showableFeatures = [
      "FORM", "LEMMA", "UPOS", "XPOS", "FEATS", "HEAD", "DEPREL", "DEPS", "MISC"];

    const FeatureCheckboxClass = Vue.extend(FeatureCheckbox);

    showableFeatures.forEach((feat) => {
      const checked = this.shownFeatures.includes(feat);
      const checkbox = new FeatureCheckboxClass({
        propsData: {feat: feat, checked: checked}
      });
      checkbox.$mount();  
      this.$refs.sft.appendChild(checkbox.$el);
      checkbox.$on("updateFeatureVisibility", this.updateFeatureVisibility);
    })
  },

  methods: {
    updateFeatureVisibility(feat) {
      if (this.shownFeatures.includes(feat)) {
        this.shownFeatures = this.shownFeatures.filter((shownFeat) => shownFeat != feat);
      } else {
        this.shownFeatures.push(feat);
      }
      this.$emit("updateVisibility", this.shownFeatures);
    }
  }
}
</script>

<style>
.checkbox {
  padding: 0.25em;
}
</style>