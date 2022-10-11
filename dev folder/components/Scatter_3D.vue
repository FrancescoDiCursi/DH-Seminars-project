<script>
import arrayUniqueDeep from "array-unique-deep";
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
import * as d3 from "https://cdn.skypack.dev/d3@7";

export default {
  name: "Scatter_3D",
  props: { verses_: Array },
  data() {
    return {
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
      trigrams_: [],
      verses_idx:[],
      trigrams_freq_temp:[],
      colors_temp:[],
      displayable_trigrams_temp:[],

      trigrams_freq:[],
      colors:[],
      displayable_trigrams:[],
      filt_type:'All',
      filt_options:['All','Coord','Subord'],
      annot_status:'Show annots',
      annot_options:['Show annots','Hide annots'],
      scale_option:'Absolute values',
      scale_options:['Absolute values','Scaled values']
    };
  },
  mounted() {
    //this.scale_option=this.scale_options[0]
    var trigrams_temp = this.verses_.map((d) => [
      ...d[2]
        .split(" ")
        .slice(1)
        .map((r) => r.replace(/\W+/, " ").replace("”", " ").replace("'", " "))
        .map((r) => r.split(" ")),
    ]);

    var trigrams_temp2 = [];
    for (let i = 0; i < trigrams_temp.length; i++) {
      var temp_l = [];
      for (let j = 0; j < trigrams_temp[i].length; j++) {
        //console.log(trigrams_temp[i][j])
        for (let k = 0; k < trigrams_temp[i][j].length; k++) {
          if (trigrams_temp[i][j][k] != "") {
            temp_l.push(trigrams_temp[i][j][k]);
          }
        }
      }
      trigrams_temp2.push(temp_l);
    }

    console.log("trig 2,", trigrams_temp2);

    var trigrams_res = [];
    for (let i = 0; i < trigrams_temp2.length; i++) {
      var temp_l = [];
      for (let j = 0; j < trigrams_temp2[i].length; j++) {
        if (j <= trigrams_temp2[i].length - 1 - 3) {
          temp_l.push([
            trigrams_temp2[i][j],
            trigrams_temp2[i][j + 1],
            trigrams_temp2[i][j + 2],
          ]);
        }
      }
      trigrams_res.push(temp_l);
    }
    console.log(this.verses_, trigrams_res);
    this.trigrams_ = trigrams_res;
  },
  methods: {
    draw_scatter() {
      var x_ = [];
      this.trigrams_.map((d) => d.map((r) => x_.push(r[0])));
      var y_ = [];
      this.trigrams_.map((d) => d.map((r) =>  y_.push(r[1]) ));
      var z_ = [];
      this.trigrams_.map((d) => d.map((r) =>   z_.push(r[2])));

      var sizes_dict = {};
      var temp_trigrams = x_.map((d, i) => [d, y_[i], z_[i]]);
      var dict_keys = [...new Set(temp_trigrams)];
      for (var i = 0; i < temp_trigrams.length; i++) {
        if (isNaN(sizes_dict[temp_trigrams[i]])) {
          sizes_dict[temp_trigrams[i]] = 1;
        } else {
          sizes_dict[temp_trigrams[i]] += 1;
        }
      }

      var sizes_ = temp_trigrams.map(d=>sizes_dict[d]);
      console.log("DICT", sizes_dict);

      //color


      console.log("a", this.trigrams_);

     // var temp_trigrams = this.trigrams_;

      var trigrams_dict_coord={}

      var trigrams_dict_subord={}

      var dict_keys_coord=[... new Set(temp_trigrams.map(d=>d.map(y=>y)))]
      dict_keys_coord.map(d=>trigrams_dict_coord[d]=1)

      var dict_keys_subord=[... new Set(temp_trigrams.map(d=>d.map(y=>y)))]
      dict_keys_subord.map(d=>trigrams_dict_subord[d]=1)

      console.log(trigrams_dict_coord,trigrams_dict_subord)
      for (let i = 0; i < temp_trigrams.length; i++) {
        //console.log('b')
        for (let j = 0; j < this.coord_conj.length; j++)  {
          //console.log('c')
          
            if (
              temp_trigrams[i].includes(this.coord_conj[j].toLowerCase())
            ) {
              trigrams_dict_coord[temp_trigrams[i]]+= 1;
            }
          
        }
        for (let j = 0; j < this.subord_conj.length; j++) {
          
            if (
              temp_trigrams[i].includes(this.subord_conj[j].toLowerCase())
            ) {
              trigrams_dict_subord[temp_trigrams[i]]+= 1
            
          }
        }

      }

      var coords_vals = temp_trigrams.map(d=>trigrams_dict_coord[d]);
      var subord_vals = temp_trigrams.map(d=>-Math.abs(trigrams_dict_subord[d]));;

      console.log(coords_vals, subord_vals);

      var colors_temp = coords_vals.map((d, i) => d + subord_vals[i]);
      var colors_=[]
      //normalize
      for(let i=0;i<colors_temp.length;i++){
        if (colors_temp[i]==0){
          colors_.push(0)
        }else if(colors_temp[i]>0){
          colors_.push(1)
        }else if(colors_temp[i]<0){
          colors_.push(-1)
        }
      }
      //var colors_set=[... new Set(colors_)].sort((a,b)=>a-b)
      //var min_=colors_set[0]
      //var max_=colors_set[colors_set.length-1]
      var text_=temp_trigrams.map((d,i)=>'Color:'+String(colors_[i])+' Size:'+String(sizes_[i]))
      console.log(x_,y_,z_,colors_,sizes_,text_)
      var trigrams_for_arrows=x_.map((d,i)=>[x_[i],y_[i],z_[i],colors_[i],sizes_[i]])
      var max_coord_trigram=trigrams_for_arrows.filter(function(d){
        if (d[3]==1){
         return d
        }
      })

      max_coord_trigram=max_coord_trigram.filter(function(d){
        if (d[4]==Math.max.apply(Math,max_coord_trigram.map(d=>d[4]))){
          return d
        }
      })
      max_coord_trigram=max_coord_trigram[0]

      var max_subord_trigram=trigrams_for_arrows.filter(function(d){
        if (d[3]==-1){
         return d
        }
      })

      max_subord_trigram=max_subord_trigram.filter(function(d){
        if (d[4]==Math.max.apply(Math,max_subord_trigram.map(d=>d[4]))){
          return d
        }
      })
      console.log(max_subord_trigram)
      max_subord_trigram=max_subord_trigram[0]

      var max_neutral_trigram=trigrams_for_arrows.filter(function(d){
        if (d[3]==0){
         return d
        }
      })

      max_neutral_trigram=max_neutral_trigram.filter(function(d){
        if (d[4]==Math.max.apply(Math,max_neutral_trigram.map(d=>d[4]))){
          return d
        }
      })
      max_neutral_trigram=max_neutral_trigram[0]

      console.log(trigrams_for_arrows,max_coord_trigram,max_subord_trigram,max_neutral_trigram)

      var arrows_subj=[max_coord_trigram,max_subord_trigram,max_neutral_trigram]

      //var colorscale = d3.scaleDivergingSymlog([-1, 0, 1], d3.interpolatePiYG);

      console.log("COLORS", colors_,);

      //annot rows
      var annot_temp=[
            //axis
            {
              'x':0,
              'y':0,
              'z':0,
              ax:100,
              arrowcolor:'black',
              arrowhead:4,
              showarrow:true,
              text:'0 point'

            },
            //coord
            {
              'x':max_coord_trigram[0],
              'y':max_coord_trigram[1],
              'z':max_coord_trigram[2],
              arrowcolor:'red',
              font:{
                color:'red',
                size:12,
              },
              text:`<b>${max_coord_trigram.slice(0,3).join(' ')} (${max_coord_trigram[4]})</b>`,
              bgcolor:'rgba(255,0,0,0.3)',
              bordercolor:'black'
            },//subord
            {
              'x':max_subord_trigram[0],
              'y':max_subord_trigram[1],
              'z':max_subord_trigram[2],
              arrowcolor:'blue',
              font:{
                color:'blue',
                size:12,
              },
              text:`<b>${max_subord_trigram.slice(0,3).join(' ')} (${max_subord_trigram[4]})</b>`,
              bgcolor:'rgba(0,0,255,0.3)',
              bordercolor:'black'

            },//neutral
            {
              'x':max_neutral_trigram[0],
              'y':max_neutral_trigram[1],
              'z':max_neutral_trigram[2],
              arrowcolor:'grey',
              font:{
                color:'grey',
                size:12,
              },
              text:`<b>${max_neutral_trigram.slice(0,3).join(' ')} (${max_neutral_trigram[4]})</b>`,
              bgcolor:'rgba(210, 210, 210,0.6)',
              bordercolor:'black'
            }
          ]
      var annot_=[]
      //set of points
      var temp_data_unfilt=[... new Set(x_.map((d,i)=>[x_[i],y_[i],z_[i],colors_[i],sizes_[i],text_[i]].join('§')))].map(d=>d.split('§'))
      console.log(temp_data_unfilt)
      var temp_data=[]
      if(this.filt_type=='Coord'){
        temp_data=temp_data_unfilt.filter(function(d){
          if(d[3]=='1'){
            return d
          }
        })
        if (max_coord_trigram!=[]){
          annot_=[annot_temp[0],annot_temp[1]]
          annot_[0]['x']=temp_data[0][0]
          annot_[0]['y']=temp_data[0][1]
          annot_[0]['z']=temp_data[0][2]

        }
      } else  if(this.filt_type=='Subord'){
        temp_data=temp_data_unfilt.filter(function(d){
          if(d[3]=='-1'){
            return d
          }
        })
        if (max_subord_trigram!=[]){
          annot_=[annot_temp[0],annot_temp[2]]
          annot_[0]['x']=temp_data[0][0]
          annot_[0]['y']=temp_data[0][1]
          annot_[0]['z']=temp_data[0][2]

        }
      } else if(this.filt_type=='All'){
        temp_data=temp_data_unfilt
        annot_=annot_temp
        annot_[0]['x']=temp_data[0][0]
          annot_[0]['y']=temp_data[0][1]
          annot_[0]['z']=temp_data[0][2]
      }

      if(this.annot_status!='Show annots'){
        annot_=[]
      }
  
      console.log(x_, temp_data,annot_)







      var trace1 = {
        x:temp_data.map(d=>d[0]),
        y:temp_data.map(d=>d[1]),
        z:temp_data.map(d=>d[2]),

        mode: "markers",
        type: "scatter3d",
        text: temp_data.map(d=>d[5]),
        surfacecolor:'black',
        marker: {
       
          colorbar: {
            orientation: "h",

          },
          cmin:-1,
          cmid:0,
          cmax:1,
             color: temp_data.map((d) => +d[3]),
          size: temp_data.map((d) => this.scale_option=='Scaled values' ?Math.sqrt(+d[4])*5 :+d[4]),
        },
      };
      var trace2 = {
        alphahull:9,
        opacity: 0.03,
        type: 'mesh3d',
        x: x_,
        y: y_,
        z: z_,
        
    };

      var layout = {
        automargin:true,
        autosize: true,
        showscale:true,
        height: 1200,
        scene: {
          aspectratio: {
            x: 1,
            y: 1,
            z: 1,
          },
          camera: {
            center: {
              x: 0,
              y: 0,
              z: 0,
            },
            eye: {
              x: 1.25,
              y: 1.25,
              z: 1.25,
            },
            up: {
              x: 0,
              y: 0,
              z: 1,
            },
          },
          xaxis: {
            type: "categorical",
            automargin:true
          },
          yaxis: {
            type: "categorical",
            automargin:true
          },
          zaxis: {
            type: "categorical",
            automargin:true
          },
          annotations:annot_
        },
      };
      console.log(x_.indexOf('cannot'),x_.indexOf('on'), x_[x_.length/2])
      var temp_verses_idx=[]
      var temp_disp_trigrams=[]
      var il_classes=[]
      for (var each of colors_){
        var res=''
        if(each==0){
          res='neutral'
        }else if(each==1){
          res='coord'
        }else if(each==-1){
          res='subord'
        }
        il_classes.push(res)
      }

      this.colors_temp=il_classes
      this.trigrams_freq_temp=sizes_
      this.trigrams_.map((d,i)=>d.map(r=>temp_verses_idx.push(i+1)))
      this.trigrams_.map(d=>d.map(r=>temp_disp_trigrams.push(r)))
      this.verses_idx=temp_verses_idx
      this.displayable_trigrams_temp=temp_disp_trigrams
      console.log( this.displayable_trigrams_temp)

      Plotly.newPlot("scatter_3D_single", [trace1], layout); //trace 2, mesh, makes it crash

      this.order_and_filter_list()
    },
    order_and_filter_list(){
     var temp_l_init= this.displayable_trigrams_temp.map((d,i)=>[d,this.colors_temp[i],this.trigrams_freq_temp[i]])
     var temp_l_init_str=temp_l_init.map(d=>d.join(' '))
     var temp_l=[... new Set(temp_l_init_str)].map(d=>d.split(' ')).map(d=>[d[0].split(','),d[1],d[2]])

     console.log(temp_l)

     var temp_l_coord=temp_l.filter(function(f){
      if(f[1]=='coord'){
        return f
      }
     })
     temp_l_coord=[... temp_l_coord].sort((a,b)=>a[2]-b[2])

     var temp_l_subord=temp_l.filter(function(f){
      if(f[1]=='subord'){
        return f
      }
     })
     temp_l_subord=[... temp_l_subord].sort((a,b)=>a[2]-b[2])

     temp_l=[... temp_l].sort((a,b)=>a[2]-b[2])
     console.log(temp_l)

     if(this.filt_type=='Coord'){
      temp_l_coord=temp_l_coord.reverse()
      this.displayable_trigrams=temp_l_coord.map(d=>d[0])
      this.colors=temp_l_coord.map(d=>d[1])
      this.trigrams_freq=temp_l_coord.map(d=>d[2])
      
     }else if(this.filt_type=='Subord'){
      temp_l_subord=temp_l_subord.reverse()
      this.displayable_trigrams=temp_l_subord.map(d=>d[0])
      this.colors=temp_l_subord.map(d=>d[1])
      this.trigrams_freq=temp_l_subord.map(d=>d[2])
      
     }else if (this.filt_type='All'){
      temp_l=temp_l.reverse()
      this.displayable_trigrams=temp_l.map(d=>d[0])
      this.colors=temp_l.map(d=>d[1])
      this.trigrams_freq=temp_l.map(d=>d[2])

     }

     console.log(this.displayable_trigrams, this.colors, this.trigrams_freq)
     
    }
  },

  watch: {
    trigrams_: function () {
      this.draw_scatter();
    },
    filt_type: function(){
      //filter and order list
      this.draw_scatter()

      this.order_and_filter_list()

    },
    annot_status: function(){
      this.draw_scatter()
    },
    scale_option: function(){
      this.draw_scatter()
    }
  },
};
</script>

