<script>
import Vue from "vue";

import * as d3 from "https://cdn.skypack.dev/d3@7";
import * as unique from "array-unique-deep";
import Scatterplot_freq from "./Scatterplot_freq.vue";

export default {
  name: "Single_section",
  components: { Scatterplot_freq },
  data() {
    return {
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
      token_list:[],
      badge_chaps: [],
      start_flag: false,
      loading_status:true,
      freq_:{}, //count token's frequency
      freq_status:false,
      freq_plot_key:0,
      folders: [
        "Easy-to-Read Version__American",
        "Unlocked Literal Bible__",
        "Young's Literal Translation__archaic British",
      ],
      //target_files: "",
      files: [
        [
          /*Easy to read */ "All",
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
          "19-Psalm",
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
        [
          /*Unlocked Literal*/ "All",
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
        [
          /* Young's Literal Translation__archaic British*/ "All",
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
          "22-Song of Solomon",
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
      ],

      chaps: [],

      loaded_file: [],
    };
  },
  mounted() {
    //this.target_folder=this.folders[0]
    //crea file unici su python, uno per ogni edizione al massimo, se non uno totale
    Promise.all(this.folders.map((d) => d3.csv("./static/" + d + ".csv"))).then(
      (csvs) => {
        this.loaded_file.push(csvs[0]);
        this.loaded_file.push(csvs[1]);
        this.loaded_file.push(csvs[2]);
      }
    );
  },
  methods: {
    manual_update(val){
      val+=1
      console.log(val)
    },
    tokenize(){
      var temp_text=this.displayable_verses.join(' ') //remove verse idx and merge
      this.token_list=temp_text.split(/\W+/)
      console.log(this.token_list)

      //make x and y for freq plot
      var token_keys=[... new Set(this.token_list)]
      var freq_counter={}
      for(var each of this.token_list){
        if(isNaN(freq_counter[each])){
          freq_counter[each]=1
        }else{
          freq_counter[each]+=1
        }
      }
      this.freq_=freq_counter
      this.freq_status=true
      this.freq_plot_key+=1
      console.log(this.freq_plot_key)
      console.log(freq_counter)
      console.log(Object.keys(freq_counter), Object.values(freq_counter))

    },
   
    handle_verses() {
      this.freq_stats=false
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
          return d3;
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
            return d;
          }
        }
      });

      //there are dupliacate verse, take half the length
      temp_file = temp_file.slice(0, temp_file.length / 2);
      this.displayable_verses = temp_file.map((d) => [
        d["Book"],
        d["Chapter"],
        d["Verse"],
      ]);
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
      this.target_file = this.target_files[0];
      if (this.start_flag == true) {
        this.handle_verses();
        this.tokenize()
        this.loading_status=false
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
        this.tokenize()
        this.loading_status=false
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
        this.tokenize()
        this.loading_status=false
      }
    },
    target_verse: function () {
      //comment out "this.handle_verses" if don't want to load all verses at start
      this.handle_verses()
      this.start_flag = true;
      this.tokenize()
      this.loading_status=false
      
    },
    freq_: function () {
      console.log(this.freq_)
    },
  },
};
</script>
    
    <template>
  <div id="single_section" >
    <div>
      <b-spinner v-if="loading_status"
        id="single_section_spinner"
        label="Loading..."
      ></b-spinner>
      <span v-else></span>
    </div>
    SINGLE
    <b-container>
      <b-row>
        <b-form inline>
          <b-form-group>
            <b-form-select @click="this.loading_status=true"
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
      </b-row>
      <b-row>
        <div id="verses">
          <b-list-group>
            <b-list-group-item
              class="list_item"
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
    
      <b-row>
        <Scatterplot_freq v-if="freq_status" :key="this.freq_plot_key" :freq_="this.freq_"></Scatterplot_freq>
        <span v-else></span>
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

.list_item:hover {
  background-color: rgba(241, 214, 147, 0.5);
}

#verses {
  width: 100%;
  height: 30rem;
  overflow: hidden;
  overflow-y: scroll;
}


</style>