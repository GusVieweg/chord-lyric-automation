<template>
  <div>
    <span class="lyric-container" v-for="(word, index) in words" :key="index">
      <span class="word" :style="'padding-right:' + spaces[index] + 'ch;'">
        {{ word }}
      </span>
    </span>
  </div>
</template>

<script>
import { DismantleLyric } from "@/mixins/DismantleLyric.js";
export default {
  name: "LyricLine",
  mixins: [DismantleLyric],
  data: () => ({
    words: [],
    spaces: null
  }),
  methods: {
    addSpacesToLyric() {
      for (let i = 0; i < this.words.length; i++) {
        this.compareLyricsAndChords(i);
      }
    },
    compareLyricsAndChords(index) {
      let l = this.words;
      let c = this.chords;

      if (!c[index]) {
        c.splice(index, 1, "x");
      }
      if (index == 0 && !l[index].trim()) {
        this.spaces.splice(index, 1, c[index].length + 1);
        return;
      }
      if (c[index].length > l[index].length) {
        this.spaces.splice(index, 1, c[index].length - l[index].length + 1);
      } else {
        this.spaces.splice(index, 1, 1);
      }
    }
  },
  props: ["lyric", "chords", "lyricUpdates"],
  watch: {
    chords: {
      handler: function() {
        if (this.chords.length == this.words.length) {
          this.addSpacesToLyric();
        }
      }
    },
    lyricUpdates: {
      handler: function() {
        this.words = this.dismantleLyric(this.lyric);
        this.spaces = Array(this.words.length);
        this.spaces.fill(1);
        this.words.forEach((word, index) => {
          if (word[0] == ">") {
            this.words[index] = "";
          }
        });
        this.addSpacesToLyric();
      }
    }
  },
  mounted() {
    this.words = this.dismantleLyric(this.lyric);
    this.spaces = Array(this.words.length);
    this.spaces.fill(1);
    this.words.forEach((word, index) => {
<<<<<<< HEAD
      if (word[0] == ">") {
=======
      if (word[0] == "\\") {
        // this.words[index] = " ";
>>>>>>> 20fec8d0999452b7b7640cc74d03acfb800d19f6
        this.words[index] = "";
      }
    });
    this.addSpacesToLyric();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
span {
  font-size: 16px;
}
</style>