<template>
  <b-container>
    <b-row>
      <b-form-group v-slot="{ ariaDescribedby }">
            <b-form-radio-group
              v-model="scale_option"
              :options="scale_options"
              :aria-describedby="ariaDescribedby"
              name="radio-inline-scale"
            ></b-form-radio-group>
          </b-form-group>
</b-row>
<b-row>
  <b-form-group v-slot="{ ariaDescribedby }">
            <b-form-radio-group
              v-model="annot_status"
              :options="annot_options"
              :aria-describedby="ariaDescribedby"
              name="radio-inline-anntos"
            ></b-form-radio-group>
          </b-form-group>
</b-row>
<b-row>
  <b-form-group v-slot="{ ariaDescribedby }">
            <b-form-radio-group
              v-model="filt_type"
              :options="filt_options"
              :aria-describedby="ariaDescribedby"
              name="radio-inline"
            ></b-form-radio-group>
          </b-form-group>
</b-row>

    <b-row>
      <b-col cols="11">
       
    <div id="scatter_3D_single"></div>
  </b-col>

      <b-col cols="1">
    <div id="top_trigrams">      
      <b-list-group>
      <b-list-group-item v-for="(trigram,i) in displayable_trigrams.slice(0,200)" :key="String(i)+trigram" :id="'trigram_il'+i" :class="'il_'+colors[i]">
        {{trigram.join(' ') + ' '+trigrams_freq[i] }}
      </b-list-group-item>
    </b-list-group>
  </div>
    </b-col>
  </b-row>

  
  </b-container>
</template>

<style>
.il_coord{
  background-color: rgba(255, 0, 0, 0.301);
}
.il_subord{
  background-color:rgba(0, 0, 255, 0.264);
}
#top_trigrams{
  height:900px;
  width:15rem;
  overflow: hidden;
  overflow-y: scroll;
  margin-left: auto;
  margin-right: auto
}
</style>