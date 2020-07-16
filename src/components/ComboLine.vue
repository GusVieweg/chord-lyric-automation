<template>
  <v-container class="pa-0 my-0">
    <v-row>
      <v-col class="py-0 mt-0">
        <ChordLine
          :exporting="exporting"
          :spaces="spaces"
          :lyric="lyric"
          :lyricUpdates="lyricUpdates"
          :transposeN="transposeN"
          @chordsEntered="captureChords($event)"
          @disableTranspose="disable($event)"
        />
      </v-col>
    </v-row>
    <v-row>
      <v-col class="py-0 my-2">
        <LyricLine
          :lyric="lyric"
          :lyricUpdates="lyricUpdates"
          :chords="chords"
          style="padding-top:0px;"
        />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import ChordLine from "@/components/ChordLine.vue";
import LyricLine from "@/components/LyricLine.vue";
import { DismantleLyric } from "@/mixins/DismantleLyric.js";
export default {
  name: "ComboLine",
  mixins: [DismantleLyric],
  data: () => ({
    spaces: [],
    chords: []
  }),
  props: {
    lyricUpdates: Number,
    lyric: String,
    transposeN: Number,
    exporting: Boolean
  },
  methods: {
    captureChords(chords) {
      this.chords = chords;
    },
    disable(disable) {
      this.$emit("disableTranspose", disable);
    }
  },
  watch: {
    lyric: function calculateSpaces() {
      this.spaces = [];
      let words = this.dismantleLyric(this.lyric);

      this.spaces = words.map((x) => x.length);
    }
  },
  mounted() {
    let words = this.dismantleLyric(this.lyric);

    this.spaces = words.map((x) => x.length);
  },
  components: {
    ChordLine,
    LyricLine
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div {
  font-family: "Courier New", Courier, monospace;
  text-align: left;
  line-height: 11px;
  font-size: 16px;
}
</style>
