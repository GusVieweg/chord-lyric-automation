<template>
  <v-container class="py-0 mt-7" style="max-width:1050px;">
    <Controller
      @lyricsDone="captureLyrics"
      @titleEntered="captureTitle"
      @transposeChanged="captureTranspose"
      :disableTranspose="disableTranspose"
      @export="exportPDF"
      @copyText="copyText"
    ></Controller>
    <v-container ref="output">
      <v-row align="center" class="py-0 mt-7">
        <v-col>
          <h1>{{ title }}</h1>
        </v-col>
      </v-row>
      <v-row align="center" class="mt-0">
        <v-col>
          <div v-for="(lyric, idx) in lyrics" :key="idx">
            <ComboLine
              :lyricUpdates="lyricUpdates"
              :lyric="lyric"
              :transposeN="transposeN"
              @disableTranspose="captureDisable"
              :exporting="exporting"
            />
          </div>
        </v-col>
      </v-row>
    </v-container>
  </v-container>
</template>

<script>
// @ is an alias to /src
import ComboLine from "@/components/ComboLine.vue";
import Controller from "@/components/Controller.vue";
import jsPDF from "jspdf";
import { CopyChordsAndLyrics } from "@/mixins/CopyChordsAndLyrics.js";

// import html2canvas from "html2canvas";

export default {
  name: "Home",
  mixins: [CopyChordsAndLyrics],
  data: () => ({
    lyrics: null,
    lyricUpdates: 0,
    title: "",
    transposeN: 0,
    disableTranspose: true,
    output: null,
    exporting: false
  }),
  methods: {
    captureLyrics(lyrics) {
      this.lyrics = lyrics.array;
      this.lyricUpdates = lyrics.updates;
    },
    captureTitle(value) {
      this.title = value;
    },
    captureTranspose(transposeN) {
      this.transposeN = transposeN;
    },
    captureDisable(disable) {
      this.disableTranspose = disable;
    },
    async exportPDF() {
      this.exporting = true;
      const el = this.$refs.output;
      let doc = new jsPDF();
      const options = {
        type: "dataURL"
      };
      this.output = await this.$html2canvas(el, options);
      doc.addImage(this.output, "JPEG", 15, 0);
      doc.save(this.title + " Chords and Lyrics.pdf");
      this.exporting = false;
    },
    copyText() {
      let spans = this.$refs.output.querySelectorAll(
        "span:not(.lyric-container)"
      );
      // from CopyChordsAndLyrics mixin
      this.copyChordsAndLyrics(spans);
    }
  },
  components: {
    ComboLine,
    Controller
  },
  mounted() {
    document.title = "Bowstring";
  }
};
</script>

<style>
h1 {
  float: left;
  text-align: left;
  font-family: "Courier New", Courier, monospace;
  font-size: 24px;
}
</style>
