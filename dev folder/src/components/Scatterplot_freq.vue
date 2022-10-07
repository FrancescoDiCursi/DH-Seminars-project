<script>
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
export default {
    name:'Scatterplot_freq',
    props:{freq_:Object,},
    data(){
        return{
            x_:[],
            y_:[],
        }
    },
    mounted(){
        this.x_=Object.keys(this.freq_)
        //sort reverse
        var temp_vals=[]
        for(var each of this.x_){
            temp_vals.push([each,this.freq_[each]])
        }

        temp_vals=temp_vals.sort(function(a,b){
            return b[1]-a[1]
        })
        console.log(temp_vals)

        this.x_=temp_vals.map(d=>d[0])
        
        this.y_=temp_vals.map(d=>d[1])
        console.log(this.x_,this.y_)
    },
    methods:{
        draw_scatter(){
            var trace={
                x:this.x_,
                y:this.y_,
                mode:'lines+markers',
                type:'scatter',

            }
            var layout={

            }
            Plotly.newPlot('freq_plot_single',[trace],layout)
        }

    },

watch:{
    y_:function(){
        this.draw_scatter()
    }
}
}
</script>

<template>
    <div id="freq_plot_single"></div>
</template>