<script>
import Vue from "vue";

import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7.7.0/+esm";
import * as unique from "array-unique-deep";
import Scatterplot_freq from "./Scatterplot_freq.vue";
import Distplot_style from "./Distplot_style.vue";
import Scatter_3D from "./Scatter_3D.vue";
import Rect_D3 from "./Rect_D3.vue";
import Single_heatmap from "./Single_heatmap.vue";

export default {
  name: "Single_section",
  components: { Scatterplot_freq, Distplot_style, Scatter_3D, Rect_D3 ,Single_heatmap},
  data() {
    return {
      show_selectors:true,
      select_btn:false,
      last_scroll_position:0,
      temp_file_list: [],
      target_folder: "",
      target_folder_idx: "",
      target_files: [],
      target_file: "",
      target_chaps: [],
      target_chap: "",
      target_verse: "",
      target_verses: [],
      displayable_verses: [],
      title_selection: '',
      token_list: [],
      badge_chaps: [],
      start_flag: false,
      loading_status: true,
      freq_: {}, //count token's frequency
      grouped_tokens: [], //same as displayable_tokens but with [2]=tokens //count grouped by book and chapter
      filtered_data:[] ,//for network
      freq_status: false,
      freq_plot_key: 0,
      dist_plot_key: 0,
      scatter_3d_key: 0,
      rect_key: 0,
      heat_key:0,
      folders: [
        "Orthodox Jewish Bible",
        "Young's Literal Translation",
        "New World Translation",
        "New Revised Standard Version"
      ],
      //target_files: "",
      files: [
        //Orthodox Jewish Bible
        [
          "1-Genesis",
          "2-Exodus",
          "3-Leviticus",
          "4-Numbers",
          "5-Deuteronomy",
          "6-Joshua",
          "7-Judges",
          "8-Ruth",
          "9-1 Samuel",
          "10-2 Samuel",
          "11-1 Kings",
          "12-2 Kings",
          "13-1 Chronicles",
          "14-2 Chronicles",
          "15-Ezra",
          "16-Nehemiah",
          "17-Esther",
          "18-Job",
          "19-Psalms",
          "20-Proverbs",
          "21-Ecclesiastes",
          "22-Song Of Songs",
          "23-Isaiah",
          "24-Jeremiah",
          "25-Lamentations",
          "26-Ezekiel",
          "27-Daniel",
          "28-Hosea",
          "29-Joel",
          "30-Amos",
          "31-Obadiah",
          "32-Jonah",
          "33-Micah",
          "34-Nahum",
          "35-Habukkuk",
          "36-Zephaniah",
          "37-Haggai",
          "38-Zechariah",
          "39-Malachi",
          "40-Matthew",
          "41-Mark",
          "42-Luke",
          "43-John",
          "44-Acts",
          "45-Romans",
          "46-1 Corinthians",
          "47-2 Corinthians",
          "48-Galatians",
          "49-Ephesians",
          "50-Philippians",
          "51-Colossians",
          "52-1 Thessalonians",
          "53-2 Thessalonians",
          "54-1 Timothy",
          "55-2 Timothy",
          "56-Titus",
          "57-Philemon",
          "58-Hebrews",
          "59-James",
          "60-1 Peter",
          "61-2 Peter",
          "62-1 John",
          "63-2 John",
          "64-3 John",
          "65-Jude",
          "66-Revelation",
        ],
        //Young'S Literal Translation
        [
          "1-Genesis",
          "2-Exodus",
          "3-Leviticus",
          "4-Numbers",
          "5-Deuteronomy",
          "6-Joshua",
          "7-Judges",
          "8-Ruth",
          "9-1 Samuel",
          "10-2 Samuel",
          "11-1 Kings",
          "12-2 Kings",
          "13-1 Chronicles",
          "14-2 Chronicles",
          "15-Ezra",
          "16-Nehemiah",
          "17-Esther",
          "18-Job",
          "19-Psalms",
          "20-Proverbs",
          "21-Ecclesiastes",
          "22-Song Of Songs",
          "23-Isaiah",
          "24-Jeremiah",
          "25-Lamentations",
          "26-Ezekiel",
          "27-Daniel",
          "28-Hosea",
          "29-Joel",
          "30-Amos",
          "31-Obadiah",
          "32-Jonah",
          "33-Micah",
          "34-Nahum",
          "35-Habukkuk",
          "36-Zephaniah",
          "37-Haggai",
          "38-Zechariah",
          "39-Malachi",
          "40-Matthew",
          "41-Mark",
          "42-Luke",
          "43-John",
          "44-Acts",
          "45-Romans",
          "46-1 Corinthians",
          "47-2 Corinthians",
          "48-Galatians",
          "49-Ephesians",
          "50-Philippians",
          "51-Colossians",
          "52-1 Thessalonians",
          "53-2 Thessalonians",
          "54-1 Timothy",
          "55-2 Timothy",
          "56-Titus",
          "57-Philemon",
          "58-Hebrews",
          "59-James",
          "60-1 Peter",
          "61-2 Peter",
          "62-1 John",
          "63-2 John",
          "64-3 John",
          "65-Jude",
          "66-Revelation",
        ],
        //New World Translation
        [
          "1-Genesis",
          "2-Exodus",
          "3-Leviticus",
          "4-Numbers",
          "5-Deuteronomy",
          "6-Joshua",
          "7-Judges",
          "8-Ruth",
          "9-1 Samuel",
          "10-2 Samuel",
          "11-1 Kings",
          "12-2 Kings",
          "13-1 Chronicles",
          "14-2 Chronicles",
          "15-Ezra",
          "16-Nehemiah",
          "17-Esther",
          "18-Job",
          "19-Psalms",
          "20-Proverbs",
          "21-Ecclesiastes",
          "22-Song of Songs",
          "23-Isaiah",
          "24-Jeremiah",
          "25-Lamentations",
          "26-Ezekiel",
          "27-Daniel",
          "28-Hosea",
          "29-Joel",
          "30-Amos",
          "31-Obadiah",
          "32-Jonah",
          "33-Micah",
          "34-Nahum",
          "35-Habakkuk",
          "36-Zephaniah",
          "37-Haggai",
          "38-Zechariah",
          "39-Malachi",
          "40-Matthew",
          "41-Mark",
          "42-Luke",
          "43-John",
          "44-Acts",
          "45-Romans",
          "46-1 Corinthians",
          "47-2 Corinthians",
          "48-Galatians",
          "49-Ephesians",
          "50-Philippians",
          "51-Colossians",
          "52-1 Thessalonians",
          "53-2 Thessalonians",
          "54-1 Timothy",
          "55-2 Timothy",
          "56-Titus",
          "57-Philemon",
          "58-Hebrews",
          "59-James",
          "60-1 Peter",
          "61-2 Peter",
          "62-1 John",
          "63-2 John",
          "64-3 John",
          "65-Jude",
          "66-Revelation",
        ],
        //New Revised Standard Version
        [
          "1-Genesis",
          "2-Exodus",
          "3-Leviticus",
          "4-Numbers",
          "5-Deuteronomy",
          "6-Joshua",
          "7-Judges",
          "8-Ruth",
          "9-1 Samuel",
          "10-2 Samuel",
          "11-1 Kings",
          "12-2 Kings",
          "13-1 Chronicles",
          "14-2 Chronicles",
          "15-Ezra",
          "16-Nehemiah",
          "17-Esther",
          "18-Job",
          "19-Psalms",
          "20-Proverbs",
          "21-Ecclesiastes",
          "22-Song Of Songs",
          "23-Isaiah",
          "24-Jeremiah",
          "25-Lamentations",
          "26-Ezekiel",
          "27-Daniel",
          "28-Hosea",
          "29-Joel",
          "30-Amos",
          "31-Obadiah",
          "32-Jonah",
          "33-Micah",
          "34-Nahum",
          "35-Habukkuk",
          "36-Zephaniah",
          "37-Haggai",
          "38-Zechariah",
          "39-Malachi",
          "40-Matthew",
          "41-Mark",
          "42-Luke",
          "43-John",
          "44-Acts",
          "45-Romans",
          "46-1 Corinthians",
          "47-2 Corinthians",
          "48-Galatians",
          "49-Ephesians",
          "50-Philippians",
          "51-Colossians",
          "52-1 Thessalonians",
          "53-2 Thessalonians",
          "54-1 Timothy",
          "55-2 Timothy",
          "56-Titus",
          "57-Philemon",
          "58-Hebrews",
          "59-James",
          "60-1 Peter",
          "61-2 Peter",
          "62-1 John",
          "63-2 John",
          "64-3 John",
          "65-Jude",
          "66-Revelation",
        ],
      ],
      coord_conj: ["for", "and", "but", "nor", "or", "yet", "so"],
      subord_conj: [
        "as though",
        "supposing",
        "whereas",
        "till",
        "which",
        "in order that",
        "even if",
        "as long as",
        "now that",
        "since",
        "before",
        "where if",
        "so that",
        "because",
        "as soon as",
        "til",
        "while",
        "in order",
        "inasmuch",
        "after",
        "whoever",
        "once",
        "as",
        "now",
        "besides",
        "as if",
        "if",
        "that",
        "if only",
        "still",
        "whenever",
        "if then",
        "wherever",
        "though",
        "whether",
        "rather than",
        "unless",
        "although",
        "now since",
        "in case",
        "until",
        "as much as",
        "why",
        "where",
        "within",
        "if when",
        "even though",
        "provided",
        "when",
        "than",
        "who",
        "just as",
        "provided that",
        "without",
        "even",
        "lest",
        "now when",
      ],
      chaps: [],

      loaded_file: [],
    };
  },
  mounted() {

    window.addEventListener('scroll', this.onScroll)
    

    Promise.all(this.folders.map((d) => d3.csv("./static/" + d + ".csv"))).then(
      (csvs) => {
        this.loaded_file.push(csvs[0]);
        this.loaded_file.push(csvs[1]);
        this.loaded_file.push(csvs[2]);
        this.loaded_file.push(csvs[3]);
        //this.loaded_file.push(csvs[4]);
      }
    );
  },
  beforeDestroy () {
  window.removeEventListener('scroll', this.onScroll)
},
  methods: {
   
    manual_update(val) {
      val += 1;
      console.log(val);
    },
    tokenize() {
      //temp_text is for scatter
      var temp_text = this.displayable_verses.map((d) => d[2]).join(" "); //only verses
      this.token_list = temp_text.split(/\W+/);
      console.log(this.token_list);
      console.log("VERSES", this.displayable_verses);

      //make x and y for freq plot
      var token_keys = [...new Set(this.token_list)];
      var freq_counter = {};
      for (var each of this.token_list) {
        if (isNaN(freq_counter[each])) {
          freq_counter[each] = 1;
        } else {
          freq_counter[each] += 1;
        }
      }

      //it is for dist
      var temp_grouped_verses = this.displayable_verses.map((d) => [
        d[0],
        d[1],
        d[2].split(/\W+/),
      ]);
      this.grouped_tokens = temp_grouped_verses;

      this.freq_ = freq_counter;
      this.freq_status = true;
      this.freq_plot_key += 1;
      this.rect_key += 1;
      this.dist_plot_key += 1;
      this.scatter_3d_key += 1;
      this.heat_key+=1
      console.log(this.freq_plot_key);
      console.log(freq_counter);
      console.log(Object.keys(freq_counter), Object.values(freq_counter));
    },

    handle_verses() {
      this.title_selection= this.target_folder +' - '+ this.target_file+' - '+this.target_chap+':'+this.target_verse
      console.log(this.title_selection)
      this.freq_stats = false;
      this.displayable_verses = [];
      var temp_file =
        this.loaded_file[this.folders.indexOf(this.target_folder)];
      var temp_book = this.target_file;
      var temp_chap = this.target_chap;
      var temp_verse = this.target_verse;
      console.log("handle verses", temp_file);

      //filt by book
      temp_file = temp_file.filter(function (d) {
        if (temp_book == "All") {
          return d;
        } else if (temp_book != "All") {
          if (d["Book"] == temp_book) {
            return d;
          }
        }
      });

      //filt by chap
      temp_file = temp_file.filter(function (d) {
        if (temp_chap == "All") {
          return d;
        } else if (temp_chap != "All") {
          if (d["Chapter"] == temp_chap) {
            return d;
          }
        }
      });
      console.log(temp_file);

      //filt by verse
      temp_file = temp_file.filter(function (d) {
        if (temp_verse == "All") {
          return d;
        } else if (temp_verse != "All") {
          if (d["Verse"].split(" ")[0] == String(temp_verse)) {
            console.log(d["Verse"], d["Verse"].split(" ")[0]);
            return d;
          }
        }
      });

      console.log(temp_file);

      this.filtered_data=temp_file
      /*this.displayable_verses*/ var temp_displayable_verses = temp_file.map(
        (d) => [d["Book"], d["Chapter"], d["Verse"]]
      );

      console.log(temp_file);
      //color
      var coords_vals = [];
      var subord_vals = [];

      var verses_tokens = temp_file.map((d) => d["Verse"].split(" "));
      console.log(verses_tokens);

      for (let i = 0; i < temp_file.length; i++) {
        var counter_coords = 0;
        for (let j = 0; j < verses_tokens[i].length; j++) {
          //console.log(verses_tokens[i],verses_tokens[i][j])
          if (this.coord_conj.includes(verses_tokens[i][j].toLowerCase())) {
            counter_coords += 1;
          }
        }
        var counter_subords = 0;
        for (let j = 0; j < verses_tokens[i].length; j++) {
          if (this.subord_conj.includes(verses_tokens[i][j].toLowerCase())) {
            counter_subords += 1;
          }
        }

        coords_vals.push(counter_coords);
        if (counter_subords == 0) {
          subord_vals.push(counter_subords);
        } else {
          subord_vals.push(-Math.abs(counter_subords));
        }
        //NEGATIVE
      }

      var colors_temp = coords_vals.map((d, i) => d + subord_vals[i]);

      var color_vals = [];
      //normalize
      for (let i = 0; i < colors_temp.length; i++) {
        if (colors_temp[i] == 0) {
          color_vals.push("neutral");
        } else if (colors_temp[i] > 0) {
          color_vals.push("coord");
        } else if (colors_temp[i] < 0) {
          color_vals.push("subord");
        }
      }
      this.displayable_verses = temp_displayable_verses.map((d, i) => [
        d[0],
        d[1],
        d[2],
        color_vals[i],
      ]);
      console.log(
        "VALS",
        coords_vals,
        subord_vals,
        color_vals,
        temp_displayable_verses
      );
    },
  },
  watch: {
    loaded_file: function () {
      console.log("changed", this.loaded_file);
      //this.target_files=[... new Set(this.loaded_file[this.folders.indexOf(this.target_folder)].map(d=>d['Book']))]
      this.target_folder = "";
      this.target_folder = this.folders[0];
    },
    target_folder: function () {
      this.target_files = [
        "All",
        ...new Set(
          this.loaded_file[this.folders.indexOf(this.target_folder)].map(
            (d) => d["Book"]
          )
        ),
      ];
      this.target_file = "";
      this.target_file = this.target_files[1];
      this.target_chap = "All";
      this.target_verse = "All";
      if (this.start_flag == true) {
        this.handle_verses();
        this.tokenize();
        this.loading_status = false;
      }
    },
    target_file: function () {
      var temp_chaps = this.loaded_file[
        this.folders.indexOf(this.target_folder)
      ].filter((d) => {
        if (d["Book"] == this.target_file) {
          return d["Chapter"];
        }
      });
      this.target_chaps = [
        "All",
        ...new Set(temp_chaps.map((d) => d["Chapter"])),
      ];
      this.target_chap = "";
      this.target_chap = this.target_chaps[0];
      console.log(this.target_chaps);
      if (this.start_flag == true) {
        this.handle_verses();
        this.tokenize();
        this.loading_status = false;
      }
    },
    target_chap: function () {
      var temp_verses = this.loaded_file[
        this.folders.indexOf(this.target_folder)
      ].filter((d) => {
        if (d["Book"] == this.target_file) {
          if (d["Chapter"] == this.target_chap) {
            return d["Verse"];
          }
        }
      });

      this.target_verses = [
        "All",
        ...temp_verses.map((d) => d["Verse"].split(" ")[0]),
      ];
      this.target_verse = "";
      this.target_verse = this.target_verses[0];
      if (this.start_flag == true) {
        this.handle_verses();
        this.tokenize();
        this.loading_status = false;
      }
    },
    target_verse: function () {
      //comment out "this.handle_verses" if don't want to load all verses at start
      this.handle_verses();
      console.log(this.verses_);
      this.start_flag = true;
      this.tokenize();
      this.loading_status = false;
    },
    freq_: function () {
      console.log(this.freq_);
    },
  },
};

