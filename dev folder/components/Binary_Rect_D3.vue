<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";

export default {
    name:'Binary_Rect_D3',
    props:{data_:Array, data_2:Array},
    data(){
        return{
            coord_perc:0,
            coord_val:0,
            subord_perc:0,
            subord_val:0,
            neutral_perc:0,
            neutral_val:0,

            coord_perc_2:0,
            coord_val_2:0,
            subord_perc_2:0,
            subord_val_2:0,
            neutral_perc_2:0,
            neutral_val_2:0,

            tot:100,
            legend_vals:[],
            legend_vals_2:[]
        }
    },
    mounted(){
        var temp_data=this.data_
        this.coord_perc=Math.round((temp_data.filter(function(d){
          if(d[3]=='coord'){
            return d
          }
        }).length /temp_data.length)*100)

        this.subord_perc=Math.round((temp_data.filter(function(d){
          if(d[3]=='subord'){
            return d
          }
        }).length / temp_data.length)*100)

        this.neutral_perc=Math.round((temp_data.filter(function(d){
          if(d[3]=='neutral'){
            return d
          }
        }).length /temp_data.length)*100)

        var coord_val=temp_data.filter(function(d){
          if(d[3]=='coord'){
            return d
          }
        }).length

       var subord_val=temp_data.filter(function(d){
          if(d[3]=='subord'){
            return d
          }
        }).length

        var neutral_val=temp_data.filter(function(d){
          if(d[3]=='neutral'){
            return d
          }
        }).length
        //vals2
        var temp_data_2=this.data_2
        this.coord_perc_2=Math.round((temp_data_2.filter(function(d){
          if(d[3]=='coord'){
            return d
          }
        }).length /temp_data_2.length)*100)

        this.subord_perc_2=Math.round((temp_data_2.filter(function(d){
          if(d[3]=='subord'){
            return d
          }
        }).length / temp_data_2.length)*100)

        this.neutral_perc_2=Math.round((temp_data_2.filter(function(d){
          if(d[3]=='neutral'){
            return d
          }
        }).length /temp_data_2.length)*100)

        var coord_val_2=temp_data_2.filter(function(d){
          if(d[3]=='coord'){
            return d
          }
        }).length

       var subord_val_2=temp_data_2.filter(function(d){
          if(d[3]=='subord'){
            return d
          }
        }).length

        var neutral_val_2=temp_data_2.filter(function(d){
          if(d[3]=='neutral'){
            return d
          }
        }).length
        this.legend_vals=[coord_val,subord_val,neutral_val]
        this.legend_vals_2=[coord_val_2,subord_val_2,neutral_val_2]
    },
    methods:{
        draw_rect(coord_perc,subord_perc,neutral_perc,legend_vals,data_,div_idx){
            var traces=[]
            var l_=[coord_perc,subord_perc,neutral_perc]
            var names_=['Coord','Subord','Neutral']
            var colors_=['red','blue','grey']
            var temp_legend_vals=legend_vals
            for(let i=0;i<3;i++){
            var trace={
                name:names_[i]+' '+temp_legend_vals[i],
                type:'bar',
                x:[l_[i]],
                y:['Bar'],
                orientation:'h',
                marker:{
                    color:colors_[i]
                },
                text:String(l_[i]+'%')
            }
            traces.push(trace)
        }
        
        var layout={
            title:'<b>Total: '+String(data_.length)+'</b>',
            barmode:'stack',
            annotations:[],
            //width:window.innerWidth/1.2,
            height:300,
            xaxis:{
                visible:false
            },
            yaxis:{
                visible:false
            }
        }

        Plotly.newPlot(div_idx,traces,layout)
        }
    },
    watch:{
        legend_vals_2:function(){
           this.draw_rect(this.coord_perc,this.subord_perc,this.neutral_perc,this.legend_vals,this.data_,'binary_rect_1')
           this.draw_rect(this.coord_perc_2,this.subord_perc_2,this.neutral_perc_2,this.legend_vals_2,this.data_2,'binary_rect_2')

        }
    }

}
</script>

<template>
<b-container>
  <b-row>
    <b-col><div id="binary_rect_1"></div></b-col>
    <b-col><div id="binary_rect_2"></div></b-col>

  </b-row>
</b-container>
                

</template>

<style>

</style>