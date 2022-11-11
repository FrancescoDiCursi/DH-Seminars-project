<script>
import Vue from "vue";
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
import * as d3 from "https://cdn.skypack.dev/d3@7";
import Binary_Rect_D3 from "./Binary_Rect_D3.vue";

export default {
  name: "Binary_section",
  components: {Binary_Rect_D3},
  data() {
    return {
      temp_file_list: [],
      target_folder_1: "",
      target_folder_idx_1: "",
      target_folder_2: "",
      target_folder_idx_2: "",
      target_files: [],
      target_file: "",
      target_chaps: [],
      target_chap: "",
      target_verse: "",
      target_verses: [],
      displayable_verses_1: [],
      displayable_verses_2: [],
      token_list: [],
      badge_chaps: [],
      start_flag: false,
      loading_status: true,
      freq_: {}, //count token's frequency
      grouped_tokens: [], //same as displayable_tokens but with [2]=tokens //count grouped by book and chapter
      freq_status: false,
      freq_plot_key: 0,
      dist_plot_key: 0,
      scatter_3d_key: 0,
      rect_key: 0,
      col_target:'',
      folders: [
        "Orthodox Jewish Version",
        "Young's Literal Translation",
        "New World Translation",
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
    Promise.all(this.folders.map((d) => d3.csv("./static/" + d + ".csv"))).then(
      (csvs) => {
        this.loaded_file.push(csvs[0]);
        this.loaded_file.push(csvs[1]);
        this.loaded_file.push(csvs[2]);
        this.loaded_file.push(csvs[3]);
        this.loaded_file.push(csvs[4]);
      }
    );
  },
  methods: {
    tokenize(displayable_verses) {
      //temp_text is for scatter
      var temp_text = displayable_verses.map((d) => d[2]).join(" "); //only verses
      this.token_list = temp_text.split(/\W+/);
      console.log(this.token_list);
      console.log("VERSES", displayable_verses);

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
      var temp_grouped_verses = displayable_verses.map((d) => [
        d[0],
        d[1],
        d[2].split(/\W+/),
      ]);
      this.grouped_tokens=temp_grouped_verses
     
      this.freq_ = freq_counter;
      this.freq_status = true;
      this.freq_plot_key += 1;
      this.rect_key +=1
      this.dist_plot_key += 1;
      this.scatter_3d_key +=1
      console.log(this.freq_plot_key);
      console.log(freq_counter);
      console.log(Object.keys(freq_counter), Object.values(freq_counter));
    },

    handle_verses(displayable_verses,target_folder,target_file,target_chap,target_verse, bin_n) {
      this.freq_stats = false;
      displayable_verses = [];
      var temp_file =
        this.loaded_file[this.folders.indexOf(target_folder)];
      var temp_book = target_file;
      var temp_chap = target_chap;
      var temp_verse = target_verse;
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
            console.log(d['Verse'], d["Verse"].split(" ")[0])
            return d;
          }
        }
      });

      console.log(temp_file)


      
      /*this.displayable_verses*/var temp_displayable_verses = temp_file.map((d) => [
        d["Book"],
        d["Chapter"],
        d["Verse"],
      ]);
      
      console.log(temp_file)
//color
      var coords_vals = [];
      var subord_vals = [];

      var verses_tokens=temp_file.map(d=>d['Verse'].split(' '))
      console.log(verses_tokens)

      for (let i = 0; i < temp_file.length; i++) {
        var counter_coords = 0;
        for (let j = 0; j < verses_tokens[i].length; j++) {
          //console.log(verses_tokens[i],verses_tokens[i][j])
          if (this.coord_conj.includes(verses_tokens[i][j].toLowerCase())) {
            counter_coords += 1;
          }
        }
        var counter_subords = 0;
        for (let j = 0; j <verses_tokens[i].length; j++) {
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

      var color_vals=[]
      //normalize
      for(let i=0;i<colors_temp.length;i++){
        if (colors_temp[i]==0){
          color_vals.push('neutral')
        }else if(colors_temp[i]>0){
          color_vals.push('coord')
        }else if(colors_temp[i]<0){
          color_vals.push('subord')
        }
      }
      displayable_verses=temp_displayable_verses.map((d,i)=>[d[0],d[1],d[2],color_vals[i]])
      if(bin_n=='1'){
        this.displayable_verses_1=displayable_verses
      }else if(bin_n=='2'){
        this.displayable_verses_2=displayable_verses
      }
      
      console.log("VALS", coords_vals, subord_vals, color_vals, temp_displayable_verses);
    },
  },
  watch: {
    loaded_file: function () {
      console.log("changed", this.loaded_file);
      //this.target_files=[... new Set(this.loaded_file[this.folders.indexOf(this.target_folder)].map(d=>d['Book']))]
      this.target_folder_1 = "";
      this.target_folder_1 = this.folders[0];
      this.target_folder_2=""
      this.target_folder_2=this.folders[1]
    },
    target_folder_1: function () {
        this.col_target='1'
      this.target_files = [
        "All",
        ...new Set(
          this.loaded_file[this.folders.indexOf(this.target_folder_1)].map(
            (d) => d["Book"]
          )
        ),
      ];
      this.target_file = "";
      this.target_file = this.target_files[1];
      this.target_chap='All'
      this.target_verse='All'
      if (this.start_flag == true) {
        this.handle_verses(this.displayable_verses_1,this.target_folder_1,this.target_file,this.target_chap,this.target_verse,'1');
      this.handle_verses(this.displayable_verses_2,this.target_folder_2,this.target_file,this.target_chap,this.target_verse,'2');
      this.tokenize(this.displayable_verses_1);
      this.tokenize(this.displayable_verses_2);
        this.loading_status = false;
      }
    },
    target_folder_2: function () {
        this.col_target='2'
      this.target_files = [
        "All",
        ...new Set(
          this.loaded_file[this.folders.indexOf(this.target_folder_2)].map(
            (d) => d["Book"]
          )
        ),
      ];
      this.target_file = "";
      this.target_file = this.target_files[1];
      this.target_chap='All'
      this.target_verse='All'
      if (this.start_flag == true) {
        this.handle_verses(this.displayable_verses_1,this.target_folder_1,this.target_file,this.target_chap,this.target_verse,'1');
      this.handle_verses(this.displayable_verses_2,this.target_folder_2,this.target_file,this.target_chap,this.target_verse,'2');
      this.tokenize(this.displayable_verses_1);
      this.tokenize(this.displayable_verses_2);
        this.loading_status = false;
      }
    },
    target_file: function () {
        const handleTargetCol=()=>this.col_target==='1' ?this.target_folder_1 :this.target_folder_2
        const handleDisplayable=()=>this.col_target==='1' ?this.displayable_verses_1 :this.displayable_verses_2
      var temp_chaps = this.loaded_file[
        this.folders.indexOf(handleTargetCol())
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
        this.handle_verses(this.displayable_verses_1,this.target_folder_1,this.target_file,this.target_chap,this.target_verse,'1');
      this.handle_verses(this.displayable_verses_2,this.target_folder_2,this.target_file,this.target_chap,this.target_verse,'2');
      this.tokenize(this.displayable_verses_1);
      this.tokenize(this.displayable_verses_2);
        this.loading_status = false;
      }
    },
    target_chap: function () {
        const handleTargetCol=()=>this.col_target==='1' ?this.target_folder_1 :this.target_folder_2
        const handleDisplayable=()=>this.col_target==='1' ?this.displayable_verses_1 :this.displayable_verses_2
      var temp_verses = this.loaded_file[
        this.folders.indexOf(handleTargetCol())
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
        this.handle_verses(this.displayable_verses_1,this.target_folder_1,this.target_file,this.target_chap,this.target_verse,'1');
      this.handle_verses(this.displayable_verses_2,this.target_folder_2,this.target_file,this.target_chap,this.target_verse,'2'); 
      this.tokenize(this.displayable_verses_1);
      this.tokenize(this.displayable_verses_2);
        this.loading_status = false;
      }
    },

    target_verse: function () {
        const handleTargetCol=()=>this.col_target==='1' ?this.target_folder_1 :this.target_folder_2
        const handleDisplayable=()=>this.col_target==='1' ?this.displayable_verses_1 :this.displayable_verses_2
      //comment out "this.handle_verses" if don't want to load all verses at start
      this.handle_verses(this.displayable_verses_1,this.target_folder_1,this.target_file,this.target_chap,this.target_verse,'1');
      this.handle_verses(this.displayable_verses_2,this.target_folder_2,this.target_file,this.target_chap,this.target_verse,'2');

      console.log(this.verses)
      this.start_flag = true;
      this.tokenize(this.displayable_verses_1);
      this.tokenize(this.displayable_verses_2);
      this.loading_status = false;
      console.log(handleDisplayable())
    },
    freq_: function () {
      console.log(this.freq_);
    },
  },
};
</script>
    
    <template>
  <div id="binary_page">
    <b-container>
      <b-row>
        <b-col>
          <b-form >
            <b-form-group>
              <b-form-select
                @click="this.loading_status = true"
                v-model="target_folder_1"
                :options="folders"
                id="folder_selector_1"
              ></b-form-select>

            </b-form-group>
          </b-form>
        </b-col>
        <b-col>
          <b-form>
            <b-form-group>
              <b-form-select
                @click="this.loading_status = true"
                v-model="target_folder_2"
                :options="folders"
                id="folder_selector_2"
              ></b-form-select>

            </b-form-group>
          </b-form>
        </b-col>
      </b-row>
      <b-row>
        <b-col>
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
        </b-col>
      </b-row>
      <b-row>
     <Binary_Rect_D3  :key="String(this.rect_key)+'rect'" :data_="displayable_verses_1" :data_2="displayable_verses_2"></Binary_Rect_D3>
      </b-row>
      <b-row>
        
        <b-col>
            <div id="verses_1">
          <b-list-group>
            <b-list-group-item
              :class="'il_'+displayable_verses_1[i][3]"
              v-for="(verse, i) in this.displayable_verses_1"
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
        </b-col>
        <b-col>
        <div id="verses_2">
          <b-list-group>
            <b-list-group-item
              :class="'il_'+displayable_verses_2[i][3]"
              v-for="(verse, i) in this.displayable_verses_2"
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
    </b-col>
      </b-row>
    </b-container>
  </div>
</template>
    
    <style>
    #binary_page{
        width:100%;
        height:1000px
    }
    #verses_1,#verses_2{
        width: 100%;
  height: 30rem;
  overflow: hidden;
  overflow-y: scroll;

    }
</style>