<script>
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
import * as d3 from "https://cdn.skypack.dev/d3@7";

export default {
  name: "Single_heatmap.vue",
  props: { data_: Array },
  data() {
    return {
      plot_filter:Boolean,
      data_loaded: {},
      plot_option: "Blank",
      plot_options: ["Blank", "2D Heatmap", "3D Network"],
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

      var name_counts = Object.fromEntries(names_set.map((k) => [k, []]));
      var single_name_counts = Object.fromEntries(names_set.map((k) => [k, 0]));
      var vals = [];

      //single freq:
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

    
      //CONTINUE FROM HERE
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

        var data = [
          {
            z: vals, //vals_sqrd
            x: names_set,
            y: names_set,
            type: "heatmap",
            hoveorongaps: false,
          },
        ];

        var layout = {
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
        var chart = this.ForceGraph(net_data, {
          nodeId: (d) => d.id,
          nodeGroups: entity_sentiment_set,
          nodeGroup: (d,i) => entity_sentiment[i],// entity sentiment value ()
          //nodeFill:(d,i)=>colormap(entity_sentiment[i]),
          nodeStroke:(d,i)=>'white',
          nodeTitle: (d,i) => `Entity: ${d.id}\nFrequency: ${d.size}\nSentiment:${entity_sentiment[i].toFixed(2)}`,
          linkStrokeWidth: (l, i) => Math.sqrt(l.value),
          linkStroke: (l, i) => colormap(link_sentiment[i]),//colormap(l.value), //link sentiment value ()
          nodeRadius: (d, i) => Math.sqrt(node_sizes[i])*2,
          linkStrength: (d,i)=>link_sentiment[i],
          nodeStrength:(d,i)=>entity_sentiment[i],
          node_sentiment_forces:entity_sentiment,
        link_sentiment_forces: link_sentiment,
        plot_filter: this.plot_filter,
          width: window.innerWidth / 1.5,
          height: window.innerHeight*2,
          //invalidation // a promise to stop the simulation when the cell is re-run
        });

       
        div_.appendChild(chart);
      }
    },
    // Copyright 2021 Observable, Inc.
    // Released under the ISC license.
    // https://observablehq.com/@d3/force-directed-graph
    ForceGraph(
      {
        nodes, // an iterable of node objects (typically [{id}, …])
        links, // an iterable of link objects (typically [{source, target}, …])
      },
      {
        nodeId = (d) => d.id, // given d in nodes, returns a unique identifier (string)
        nodeGroup, // given d in nodes, returns an (ordinal) value for color
        nodeGroups, // an array of ordinal values representing the node groups
        nodeTitle, // given d in nodes, a title string
        nodeFill , // node stroke fill (if not using a group color encoding)
        nodeStroke ,// node stroke color
        nodeStrokeWidth = 2, // node stroke width, in pixels
        nodeStrokeOpacity = 0.6, // node stroke opacity
        nodeRadius = 5, // node radius, in pixels
        nodeStrength,
        linkSource = ({ source }) => source, // given d in links, returns a node identifier string
        linkTarget = ({ target }) => target, // given d in links, returns a node identifier string
        linkStroke = "#999", // link stroke color
        linkStrokeOpacity = 0.2, // link stroke opacity
        linkStrokeWidth = 1.5, // given d in links, returns a stroke width in pixels
        linkStrokeLinecap = "round", // link stroke linecap
        linkStrength,
        colors = ['blue','lightblue','rgba(237, 231, 225,1)','pink','red'], // an array of color strings, for the node groups,
        node_sentiment_forces,
        link_sentiment_forces,
        plot_filter,
        width = window.innerWidth, // outer width, in pixels
        height = window.innerHeight, // outer height, in pixels
        //invalidation, // when this promise resolves, stop the simulation
      } = {}
    ) {
      // Compute values.
      const N = d3.map(nodes, nodeId).map(intern);
      const LS = d3.map(links, linkSource).map(intern);
      const LT = d3.map(links, linkTarget).map(intern);
      if (nodeTitle === undefined) nodeTitle = (_, i) => N[i];
      const T = nodeTitle == null ? null : d3.map(nodes, nodeTitle);
      const G = nodeGroup == null ? null : d3.map(nodes, nodeGroup).map(intern);
      const W =
        typeof linkStrokeWidth !== "function"
          ? null
          : d3.map(links, linkStrokeWidth);
      const L =
        typeof linkStroke !== "function" ? null : d3.map(links, linkStroke);

      // Replace the input nodes and links with mutable objects for the simulation.
      nodes = d3.map(nodes, (_, i) => ({ id: N[i] }));
      links = d3.map(links, (_, i) => ({ source: LS[i], target: LT[i] }));

      // Compute default domains.
      if (G && nodeGroups === undefined) nodeGroups = d3.sort(G);

      // Construct the scales.
      const color = d3
          .scaleLinear()
          .domain([-1, -0.5,-0.25, 0, 0.25,0.5, 1])   //(([1, [...link_weights].sort().pop(-1)]) //retrieve sentiment values and insert them here
          .range(['blue','lightblue','lightgreen','beige','pink','orange','red'])

      // Construct the forces.
      const forceNode = d3.forceManyBody();
      const forceLink = d3.forceLink(links).id(({ index: i }) => N[i]);
      if (nodeStrength !== undefined) forceNode.strength(-10).distanceMin(nodeStrength).distanceMax(nodeStrength*100);
      if (linkStrength !== undefined) forceLink.distance(linkStrength);

      const simulation = d3
        .forceSimulation(nodes)
        .force("x", d3.forceX(width / 2))
  .force("y", d3.forceY(height / 2))
        .force("link", forceLink)
        
        .force("charge", forceNode)
        .force("center", d3.forceCenter())
        .force('collide',d3.forceCollide(20))
       
        .on("tick", ticked);

      
      const svg = d3
        .create("svg")
        .attr("width", width)
        .attr("height", height)
        .attr("viewBox", [-width / 2, -height / 2, width, height])
        .attr("style", "max-width: 100%; height: auto; height: intrinsic;");

      svg.append('rect')
      .attr('stroke','rgba(0,0,0)')
      .attr('opacity',0.5)
      .attr('width','100%')
      .attr('height','100%')
      //.attr('fill','rba(0,0,0,0.5)')
      .attr('x',-500)
      .attr('y',-1000)

      const link = svg
        .append("g")
        .attr("stroke", typeof linkStroke !== "function" ? linkStroke : null)
        .attr("stroke-opacity", linkStrokeOpacity)
        .attr(
          "stroke-width",
          typeof linkStrokeWidth !== "function" ? linkStrokeWidth : null
        )
        .attr("stroke-linecap", linkStrokeLinecap)
        .selectAll("line")
        .data(links)
        .join("line")
        .attr("class",'link')


      const node = svg
        .append("g")
        .attr("fill", nodeFill)
        .attr("stroke", 'white')
        .attr("stroke-opacity", nodeStrokeOpacity)
        .attr("stroke-width", nodeStrokeWidth)
        .selectAll("circle")
        .data(nodes)
        .join("circle")
        .attr('class','node')
        .attr("r", nodeRadius)
        .attr("opacity",1)
        .on("click",function(event){
           //handle selection
          //links
          this.plot_filter= !plot_filter
          plot_filter=!plot_filter
          console.log('click!',event, plot_filter)
          var hidden_nodes=[]
          d3.selectAll(".link")
          .filter(function(d){
            console.log(d, event.target.__data__.id)
            var event_id=event.target.__data__.id
            //return (d.source.id===event.subject.id) || (d.target.id===event.subject.id) //select all related links
            if (d.target.id!==event_id){
              hidden_nodes.push(d.source.id)
            }
            return (d.target.id!==event_id) //select all NON related links
          }).style("display",function (){
            if(plot_filter===true){
              return 'none'
            }else{
              return 'block'
            }
          })
          console.log(event)
        })
       .call(drag(simulation))

      

        //node.append("title").text(d=>d)

      if (W) link.attr("stroke-width", ({ index: i }) => W[i]);
      if (L) link.attr("stroke", ({ index: i }) => L[i]);
      if (G) node.attr("fill", ({ index: i }) => color(G[i]));
      if (T) node.append("title").text(({ index: i }) => T[i]);
      //  if (invalidation != null) invalidation.then(() => simulation.stop());

      function intern(value) {
        return value !== null && typeof value === "object"
          ? value.valueOf()
          : value;
      }

      function ticked() {
        link
          .attr("x1", (d) => d.source.x)
          .attr("y1", (d) => d.source.y)
          .attr("x2", (d) => d.target.x)
          .attr("y2", (d) => d.target.y);

        node.attr("cx", (d) => d.x).attr("cy", (d) => d.y);
      }

      function drag(simulation) {
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
          }).style("opacity",1)
          console.log(event.subject)

           
        }

        return d3
          .drag()
          .on("start", dragstarted)
          .on("drag", dragged)
          .on("end", dragended);
      }

      return Object.assign(svg.node(), { scales: {  } });
    },

  },
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
          v-model="plot_option"
          :options="plot_options"
          :aria-describedby="ariaDescribedby"
          name="radio-inline-scale"
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