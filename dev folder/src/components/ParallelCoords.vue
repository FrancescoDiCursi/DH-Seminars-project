<script>
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";

export default {
  name: "ParallelCoords",
  props: {
    hours: Array,
    minutes: Array,
    prices: Array,
    nums: Array,
    locations: Array,
    dates: Array,
    start: Boolean

  },
  data() {
    return {
      nums_prices: [],
      data_length: 0,
    };
  },
  methods: {
    parallelCoords() {

      this.nums_prices = this.nums.map((d, i) => {
        return "•) " + this.nums[i] + ": " + this.prices[i];
      });

      var colors = new Int8Array(this.data_length);
      var colorscale = [
        [0, "gray"],
        [1, "firebrick"],
      ];
      var trace1 = {
        type: "parcats",
        hoveron: "color",
        hoverinfo: "count+probability",
        dimensions: [
          {
            label: "Nums",
            values: this.nums.map(String),
            categoryorder: "category descending",
          },
          {
            label: "Date",
            values: this.dates.map(String),
            categoryorder: "category descending",
          },
          {
            label: "Hour",
            values: this.hours.map(String),
            categoryorder: "category descending",
          },
          {
            label: "Minute",
            values: this.minutes.map(String),
            categoryorder: "category descending",
          },
          {
            label: "Location",
            values: this.locations.map(String),
            categoryorder: "category descending",
          },
        ],
        counts: this.prices.map(Number),
        line: {
          colorscale: colorscale,
          cmin: 0,
          cmax: 1,
          color: colors,
          shape: "hspline",
        },
      };

      var data = [trace1];

      let height_;
      let num_set=[...new Set(this.nums)]


      if (num_set.length <= 25) {
        height_ = 800;
      }else if( (num_set.length >25) && (num_set.length<=50)){
        height_=1000
      } else {
        height_ = 1500;
      }
      var layout = { width: 1300, height: height_ };

      Plotly.newPlot("parallel_", data, layout, {displayModeBar: false,});

      var new_color = new Int8Array(this.data_length);
      var plot_div = document.getElementById("parallel_");
      plot_div.on("plotly_click", function (event) {

        var selection = [];
        for (var i = 0; i < event.points.length; i++) {
          if (new_color[event.points[i].pointNumber] == 1) {
            new_color[event.points[i].pointNumber] = 0;
          } else {
            new_color[event.points[i].pointNumber] = 1;
          }
          selection.push(event.points[i].pointNumber);
        }

        Plotly.restyle("parallel_", { "line.color": [new_color] });
      });
    },
  },
  watch: {
    dates: function (newVal) { 
      this.data_length = this.nums.length;
      this.parallelCoords();
    },
  },
};
</script>

<template>
  <div id="parallel_"></div>
</template>

<style scoped>

</style>