var selectors=document.getElementById('selects_handler')
</script>
    
    <template>
  <div id="single_section">
    <div class="d-flex justify-content-center mb-3">
      <b-spinner
        v-if="loading_status"
        id="single_section_spinner"
        label="Loading..."
      ></b-spinner>
      <span v-else></span>
    </div>
    <b-container>
      <b-button :class="select_btn===true ?'select_btn_active' :'select_btn'" @click="d=>{show_selectors= !show_selectors; select_btn= !select_btn} " v-model="select_btn">{{select_btn===false ?'Hide' :'Show'}} </b-button>
      <b-row class="selects_handler" :class="{ 'selects_handler--hidden': !show_selectors}" v-model="show_selectors">
        <b-col cols="7">
          <b-form inline>
            <b-form-group>
              <b-form-select
                @click="this.loading_status = true"
                v-model="target_folder"
                :options="folders"
                id="folder_selector"
              ></b-form-select>
              <b-form-select
                v-model="target_file"
                :options="target_files"
                id="book_selector"
              ></b-form-select>
              <b-form-select
                v-model="target_chap"
                :options="target_chaps"
                id="chap_selector"
              ></b-form-select>
              <b-form-select
                v-model="target_verse"
                :options="target_verses"
                id="verse_selector"
              ></b-form-select>
            </b-form-group>
          </b-form>
        </b-col>
      </b-row>

      <b-row>
        <div id="verses">
          <b-list-group>
            <b-list-group-item
              :class="'il_' + displayable_verses[i][3]"
              v-for="(verse, i) in this.displayable_verses"
              :key="i + verse[2]"
            >
              <b-badge class="book_badges" pill>{{ verse[0] }}</b-badge>
              <b-badge class="chap_badges" pill>{{ verse[1] }}</b-badge>
              <b-badge class="verse_badges" pill>{{
                verse[2].split(" ")[0]
              }}</b-badge>
              {{ verse[2].split(" ").splice(1).join(" ") }}</b-list-group-item
            >
          </b-list-group>
        </div>
      </b-row>
    <hr>
      <b-row>
        <Rect_D3
          v-if="freq_status"
          :key="String(this.rect_key) + 'rect'"
          :data_="displayable_verses"
          :title_="title_selection"
        ></Rect_D3>
      </b-row>
      <hr>
      <b-row>
        <Distplot_style
          v-if="freq_status"
          :key="String(this.dist_plot_key) + 'dist'"
          :tokens_="this.grouped_tokens"
          :title_="title_selection"
        ></Distplot_style>
      </b-row>
      <hr>
      <b-row>
        <Scatterplot_freq
          v-if="freq_status"
          :key="String(this.freq_plot_key) + 'freq'"
          :freq_="this.freq_"
          :title_="title_selection"
        ></Scatterplot_freq>
      </b-row>
      <hr>
      <b-row>
        <Scatter_3D
          v-if="freq_status"
          :key="String(this.scatter_3d_key) + 'scatter_3d'"
          :verses_="this.displayable_verses"
          :title_="title_selection"
        ></Scatter_3D>
      </b-row>
      <hr>
      <b-row>
        <Single_heatmap v-if="freq_status"
        :key="String(this.heat_key) + 'heatmap_'"
         :data_="this.filtered_data"
         :title_="title_selection"
        ></Single_heatmap>
      </b-row>
    </b-container>
  </div>
