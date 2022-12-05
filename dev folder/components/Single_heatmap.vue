<script>
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7.7.0/+esm";

export default {
  name: "Single_heatmap.vue",
  props: { data_: Array, title_:String },
  data() {
    return {
      plot_filter:Boolean,
      data_loaded: {},
      plot_option: "Blank",
      plot_options: ["Blank", "2D Heatmap", "3D Network"],
      plot_type:'Entity-Entity',
      plot_types:['Entity-Entity','Entity-concept']
    };
  },
  mounted() {
    console.log(this.data_.length);
    this.plot_filter = false
    this.data_loaded = this.data_;
    console.log(this.data_loaded);
    // console.log(this.data_loaded.map(d=>d['Proper nouns']))
  },
  methods: {
    removeDuplicates(originalArray) {
      var values = originalArray.map((d) => Object.values(d));
      console.log(values)
      var new_vals = [];
      var new_targets=[]
      var new_sources=[]
      var new_values=[]
      for (var i = 0; i < values.length; i++) {
        if (!(new_vals.includes(values[i]))) {
          var temp_link = {
            source: values[i][0],
            target: values[i][1],
            value: values[i][2],
          };
          new_vals.push(temp_link);
          new_targets.push(values[i][0])
          new_sources.push(values[i][1])
          new_values.push(values[i][2])
        }
      }
      return new_vals;
    },
    draw(type, alert_) {
      if (alert_ === true) {
        alert("If you chose a long book it may takes a few moments");
      }
      if (type === "Blank") {
        Plotly.newPlot("heatmap_single", []);
        return;
      }
      var div_ = document.getElementById("heatmap_single");
        div_.innerHTML = "";
      var names = this.data_loaded.map((d) => d["Proper nouns"]);
      var names_list = names.map((d) => d.split(";"));
      //adding summaries
      var summaries = this.data_loaded.map((d)=> d['Summary']);
      var summaries_list=summaries.map(d=>d.split(';'))
      //
      const isUpperCase = (string) => /^[A-Z]/.test(string);
      var names_list_temp = [];
      for (var i = 0; i < names_list.length; i++) {
        var temp = [];
        for (var j = 0; j < names_list.length; j++) {
          if (isUpperCase(names_list[i][j])) {
            temp.push(names_list[i][j]);
          }
        }
        names_list_temp.push(temp);
      }
      names_list = names_list_temp;

      var names_set = [];
      names_list.map((d) =>
        d.map((y) =>
          !names_set.includes(y) && isUpperCase(y[0]) ? names_set.push(y) : ""
        )
      ); //
      console.log(names_set);
      //repeating for summaries
      var summaries_list_temp = []
      for (var i = 0; i < summaries_list.length; i++){
        var temp=[]
        for (var j = 0; j < summaries_list.length; j++) {
          temp.push(summaries_list[i][j])
      }
      summaries_list_temp.push(temp)
    }
    summaries_list= summaries_list_temp

    var summaries_set=[]
    summaries_list.map(d=>
    d.map(y=>!summaries_set.includes(y) ?summaries_set.push(y) :''))
    //
      var name_counts = Object.fromEntries(names_set.map((k) => [k, []]));
      var single_name_counts = Object.fromEntries(names_set.map((k) => [k, 0]));
      var vals = [];

      console.log(summaries_set)

      

      //single freq entity-entity:
      for (let i = 0; i < names_list.length; i++) {
        for (let j = 0; j < names_list[i].length; j++) {
          single_name_counts[names_list[i][j]] += 1;
        }
      }

      for (let i = 0; i < names_set.length; i++) {
        var temp_l = names_set.map((d) => 0);
        for (let j = 0; j < names_set.length; j++) {
          if (i != j) {
            for (let z = 0; z < names_list.length; z++) {
              if (
                names_list[z].includes(names_set[i]) &&
                names_list[z].includes(names_set[j])
              ) {
                temp_l[j] += 1;
              }
            }
          }
        }

        vals.push(temp_l);
        name_counts[names_set[i]].push(temp_l);
      }
      console.log(name_counts);

      //single freq entity-concept
      var summaries_counts = Object.fromEntries(summaries_set.map((k) => [k, []]));
      var single_summary_counts = Object.fromEntries(summaries_set.map((k) => [k, 0]));
      var vals_summaries = [];

      for (let i = 0; i < summaries_list.length; i++) {
        for (let j = 0; j < summaries_list[i].length; j++) {
          single_summary_counts[summaries_list[i][j]] += 1;
        }
      }

      for (let i = 0; i < summaries_set.length; i++) {
        var temp_l = summaries_set.map((d) => 0);
        for (let j = 0; j < names_set.length; j++) {
         
            for (let z = 0; z < summaries_list.length; z++) {
              if (
                names_list[z].includes(names_set[j]) &&
                summaries_list[z].includes(summaries_set[i])
              ) {
                temp_l[j] += 1;
              }
            }
          }
          vals_summaries.push(temp_l);
        summaries_counts[summaries_set[i]].push(temp_l);
        }
      console.log(summaries_counts)

      //

      var links = [];
      var nodes = [];
      names_set.map((d) => nodes.push({ id: d, size: single_name_counts[d] }));

      for (var i = 0; i < names_set.length; i++) {
        for (var j = 0; j < names_list.length; j++) {
          if (names_list[j].includes(names_set[i])) {
            for (var z = 0; z < names_list[j].length; z++) {
              var temp_link = {
                source: names_set[i],
                target: names_list[j][z],
                value:
                  name_counts[names_set[i]][0][
                    names_set.indexOf(names_list[j][z])
                  ],
              };
              if (
                !links.includes(String(temp_link)) &&
                names_set[i] != names_list[j][z]
              ) {
                links.push(temp_link);
              }
            }
          }
        }
      }
      var final_links =links.filter((value, index, self) =>
  index === self.findIndex((t) => (
    t.target === value.target && t.source === value.source && t.value === value.value
  ))
);
      console.log(nodes, final_links);

    
      var link_sentiment= []
      var entity_sentiment=[]

      for(let j=0;j<final_links.length;j++){
        //links
        var temp_vals_links=[]

        for(let i=0;i<this.data_loaded.length;i++){
          var temp_vals=[final_links[j].source,final_links[j].target]
          var check=temp_vals.every(d=>this.data_loaded[i]['Proper nouns'].split(';').includes(d))
          if(check===true){
            temp_vals_links.push(+this.data_loaded[i]['Sentiment'])
          }
        }
        //console.log(temp_vals_links.reduce((a,b)=>a+b,0))
      
        link_sentiment.push(temp_vals_links.reduce((a,b)=>a+b,0)/temp_vals_links.length )
        
      }

      
        //nodes
        for(let j=0;j<nodes.length;j++){
          var temp_vals_nodes=[]

          for(let i=0;i<this.data_loaded.length;i++){
          if(this.data_loaded[i]['Proper nouns'].includes(nodes[j].id)){
            temp_vals_nodes.push(+this.data_loaded[i]['Sentiment'])
          }
        }
        entity_sentiment.push(temp_vals_nodes.reduce((a,b)=>a+b,0)/temp_vals_nodes.length )
      }

      var entity_sentiment_set=[]
      entity_sentiment.map(d=>!(entity_sentiment_set.includes(d)) ?entity_sentiment_set.push(d) :'')
      entity_sentiment_set.sort()

      console.log(entity_sentiment, link_sentiment)


      if (type === "2D Heatmap") {
        var vals_sqrd=vals.map(d=>d.map(y=>Math.sqrt(y)))
        var y_ = ()=>this.plot_type==this.plot_types[0] ?names_set :summaries_set
        var z_ = ()=>this.plot_type==this.plot_types[0] ?vals :vals_summaries

        var data = [
          {
            z: z_(), //vals_sqrd
            x: names_set,
            y: y_(),
            type: "heatmap",
            hoveorongaps: false,
          },
        ];

        var layout = {
          title: '<b>'+this.title_+'</b>',
          width: window.innerWidth / 2,
          height: window.innerHeight,
        };
        Plotly.newPlot("heatmap_single", data, layout);
      } else if (type === "3D Network") {
        var net_data = { nodes: nodes, links: final_links };
        var node_sizes = net_data.nodes.map((d) => d.size);
        var link_weights = net_data.links.map((d) => d.value);
        console.log([...link_weights].sort().pop(-1));
        var colormap = d3
        .scaleLinear()
          .domain([-1, -0.5,-0.25, 0, 0.25,0.5, 1])   //(([1, [...link_weights].sort().pop(-1)]) //retrieve sentiment values and insert them here
          .range(['blue','lightblue','lightgreen','rgba(237, 231, 225,1)','pink','orange','red'])


        console.log(colormap(4));
       var  width = window.innerWidth ;
        var  height= window.innerHeight*2;
//
const svg = d3
        .create("svg")
        .attr("width", width)
        .attr("height", height)
        .attr("viewBox", [0, 0, width, height])
        .attr("style", "max-width: 100%; height: auto; height: intrinsic;");

        svg.append('rect')
      .attr('stroke','rgba(0,0,0)')
      .attr('opacity',0.5)
      .attr('width','100%')
      .attr('height','100%')
      //.attr('fill','rba(0,0,0,0.5)')
      .attr('x',0)
      .attr('y',0)
      
var simulation = d3.forceSimulation()
    .force("link", d3.forceLink().id(function(d) { return d.id; }))
    .force("charge", d3.forceManyBody())
    .force("center", d3.forceCenter(width / 2, height / 2))
    .force('collide',d3.forceCollide(40))



  var link = svg.append("g")
      .attr("class", "links")
    .selectAll("line")
    .data(net_data.links)
    .enter().append("line")
      .attr("stroke-width", function(d) { return d.value })
      .attr('stroke',function(d,i){return colormap(link_sentiment[i])})

  var node = svg.append("g")
      .attr("class", "nodes")
    .selectAll("g")
    .data(net_data.nodes)
    .enter().append("g")

  var circles = node.append("circle")
    .attr("r", (d,i)=>Math.sqrt(d.size)*5)
    .attr("fill", function(d,i) { return colormap(entity_sentiment[i]); })
    .attr("stroke",'black')
    .attr("opacity",1)
        .on("click",function(event){
           //handle selection
          //links
          this.plot_filter= !this.plot_filter
          console.log('click!',event, this.plot_filter)
          var hidden_nodes=[]
          d3.selectAll("line")
          .filter(function(d){
            console.log(d, event.target.__data__.id)
            var event_id=event.target.__data__.id
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            if (d.target.id!==event_id){
              hidden_nodes.push(d.source.id)
            }
            return (d.target.id!==event_id) //select all NON related links
          }).style("display",function (){
            if(this.plot_filter===true){
              return 'none'
            }else{
              return 'block'
            }
          })
          console.log(event,hidden_nodes)
        })
   
    console.log(colormap(entity_sentiment[0]))
  // Create a drag handler and append it to the node object instead
  var drag_handler = d3.drag()
      .on("start", dragstarted)
      .on("drag", dragged)
      .on("end", dragended);

  drag_handler(node);

  console.log(entity_sentiment)
  
  var lables = node.append("text")
      .text(function(d) {
        return d.id;
      })
      .attr('x', -15)
      .attr('y', 10)
      //.attr('stroke','white')
      .attr('fill','black')
      .style('font-size',function(d){return Math.sqrt(d.size)*7})

  node.append("title")
      .text(function(d,i) { return `Entity: ${d.id}\nFrequency: ${d.size}\nSentiment:${entity_sentiment[i].toFixed(2)}` });

  simulation
      .nodes(net_data.nodes)
      .on("tick", ticked);

  simulation.force("link")
      .links(net_data.links);

  function ticked() {
    link
        .attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

    node
        .attr("transform", function(d) {
          return "translate(" + d.x + "," + d.y + ")";
        })
  }


        function dragstarted(event) {
          if (!event.active) simulation.alphaTarget(0.3).restart();
          event.subject.fx = event.subject.x;
          event.subject.fy = event.subject.y;

          console.log(event.subject.id)
          //handle selection
          //links
          var hidden_nodes=[]
          d3.selectAll("line")
          .filter(function(d){
            console.log(d)
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            if (d.target.id!==event.subject.id){
              hidden_nodes.push(d.source.id)
            }
            return (d.target.id!==event.subject.id) //select all NON related links
          }).style("opacity",0.1)

         
          var hidden_nodes=[]
          d3.selectAll("text")
          .filter(function(d){
            console.log(d)
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            if (d.target.id!==event.subject.id){
              hidden_nodes.push(d.source.id)
            }
            return (d.target.id!==event.subject.id) //select all NON related links
          }).style("opacity",0.1)
          console.log(event.subject)
         
          console.log(event.subject)

          console.log(hidden_nodes)
        }

        function dragged(event) {
          event.subject.fx = event.x;
          event.subject.fy = event.y;

        }

        function dragended(event) {
          if (!event.active) simulation.alphaTarget(0);
          event.subject.fx = null;
          event.subject.fy = null;

          //handle selection stop
          d3.selectAll("line")
          .filter(function(d){
            console.log(d)
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            return (d.target.id!==event.subject.id) //select all NON related links
          }).style("opacity",0.8)
          console.log(event.subject)

          d3.selectAll("text")
          .filter(function(d){
            console.log(d)
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            return (d.target.id!==event.subject.id) //select all NON related links
          }).style("opacity",0.8)
          console.log(event.subject)
           
        }

        function drag(simulation){
          function dragstarted(event) {
          if (!event.active) simulation.alphaTarget(0.3).restart();
          event.subject.fx = event.subject.x;
          event.subject.fy = event.subject.y;

          console.log(event.subject.id)
          //handle selection
          //links
          var hidden_nodes=[]
          d3.selectAll(".link")
          .filter(function(d){
            console.log(d)
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            if (d.target.id!==event.subject.id){
              hidden_nodes.push(d.source.id)
            }
            return (d.target.id!==event.subject.id) //select all NON related links
          }).style("opacity",0.2)
          console.log(event.subject)
         
          console.log(event.subject)

          console.log(hidden_nodes)
        }

        function dragged(event) {
          event.subject.fx = event.x;
          event.subject.fy = event.y;

        }

        function dragended(event) {
          if (!event.active) simulation.alphaTarget(0);
          event.subject.fx = null;
          event.subject.fy = null;

          //handle selection stop
          d3.selectAll(".link")
          .filter(function(d){
            console.log(d)
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            return (d.target.id!==event.subject.id) //select all NON related links
          }).style("opacity",0.8)
          console.log(event.subject)

           
        }

        }

 Object.assign(svg.node(), { scales: {  } });
var title_el=document.createElement('p')
title_el.innerHTML = '<b>'+this.title_+'</b>'  
div_.appendChild(title_el)
div_.appendChild(Object.assign(svg.node(), { scales: {  } }));

    }

  }},
  watch: {
    data_loaded: function () {
      this.draw(this.plot_option, false);
    },
    plot_option: function () {
      this.draw(this.plot_option, true);
    },
  },
};
</script>

<template>
  <b-container>
    <b-row>
      <b-form-group v-slot="{ ariaDescribedby }">
        <b-form-radio-group
          v-model="plot_type"
          :options="plot_types"
          :aria-describedby="ariaDescribedby"
          name="radio-inline-scale_type"
        ></b-form-radio-group>
      </b-form-group>
    </b-row>
    <b-row>
      <b-form-group v-slot="{ ariaDescribedby }">
        <b-form-radio-group
          v-model="plot_option"
          :options="plot_type===plot_types[0] ?plot_options :['Blank','2D Heatmap']"
          :aria-describedby="ariaDescribedby"
          name="radio-inline-scale_plot"
        ></b-form-radio-group>
      </b-form-group>
    </b-row>
    <b-row>
      <div id="heatmap_single"></div>
    </b-row>
  </b-container>
</template>

<style>
svg {
  background-color:'rgba(0,0,0,1)'
}
</style>