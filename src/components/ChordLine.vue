<template>
  <div>
    <div v-for="(word, index) in words" :key="index" class="sameLine">
      <ChordBox
        :exporting="exporting"
        @chordEntered="captureChord($event, index)"
        @disableTranspose="andDisable($event, index)"
        :transposeN="transposeN"
        :style="'padding-right:' + spaces[index] + 'ch;'"
        :chord="word"
      />
    </div>
  </div>
</template>

<script>
import ChordBox from "@/components/ChordBox.vue";
import { DismantleLyric } from "@/mixins/DismantleLyric.js";

export default {
  name: "ChordLine",
  mixins: [DismantleLyric],
  data: () => ({
    wordCount: 1,
    chords: null, // will need to capture this from ChordBox,
    badChords: null,
    badChordLine: false,
    words: null
  }),
  props: {
    lyricUpdates: Number,
    lyric: String,
    spaces: Array,
    line: Number,
    transposeN: Number,
    exporting: Boolean
  },
  methods: {
    countWords() {
      this.words = this.dismantleLyric(this.lyric);
      this.wordCount = this.words.length;
      if (this.wordCount < 1) {
        this.wordCount = 1;
      }
    },
    captureChord(val, index) {
      this.chords.splice(index, 1, val);
      this.compareLyricsAndChords(index);
      this.$emit("chordsEntered", this.chords);
    },
    compareLyricsAndChords(index) {
      let l = this.dismantleLyric(this.lyric);
      let c = this.chords;
      if (!c[index]) {
        c.splice(index, 1, "x");
      }
      if (l[index][0] == "\\") {
        c[index] = l[index].substring(1);
        l[index] = "";
      }
      if (l[index].length > c[index].length) {
        this.spaces.splice(index, 1, l[index].length - c[index].length + 1);
      } else {
        this.spaces.splice(index, 1, 1);
      }
    },
    andDisable(disable, index) {
      this.badChords.splice(index, 1, disable);
      this.badChordLine = this.badChords.some((badChord) => badChord == true);
      this.$emit("disableTranspose", this.badChordLine);
    }
  },
  mounted() {
    this.countWords();
    this.chords = Array(this.wordCount);
    this.chords.fill("x");
    this.badChords = Array(this.wordCount);
    this.badChords.fill(false);
  },
  components: {
    ChordBox
  },
  watch: {
    lyricUpdates: function() {
      this.countWords();
      for (let i = 0; i < this.wordCount; i++) {
        this.compareLyricsAndChords(i);
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.sameLine {
  float: left;
}
</style>
