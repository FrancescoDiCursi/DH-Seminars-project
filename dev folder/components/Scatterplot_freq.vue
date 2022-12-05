<script>
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
export default {
  name: "Scatterplot_freq",
  props: { freq_: Object, title_:String},
  data() {
    return {
      x_: [],
      y_: [],
      filt_type: "Frequency",
      filt_options: ["Frequency", "N items"],
      start_flag: true,
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
      colors:[]
    };
  },
  mounted() {
    console.log(this.freq_);
    this.x_ = Object.keys(this.freq_);
    //sort reverse
    var temp_vals = [];
    for (var each of this.x_) {
      temp_vals.push([each, this.freq_[each]]);
    }

    temp_vals = temp_vals.sort(function (a, b) {
      return b[1] - a[1];
    });
    console.log(temp_vals);

    this.x_ = temp_vals.map((d) => d[0]);

    this.y_ = temp_vals.map((d) => d[1]);
    console.log(this.x_, this.y_);
    //colors
    var coords_=[]
    var subords_=[]

    for(let i=0;i<this.x_.length;i++){
      var coord_counter=0
      for(let j=0;j<this.coord_conj.length;j++){
        if (this.coord_conj[j]==this.x_[i].toLowerCase()){
         coord_counter+=1
        }
      }

        var subord_counter=0
        for(let j=0;j<this.subord_conj.length;j++){
          if(this.subord_conj[j]==this.x_[i].toLowerCase()){
            subord_counter+=1
          }
        }
      

      coords_.push(coord_counter)
      subords_.push(-Math.abs(subord_counter))

      var colors_temp = coords_.map((d, i) => d + subords_[i]);

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
    }

    console.log(color_vals)
    this.colors=color_vals
  },
  methods: {
    check_value() {
      console.log(document.getElementById("number_input_freq_plot").value);
    },
    draw_scatter() {
      var filt_value = document.getElementById("number_input_freq_plot").value;
      if (this.start_flag == false) {
        if (this.filt_type == this.filt_options[1]) {
          var filt_x = this.x_.slice(0, filt_value);
          var filt_y = this.y_.slice(0, filt_value);
          var filt_colors=this.colors.slice(0,filt_value)
        } else if (this.filt_type == this.filt_options[0]) {
          var filt_x = [];
          var filt_y = [];
          var filt_colors=[]
          for (let i = 0; i < this.x_.length; i++) {
            if (this.y_[i] <= filt_value) {
              filt_x.push(this.x_[i]);
              filt_y.push(this.y_[i]);
              filt_colors.push(this.colors[i])
            }
          }
        }
      } else if(this.start_flag=true){
        var filt_x = this.x_;
        var filt_y = this.y_;
        var filt_colors=this.colors
      }
var traces=[]
      var coord_filt=filt_colors.map(function(d,i){
        if(d=='coord'){
          return [filt_x[i],filt_y[i],'Coord','red']
        }
        else{
          return ''
        }
      })
      var subord_filt=filt_colors.map(function(d,i){
        if(d=='subord'){
          return [filt_x[i],filt_y[i],'Subord','blue']
        }else{
          return ''
        }
      })
      var neutral_filt= filt_colors.map(function(d,i){
        if(d=='neutral'){
          return [filt_x[i],filt_y[i],'Neutral','grey']
        }else{
          return ''
        }
      })

      coord_filt=coord_filt.filter(function(d){
        if(d!=''){
          return d
        }})

        subord_filt=subord_filt.filter(function(d){
        if(d!=''){
          return d
        }})

        neutral_filt=neutral_filt.filter(function(d){
        if(d!=''){
          return d
        }})

      var temp_data=[coord_filt,subord_filt,neutral_filt]
      var line_colors=['red','blue','grey']
      var trace_names=['Coord','Subord','Neutral']

      console.log(temp_data,coord_filt,subord_filt,neutral_filt)
      for(let i=0;i<3;i++){
      var trace = {
        name:trace_names[i],
        x: temp_data[i].map(d=>d[0]),
        y: temp_data[i].map(d=>d[1]),
        mode: "lines+markers",
        type: "scatter",
        marker:{
          color:temp_data[i].map(d=>d[3])
        },
        line:{
          color:line_colors[i]
        }
      };
      traces.push(trace)
    }
      console.log(traces)
      var layout = {
        title:'<b>'+this.title_+'</b>',
        yaxis:{
          categoryorder:'total descending'
        }
      };
      Plotly.newPlot("freq_plot_single", traces, layout);
      this.start_flag = false;
    
    },
  },

  watch: {
    colors: function () {
      this.draw_scatter();
    },
  },
};
</script>

<template>
  <b-container>
    <b-row>
      <b-col>
        <b-form inline>
          <b-form-group v-slot="{ ariaDescribedby }">
            <b-form-radio-group
              v-model="filt_type"
              :options="filt_options"
              :aria-describedby="ariaDescribedby"
              name="radio-inline"
            ></b-form-radio-group>
          </b-form-group>
          <input
            id="number_input_freq_plot"
            :value="
              this.filt_type == this.filt_options[0]
                ? this.y_[0]
                : this.x_.length
            "
            type="number"
            min="1"
            :max="
              this.filt_type == this.filt_options[0]
                ? this.y_[0]
                : this.x_.length
            "
          />
          <b-button id="filt_btn_feq_plot" @click="draw_scatter()"
            >Redraw</b-button
          >
        </b-form></b-col
      >
    </b-row>
    <b-row>
      <b-col> <div id="freq_plot_single"></div></b-col>
    </b-row>
  </b-container>
</template>