</template>
    
    <style>
.verse_badges,
.chap_badges {
  border-radius: 100%;
}
.verse_badges {
  background-color: blue;
}
.chap_badges {
  background-color: green;
}
.book_badges {
  background-color: red;
}

#verse_selector {
  background-color: rgba(15, 10, 222, 0.5);
}

#chap_selector {
  background-color: rgba(0, 230, 5, 0.5);
}
#book_selector {
  background-color: rgba(207, 0, 15, 0.5);
}
#folder_selector{
  margin-left: 10rem;
}

.list_item:hover {
  background-color: rgba(241, 214, 147, 0.5);
}

#verses {
  width: 100%;
  height: 30rem;
  overflow: hidden;
  overflow-y: scroll;
}

.il_coord {
  background-color: rgba(255, 0, 0, 0.301);
}
.il_subord {
  background-color: rgba(0, 0, 255, 0.264);
}
#coord_ {
  background-color: rgba(255, 0, 0, 0.15);
  border: 1px rgba(0, 0, 0, 0.392) solid;
}
#subord_ {
  background-color: rgba(0, 0, 255, 0.15);
  border: 1px rgba(0, 0, 0, 0.392) solid;
}
#neutral_,
#tot_ {
  border: 1px rgba(0, 0, 0, 0.392) solid;
}
.selects_handler{
  position:fixed;
  z-index: 999;
  background-color: whitesmoke;
  width:71rem;
  box-shadow: 0 2px 15px rgba(71, 120, 120, 0.5);
  transform: translate3d(0, 0, 0);
  transition: 0.1s all ease-out;
  
}

.selects_handler.selects_handler--hidden {
  display:none;
  box-shadow: none;
  transform:translate3d(0,-100%, 0)
}
.select_btn {
  background-color: lightgray;
  margin-left:-5rem;
  position: fixed;
}

.select_btn_active {
  background-color: orange;
  margin-left:-5rem;
  position: fixed;
}



#verses{
  margin-top: 5rem;
}

hr{
  border:1px solid grey !important
}
</style>