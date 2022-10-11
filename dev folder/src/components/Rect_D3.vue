<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";

export default {
    name:'Rect_D3',
    props:{data_:Array,legend_vals:Array},
    data(){
        return{
            coord_perc:0,
            coord_val:0,
            subord_perc:0,
            subord_val:0,
            neutral_perc:0,
            neutral_val:0,
            tot:100
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

        this.legend_vals=[coord_val,subord_val,neutral_val]
    },
    methods:{
        draw_rect(){
            var traces=[]
            var l_=[this.coord_perc,this.subord_perc,this.neutral_perc]
            var names_=['Coord','Subord','Neutral']
            var colors_=['red','blue','grey']
            var temp_legend_vals=this.legend_vals
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
            title:'<b>Total: '+String(this.data_.length)+'</b>',
            barmode:'stack',
            annotations:[],
            width:1000,
            height:300,
            xaxis:{
                visible:false
            },
            yaxis:{
                visible:false
            }
        }

        Plotly.newPlot('hbar_perc',traces,layout)
        }
    },
    watch:{
        legend_vals:function(){
           this.draw_rect()
        }
    }

}
</script>

<template>

                <div id="hbar_perc"></div>

</template>

<style>

</style>