<template>
  <div class="meta__shown_features">
    <span class="checkbox"> 👁 </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" id="FORM" name="shown_features" value="FORM">
      <label for="FORM">FORM</label>
    </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" id="LEMMA" name="shown_features" value="LEMMA">
      <label for="LEMMA">LEMMA</label>
    </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" UPOS="UPOS" name="shown_features" value="UPOS">
      <label for="UPOS">UPOS</label>
    </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" XPOS="XPOS" name="shown_features" value="XPOS">
      <label for="XPOS">XPOS</label>
    </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" FEATS="FEATS" name="shown_features" value="FEATS">
      <label for="FEATS">FEATS</label>
    </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" HEAD="HEAD" name="shown_features" value="HEAD">
      <label for="HEAD">HEAD</label>
    </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" DEPREL="DEPREL" name="shown_features" value="DEPREL">
      <label for="DEPREL">DEPREL</label>
    </span>

    <span class="checkbox">
      <input onclick="toggleVisibility" type="checkbox" DEPS="DEPS" name="shown_features" value="DEPS">
      <label for="DEPS">DEPS</label>
    </span>

  </div>
</template>

<script>
export default {
  name: "ShownFeatures",
  props: ["shownFeatures"],
  methods: {
    toggleVisibility() {
      alert(this.shownFeatures)
    }
  }
}
</script>

<style>
.checkbox {
  padding: 0.25em;
}
</style>