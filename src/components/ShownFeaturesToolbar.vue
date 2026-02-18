<template>
  <div class="meta__shown_features">
    <span class="checkbox"> 👁 </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('FORM')" type="checkbox" ref="FORM" name="shown_features" value="FORM">
      <label for="FORM">FORM</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('LEMMA')" type="checkbox" ref="LEMMA" name="shown_features" value="LEMMA">
      <label for="LEMMA">LEMMA</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('UPOS')" type="checkbox" ref="UPOS" name="shown_features" value="UPOS">
      <label for="UPOS">UPOS</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('XPOS')" type="checkbox" ref="XPOS" name="shown_features" value="XPOS">
      <label for="XPOS">XPOS</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('FEATS')" type="checkbox" ref="FEATS" name="shown_features" value="FEATS">
      <label for="FEATS">FEATS</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('HEAD')" type="checkbox" ref="HEAD" name="shown_features" value="HEAD">
      <label for="HEAD">HEAD</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('DEPREL')" type="checkbox" ref="DEPREL" name="shown_features" value="DEPREL">
      <label for="DEPREL">DEPREL</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('DEPS')" type="checkbox" ref="DEPS" name="shown_features" value="DEPS">
      <label for="DEPS">DEPS</label>
    </span>

    <span class="checkbox">
      <input v-on:click="toggleFeature('MISC')" type="checkbox" ref="MISC" name="shown_features" value="MISC">
      <label for="MISC">MISC</label>
    </span>

  </div>
</template>

<script>
export default {
  name: "ShownFeaturesToolbar",
  props: {
    shownFeatures: []
  },


  mounted() {
    const shown = this.shownFeatures;

    // there must be a better way, but I can't find it
    const displayableFeats = {
      "FORM": this.$refs.FORM,
      "LEMMA": this.$refs.LEMMA,
      "UPOS": this.$refs.UPOS,
      "XPOS": this.$refs.XPOS,
      "FEATS": this.$refs.FEATS,
      "HEAD": this.$refs.HEAD,
      "DEPREL": this.$refs.DEPREL,
      "DEPS": this.$refs.DEPS,
      "MISC": this.$refs.MISC,
    }

    for (const feat in displayableFeats) {
      if (shown.includes(feat)) {
        displayableFeats[feat].checked = true;        
      } 
    };
  },

  methods: {
    toggleFeature(feat) {
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