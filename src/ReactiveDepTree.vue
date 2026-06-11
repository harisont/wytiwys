<template>
  <div class="scroll-wrapper">
    <div class="component-wrapper">
      <template v-if="minimal == false">
        <div class="header">
          <ShownFeaturesToolbar :shown-features="this.processFeaturesInput(this.shownFeatures)" v-on:updateVisibility="updateVisibility"/>
        </div>
        <svg
          ref="svgWrapper"
          class="svg-tree center"
          xmlns="http://www.w3.org/2000/svg"
        />
        <EditDialog :sentenceBus="sentenceBus" />
        <button class="center" v-on:click="toggleConll">CoNLL-U</button>
        <ShowConll
          :reactiveSentence="reactiveSentence"
          :sentenceBus="sentenceBus"
          :shownFeatures="shownFeatures"
        />
        <v-style ref="stylee"> </v-style>
      </template>
      <template v-else>
        <svg
          ref="svgWrapper"
          class="svg-tree"
          xmlns="http://www.w3.org/2000/svg"
        />
      </template>
    </div>
  </div>
</template>

<script>
import Vue from "vue";

import {
  ReactiveSentence,
  SentenceCaretaker,
  SentenceSVG,
  defaultSentenceSVGOptions
} from "wydependencytreejsiwys/lib";

import EditDialog from "./components/EditDialog.vue";
import ShowConll from "./components/ShowConll.vue";
import ShownFeaturesToolbar from "./components/ShownFeaturesToolbar.vue";

