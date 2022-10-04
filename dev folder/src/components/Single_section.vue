<script>
import Vue from "vue";
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
import * as d3 from "https://cdn.skypack.dev/d3@7";
import * as unique from "array-unique-deep";

export default {
  name: "Single_section",
  components: {},
  data() {
    return {
      temp_file_list:[],
      target_folder: "",
      target_folder_idx: "",
      target_files: [],
      target_file: "",
      target_chaps: [],
      target_chap: "",
      target_verse:"",
      target_verses:[],
      displayable_verses:[],
      folders: ["Easy-to-Read Version__American", "Unlocked Literal Bible__","Young's Literal Translation__archaic British"],
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
        [/* Young's Literal Translation__archaic British*/ "All",
        '1-Genesis',
 '2-Exodus',
 '3-Leviticus',
 '4-Numbers',
 '5-Deuteronomy',
 '6-Joshua',
 '7-Judges',
 '8-Ruth',
 '9-1 Samuel',
 '10-2 Samuel',
 '11-1 Kings',
 '12-2 Kings',
 '13-1 Chronicles',
 '14-2 Chronicles',
 '15-Ezra',
 '16-Nehemiah',
 '17-Esther',
 '18-Job',
 '19-Psalms',
 '20-Proverbs',
 '21-Ecclesiastes',
 '22-Song of Solomon',
 '23-Isaiah',
 '24-Jeremiah',
 '25-Lamentations',
 '26-Ezekiel',
 '27-Daniel',
 '28-Hosea',
 '29-Joel',
 '30-Amos',
 '31-Obadiah',
 '32-Jonah',
 '33-Micah',
 '34-Nahum',
 '35-Habakkuk',
 '36-Zephaniah',
 '37-Haggai',
 '38-Zechariah',
 '39-Malachi',
 '40-Matthew',
 '41-Mark',
 '42-Luke',
 '43-John',
 '44-Acts',
 '45-Romans',
 '46-1 Corinthians',
 '47-2 Corinthians',
 '48-Galatians',
 '49-Ephesians',
 '50-Philippians',
 '51-Colossians',
 '52-1 Thessalonians',
 '53-2 Thessalonians',
 '54-1 Timothy',
 '55-2 Timothy',
 '56-Titus',
 '57-Philemon',
 '58-Hebrews',
 '59-James',
 '60-1 Peter',
 '61-2 Peter',
 '62-1 John',
 '63-2 John',
 '64-3 John',
 '65-Jude',
 '66-Revelation']
      ],

      chaps: [],

      loaded_file: [],
    };
  },
  mounted() {
    this.target_folder = this.folders[0];
  },
  methods: {
    read_file(dirname) {
      //this.loaded_file=[]
      
      if (this.target_file == "All") {
        var file_list = this.target_files.slice(1);
        
      } else {
        var file_list = [this.target_file];
        
      }
      console.log(file_list);
      Promise.all(
        file_list.map((f) => d3.csv("./static/" + dirname + "/" + f + ".csv"))
        
      ).then((csvs) => {
        //this.loaded_file=[]
        csvs.map((f,i) => this.loaded_file.push(f));
        console.log("LOADED", this.loaded_file[-1]);
      });
      
      //this.target_chap = "";
      //this.target_chaps = [];
      if (this.target_file == "All") {
              this.loaded_file=[]
        console.log("bback");
        return;
      } else {
        //handle chapters
        var chap_list_temp = this.loaded_file.map(d=>d.map(y=>y['Chapter']))
        
        console.log(chap_list_temp);
        chap_list_temp = [
          ...new Set(
            chap_list_temp[this.target_files.indexOf(this.target_file)-1] //due to All at 0 in book handler
          ),
        ];
        this.target_chaps = chap_list_temp;
        this.target_chap = chap_list_temp[0];

        //handle verses
        

        
      }
    },
    handle_verses(){
        console.log('starting handle verses',this.loaded_file)
        console.log('INFOS', this.target_folder, this.target_folder_idx, this.target_file, this.target_chap, this.target_verse)
        if (this.target_file!='All'){
            
            
            var temp_edition=this.target_folder
            var temp_book=this.target_file.split('-').slice(1,)
            var temp_chap=this.target_chap
            
            console.log(temp_book)
            
            var temp_filt=this.loaded_file.map(d=>d.filter(function(y){
              if (y['Edition']==temp_edition){
              if(y['Book']==temp_book){
              if (y['Chapter']==String(temp_chap)){
                
                return y
              }
              else{
                return ''
              }
            }}}))

            temp_filt=temp_filt.filter(function(y){
              if (y.length>0){
                return y
              }
            })

            temp_filt=temp_filt[0] //in case of duplicate dfs //need to understand how to empty the loaded files, now works only if All is selected as chapter
            var temp_verses=temp_filt.map(d=>d['Verse'])
            this.target_verses=temp_verses.map(d=>d.split(' ')[0])//verses idx

            //CONTINUA DA QUI: ULTIMO FILTRO PER I VERSI
           var temp_target_verse=this.target_verse
            console.log(temp_target_verse)
            var filtered_verses=temp_verses.filter(function(d){
              if (d.split(' ')[0]==String(temp_target_verse)){
                return d
              }
            })
            this.displayable_verses=filtered_verses
            
            //this.displayable_verses=temp_verses

            console.log('VERSES',temp_verses)
        }
        else{
          var temp_filt=this.loaded_file.map(d=>d.filter(function(y){
              if (y['Edition']==temp_edition){
              if(y['Book']==temp_book){
                return y
              }}}))
              temp_filt=temp_filt.filter(function(y){
              if (y.length>0){
                return y
              }
            })

            temp_filt=temp_filt[0] //in case of duplicate dfs //need to understand how to empty the loaded files, now works only if All is selected as chapter
            var temp_verses=temp_filt.map(d=>d['Verse'])
            this.target_verses=temp_verses.map(d=>d.split(' ')[0])//verses idx

            this.target_verses=temp_filt
            this.target_verse=this.target_verse[0]
        }
        




    }
  },
  watch: {


    target_folder: function () {
        console.log('changed targert')
        this.target_verses=[]
        this.loaded_file=[]
        this.target_files=[]
        console.log(this.loaded_files)
      this.target_folder_idx = this.folders.indexOf(this.target_folder);
      this.target_files = this.files[this.target_folder_idx];
      this.target_file = this.target_files[0]; //0==All
      //this.target_chaps = this.chaps[this.target_chap];
      //this.target_chap = this.target_chaps;
    },
    target_file: function () {

      if (this.target_file=='All'){
        this.target_chaps=[]
        this.target_verses=[]
        this.displayable_verses=[]
      }
      // this.target_file_idx= this.files.indexOf(this.target_file) //All at 0
      //this.target_chaps = this.chaps[this.target_chap];
      //this.target_chap = this.target_chaps;

      console.log("folder changed");
      this.read_file(this.target_folder);
      //CAPIRE COME SVUOTARE LOADED_FILE SENZA FAR CRASHARE TUTTO
      //this.loaded_file=[]

      console.log('changed')


    },
    target_chap: function(){
        this.target_verses=[]
        this.target_verse=this.target_verses[0]
        this.displayable_verses=[]
        console.log('changed')
        this.handle_verses()
        

    },
    target_verse: function(){
        console.log('verse changed')
        this.handle_verses()
    },
  },
};
</script>

<template>
  <div id="single_page">
    SINGLE

    <b-form>
      <b-form-group>
        <b-form-select
          v-model="target_folder"
          :options="folders"
          id="folder_selector"
        ></b-form-select>
        <b-form-select
          v-model="target_file"
          :options="target_files"
          id="file_selector"
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

    <div id="verses">
      <b-list-group>
        <b-list-group-item v-for="(verse,i) in this.displayable_verses" :key="i+verse">
          <b-badge class="badges" variant="danger" :name="i" pill>{{verse.split(' ')[0]}}</b-badge>
          {{verse.split(' ').splice(1,).join(' ')}}</b-list-group-item>
      </b-list-group>
    </div>
  </div>
</template>

<style>
  .badges{
    background-color: blue;
    border-radius: 100%;
    
  }
  .badges:hover{
    background-color: red
  }
</style>