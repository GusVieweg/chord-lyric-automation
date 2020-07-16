<template>
  <!-- container for top portion, through export button -->
  <v-container class="py-0 mt-6" style="max-width:1050px;">
    <v-row align="center">
      <!-- page title -->
      <v-col class="pt-0 my-0 pb-3 pr-0" cols="3" align="center">
        <p>
          <v-icon
            style="vertical-align:middle;margin-bottom:6px; color:#5F917A;"
            >mdi-music-note</v-icon
          >
          <b>Bowstring</b>

          <!-- help dialog -->
          <v-dialog v-model="dialog" width="500">
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                class="mx-2"
                fab
                dark
                small
                text
                color="#5F917A"
                v-on="on"
                v-bind="attrs"
                style="margin-bottom:2px;"
              >
                <v-icon dark>mdi-information-outline</v-icon>
              </v-btn>
            </template>
            <v-card>
              <v-card-title class="headline" primary-title
                >Bowstring Help</v-card-title
              >
              <v-divider></v-divider>
              <v-card-text>
                <br />
                <b>To add a solo chordbox:</b> Type a ">" followed by the chord
                (i.e. <code>>Gmaj7</code>).
                <br />
                <b>To export:</b> Click the download button to export to PDF.
                <br />
                <br />If a chordbox is red, the root note is invalid.
              </v-card-text>
              <v-divider></v-divider>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn text @click="dialog = false">close</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </p>
      </v-col>

      <!-- transpose up button -->
      <v-col class="py-0 my-0" cols="1" offset="8">
        <v-btn
          class="py-1 my-0"
          small
          outlined
          block
          :disabled="disableTranspose"
          color="#5F917A"
          @click="transposeUp()"
          name="transposeUp"
        >
          <v-icon dark small>mdi-arrow-up-thick</v-icon>
        </v-btn>
      </v-col>
    </v-row>

    <v-row align="center">
      <v-col cols="8" class="py-0 my-0">
        <!-- title input box -->
        <v-text-field
          class="py-0 my-0"
          id="titlebox"
          type="text"
          name="titlebox"
          v-model="title"
          placeholder="Project/Song Title"
          hide-details
          tabindex="1"
          style="font-weight:bold;"
        />
      </v-col>

      <!-- transpose down button -->
      <v-col cols="1" offset="3" class="py-0 my-0">
        <v-btn
          class="py-0 my-0"
          small
          outlined
          block
          color="#5F917A"
          @click="transposeDown()"
          name="transposeDown"
          :disabled="disableTranspose"
        >
          <v-icon dark small>mdi-arrow-down-thick</v-icon>
        </v-btn>
      </v-col>
    </v-row>
    <v-row>
      <!-- lyric input box -->
      <v-col class="py-0 my-0">
        <v-textarea
          id="lyrics"
          name="lyrics"
          v-model="lyrics"
          background-color="white"
          rows="5"
          cols="33"
          placeholder="Enter your lyrics!"
          tabindex="2"
          style="border-color:white !important;"
        ></v-textarea>
      </v-col>
    </v-row>
    <v-row align="center">
      <!-- submit button -->
      <v-col class="py-0 my-0" cols="2">
        <v-btn
          small
          depressed
          block
          color="#5F917A"
          style="color:white;"
          @click="
            lyricsDone();
            titleEntered();
          "
          name="submitLyrics"
          tabindex="3"
        >
          <v-icon>mdi-chevron-double-right</v-icon>
        </v-btn>
      </v-col>

      <v-col class="py-0 my-0" cols="1" offset="8">
        <!-- copy button -->
        <v-btn
          @click="copyText"
          class="py-1 my-0"
          small
          outlined
          block
          color="#5F917A"
          :disabled="!lyricArray"
        >
          <v-icon>{{ this.clipboardIcon }}</v-icon>
        </v-btn>
      </v-col>
      <v-col class="py-0 my-0" cols="1">
        <!-- export button -->
        <v-btn
          @click="exportPDF"
          class="py-1 my-0"
          small
          outlined
          block
          color="#5F917A"
          :disabled="!lyricArray"
        >
          <v-icon>mdi-download</v-icon>
        </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    lyrics: "",
    lyricArray: null,
    lyricUpdates: 0,
    title: "",
    transposeN: 0,
    dialog: false,
    clipboardIcon: "mdi-clipboard-outline"
  }),
  props: {
    disableTranspose: Boolean
  },
  methods: {
    lyricsDone() {
      this.lyricArray = this.lyrics.split("\n");
      this.lyricUpdates += 1;
      this.clipboardIcon = "mdi-clipboard-outline";
      this.$emit("lyricsDone", {
        array: this.lyricArray,
        updates: this.lyricUpdates
      });
    },
    titleEntered() {
      this.$emit("titleEntered", this.title);
    },
    transposeUp() {
      this.transposeN = this.transposeN + 1;
      this.$emit("transposeChanged", this.transposeN);
    },
    transposeDown() {
      this.transposeN = this.transposeN - 1;
      this.$emit("transposeChanged", this.transposeN);
    },
    exportPDF() {
      this.$emit("export");
    },
    copyText() {
      this.$emit("copyText");
      this.clipboardIcon = "mdi-clipboard-check";
    }
  }
};
</script>

<style scoped>
/* app title block */
p {
  float: left;
  text-align: left;
  font-size: 26px;
  margin-bottom: 0px !important;
}

/* title text field */
.v-text-field {
  font-family: "Courier New", Courier, monospace;
  background-color: white;
  border-radius: 2px;
  border-color: #dcdcdc;
}

/* lyric input field */
.v-textarea {
  font-family: "Courier New", Courier, monospace;
}
</style>