export default {
  components: { EditDialog, ShowConll, ShownFeaturesToolbar },
  props: {
    conll: String,
    interactive: Boolean,
    shownFeatures: String,
    shownMetas: String,
    minimal: {
      type: Boolean,
      default: false
    },
    arcHeight: {
      type: Number,
      default: 40
    },
    tokenSpacing: {
      type: Number,
      default: 28
    }
  },
  data() {
    return {
      reactiveSentence: new ReactiveSentence(),
      sentenceSVG: null,
      sentenceBus: new Vue(),
      sentenceCaretaker: null,
      sentenceText: "",
      show: false,
      hover: true,
      shownMetasObj: {}
    };
  },
  mounted() {
    const shownFeatures = this.processFeaturesInput(this.shownFeatures);
    this.renderSentence(shownFeatures);
  },
  computed: {
    shownMetasList() {
      return this.processFeaturesInput(this.shownMetas);
    }
  },
  methods: {
    toggleConll() {
      this.sentenceBus.$emit("UI:toggle-conll");
    },
    // idk if some of this DOESN'T need to be done each time shown features change and could be moved back to mounted(), nor if "render" is an exhaustive term...
    renderSentence(shownFeatures) {
      const svgWrapper = this.$refs.svgWrapper;
      // add the component to the list of reactiveSentence observers
      this.reactiveSentence.attach(this);
      this.reactiveSentence.fromSentenceConll(this.conll);

      const sentenceSVGOptions = defaultSentenceSVGOptions();
      sentenceSVGOptions.shownFeatures = shownFeatures;
      sentenceSVGOptions.interactive = this.interactive;
      sentenceSVGOptions.tokenSpacing = this.tokenSpacing;
      sentenceSVGOptions.arcHeight = this.arcHeight;

      this.sentenceSVG = new SentenceSVG(
        svgWrapper,
        this.reactiveSentence,
        sentenceSVGOptions
      );

      this.sentenceCaretaker = new SentenceCaretaker(this.reactiveSentence);
      this.sentenceCaretaker.backup();

      // handle clicks on individual elements
      this.sentenceSVG.addEventListener("svg-click", e => {
        this.sentenceBus.$emit("reset:allDialog");
        const targetLabel = e.detail.targetLabel;
        const tokenId = e.detail.clicked;
        const svgPosition = this.sentenceSVG.tokenIndexToSvgPosition[tokenId];
        const oldValue = this.sentenceSVG.tokenSVGs[svgPosition].snapElements[targetLabel].innerSVG();
        

      // just an internal function (lambda) wrapping reactiveSentence.toggleBoolFeat with UI stuff cause I don't know better...
      const toggle = (feat) => {
        const button = this.sentenceSVG.tokenSVGs[svgPosition].snapElements[targetLabel];
        const active = this.reactiveSentence.toggleBoolFeat(tokenId, feat);
        if (active) {
          button.removeClass("inactive");
        } else {
          button.addClass("inactive");
        }
      }

        if (targetLabel == "ADD_AFTER") {
          this.reactiveSentence.addEmptyTokenAfter(tokenId);
        } else if (targetLabel == "ADD_BEFORE") {
          this.reactiveSentence.addEmptyTokenBefore(tokenId);
        } else if (targetLabel == "REMOVE") {
          this.reactiveSentence.removeToken(tokenId);
        } else if (targetLabel.startsWith("ANCHOR")) {
          toggle("anchored");
        } else if (targetLabel == "CHAIN") {
          toggle("subsequent");
        } else if (targetLabel == "LOCK") {
          toggle("ordered");
        } else {
          this.sentenceBus.$emit("open:editDialog", {
            ID: tokenId,
            FIELD: targetLabel, // additional param to know which column to modify
            VALUE: oldValue
        })
        this.sentenceBus.$on("update:token", token => {
        this.reactiveSentence.updateToken(token);
        this.sentenceCaretaker.backup();
        })}
      });

      // handle dragging-and-dropping of edges 
      this.sentenceSVG.addEventListener("svg-drop", e => {
        this.sentenceBus.$emit("reset:allDialog");
        let tokenId;
        let headId;
        if (e.detail.hovered > 0) {
          tokenId = e.detail.hovered;
          headId = parseInt(e.detail.dragged, 10); 
        } else {
          tokenId = e.detail.dragged;
          headId = 0;
        }
        const svgPosition = this.sentenceSVG.tokenIndexToSvgPosition[tokenId];
        const oldValue = this.sentenceSVG.tokenSVGs[svgPosition].snapElements["DEPREL"].innerSVG();
        if (tokenId >= 0 && headId >= 0) {
          this.sentenceBus.$emit("open:editDialog", {
            ID: tokenId,
            HEAD: headId,
            FIELD: "DEPREL", // additional param to know which column to modify
            VALUE: oldValue
          });
        }
        this.sentenceBus.$on("update:token", token => {
        this.reactiveSentence.updateToken(token);
        this.sentenceCaretaker.backup();
        });
      });
    },

    updateVisibility(shownFeatures) {
      this.shownFeatures = shownFeatures;
      this.renderSentence(shownFeatures);
    },

    update(reactiveSentence) {
      // this method is called whenever the reactiveSentence instance changes and call the 'update()' method of all his observers
      this.sentenceText = reactiveSentence.getSentenceText();
      this.shownMetasObj = {};
      for (const shownMeta of this.shownMetasList) {
        if (reactiveSentence.state.metaJson[shownMeta]) {
          this.shownMetasObj[shownMeta] =
            reactiveSentence.state.metaJson[shownMeta];
        }
      }
    },
    processFeaturesInput(features) {
      // not sure why and how this method is called every time renderSentence happens but it does 
      // so, return features as is if it is already an array
      if (features instanceof Array) {
        return features;
      }
      let processedFeatures;
      if (features) {
        processedFeatures = features.split(",");
      } else {
        processedFeatures = [];
      }
      return processedFeatures;
    }
  }
};
</script>
<style>
* {
  box-sizing: border-box;
}
.scroll-wrapper {
  overflow-x: auto;
}
.component-wrapper {
  /* padding-left: 20px; */
  width: fit-content;
}

.meta {
  margin-bottom: 0.6em;
  border-bottom: 1px solid lightgrey;
  padding: 0.5em 0 0.17em 0;
}

.header {
  margin-bottom: 10px;
}

.meta__dropdown {
  padding: 0 20px;
}

.meta__dropdown,
.meta__text {
  display: inline-block;
  vertical-align: middle;
}

.meta__text {
  line-height: 1.5;
}

.meta__infos {
  font-family: "Open Sans", "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 0.9em;
  font-style: italic;
  padding-left: 65px;
}

.center {
  display: block;
  margin: auto;
  width: max-content;
}

</style>
