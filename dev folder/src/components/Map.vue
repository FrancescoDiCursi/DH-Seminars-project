<script>
import * as d3 from "https://cdn.skypack.dev/d3@7";
import * as topojson from "https://cdn.skypack.dev/topojson@3.0.2";

var width_svg = 1300;
var height_svg = 800;

var projection = d3
  .geoMercator()
  .center([24.8856, 36.0703])
  .scale(600000)
  .translate([width_svg / 2, height_svg / 2]);

export default {
  name: "map_container",
  props: { data_: Array, car_traces_: Object },
  data() {
    return {
      abila1: [],
      dates: [],
      hours: [],
      minutes: [],
      seconds: [],
      names: [],
      types: [],
      titles: [],
      ids: [],
      timestamps: ["01/06/2014 06:28:01"],
      coords_gps: [],
      n_timestamps: [],
      value_timestamp: 0,
      actual_timestamp: 1,
      csv_gps: {},
      filtered_gps: {},
      autoplay: false,
      car_traces: {}, //id:last timestamp

      close_cars: [[0, "None", "", "", []]],
      heatmap: false,

      selected_days: ["All"],
      days_list: [],
      auto_zoom: false,
      show_image: false,
    };
  },
  mounted() {
    Promise.all([d3.json("./static/Abila.json.json")]).then((data) => {
      this.abila1 = topojson.feature(data[0], data[0].objects.Abila1).features;
      this.car_traces = this.car_traces_;
    });
  },
  methods: {
    zoom() {
      document.body.style.zoom = "90%";
    },
    bar_map_2d(coords_) {
      var data = [
        {
          x: coords_.map((d) => d[0]),
          y: coords_.map((d) => d[1]),
          type: "histogram2d",
          barmode: "stack",
          showlegend: "true",
        },
        {
          x: coords_.map((d) => d[0]),
          y: coords_.map((d) => d[1]),
          //z: this.nums.map(d=>String(d)),
          text: this.names.map(String),
          mode: "markers",
          type: "scatter", //scatter3d
          showlegend: "true",
        },
      ];

      var layout = {
        margin: {
          t: -100,
          l: 100,
          b: 200,
          t: 0,
        },

        width: 1000,
        height: 600,
        xaxis: {
          nticks: 20,
          tickmode: "auto",
          dtick: 45,
          //range:[ 36.04802,36.08995]
          //rangemode: 'tozero'
        },
        yaxis: {
          nticks: 20,
          tickmode: "auto",
          dtick: 45,
          //range:[24.82508,24.90848]
        },
      };
      return Plotly.newPlot("bar_map", data, layout, {
        /*scrollZoom: true*/
      });
    },
    sleep(ms) {
      return new Promise((resolve) => setTimeout(resolve, ms));
    },
    async forward() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && +this.value_timestamp + 1 <= 303206) {
        this.value_timestamp = +this.value_timestamp + 1;
        await this.sleep(0);
      }
    },
    async forwardx10() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && this.value_timestamp + 10 <= 303206) {
        this.value_timestamp = +this.value_timestamp + 10;
        await this.sleep(0);
      }
    },
    async forwardx100() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && this.value_timestamp + 100 <= 303206) {
        this.value_timestamp = +this.value_timestamp + 100;
        await this.sleep(0);
      }
    },
    async forwardx1000() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && this.value_timestamp + 1000 <= 303206) {
        this.value_timestamp = +this.value_timestamp + 1000;
        await this.sleep(0);
      }
    },
    pause() {
      this.autoplay = false;
    },
    async backward() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && this.value_timestamp >= 1) {
        this.value_timestamp = +this.value_timestamp - 1;
        await this.sleep(0);
      }
    },
    async backwardx10() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && this.value_timestamp >= 10) {
        this.value_timestamp = +this.value_timestamp - 10;
        await this.sleep(0);
      }
    },
    async backwardx100() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && this.value_timestamp >= 100) {
        this.value_timestamp = +this.value_timestamp - 100;
        await this.sleep(0);
      }
    },
    async backwardx1000() {
      this.pause();
      await this.sleep(0);
      this.autoplay = true;
      while (this.autoplay == true && this.value_timestamp >= 1000) {
        this.value_timestamp = +this.value_timestamp - 1000;
        await this.sleep(0);
      }
    },
    filter_gps(timestamp_val) {
      this.csv_gps = this.data_;
      //filter by  timestamp
      this.filtered_gps = this.csv_gps.filter(
        (d) => d.n_timestamp == timestamp_val
      );
      //
      if (this.filtered_gps == 0) {
        d3.select("#map_svg").select(".cars_markers").remove();
      }
      this.timestamps = this.filtered_gps.map((d) =>
        d.Timestamp ? d.Timestamp : ""
      );
      this.dates = this.filtered_gps.map((d) => (d.Date ? d.Date : ""));
      this.hours = this.filtered_gps.map((d) => (d.Hour ? d.Hour : ""));
      this.minutes = this.filtered_gps.map((d) => (d.Minute ? d.Minute : ""));
      this.seconds = this.filtered_gps.map((d) => (d.Second ? d.Second : ""));
      this.names = this.filtered_gps.map((d) =>
        d.fullname ? d.fullname : "?????"
      );
      this.types = this.filtered_gps.map((d) =>
        d.CurrentEmploymentType ? d.CurrentEmploymentType : ""
      );
      this.titles = this.filtered_gps.map((d) =>
        d.CurrentEmploymentTitle ? d.CurrentEmploymentTitle : ""
      );
      this.ids = this.filtered_gps.map((d) => (d.id ? d.id : ""));
      this.coords_gps = this.filtered_gps.map((d) => [+d.long, +d.lat]);
      this.n_timestamps = this.filtered_gps.map((d) => +d.n_timestamp);
      this.actual_timestamp = this.filtered_gps[0].Timestamp
        ? this.filtered_gps[0].Timestamp
        : "None";
    },
    map() {
      //assigning prop to internal data
      this.csv_gps = this.data_;

      /*var projection = d3
        .geoMercator()
        .center([24.8856, 36.0703])
        .scale(600000)
        .translate([width / 2, height / 2]);*/
      //creo l'svg

      var svg = d3
        .select("#meta_map_container")
        .append("svg")
        .attr("id", "map_svg")
        .attr("width", width_svg)
        .attr("height", height_svg);

      svg
        .append("image")
        .attr("id", "abila_background")
        .attr("xlink:href", "static\\MC2-tourist.jpg")
        .attr("width", "70%")
        .attr("height", 1200)
        .attr("y", -200)
        .attr("opacity", 0.5);

      /*
      var svg = d3
        .select("#map_svg")
        .attr("height", height_svg)
        .attr("width", width_svg)
        .attr("id", "map_svg")
        .append("g");
*/
      var path = d3.geoPath().projection(projection); //each time that data are passed, they are transformed by projection
      var properties = this.abila1.map((d, i) => this.abila1[i].properties); //properties of geometries
      var temp_coords = this.abila1.map((d, i) => this.abila1[i].geometry);
      var props_coords = temp_coords.map((d, i) =>
        temp_coords[i] ? temp_coords[i].coordinates[0] : ""
      );
      var props_coords2 = temp_coords.map((d, i) =>
        temp_coords[i] ? temp_coords[i].coordinates[1] : ""
      );
      var props_type = properties.map((d) => d.FETYPE);
      var props_name = properties.map((d) => d.FENAME);
      svg
        .selectAll(".x")
        .data(this.abila1)
        .enter()
        .append("path")
        .attr("class", "x")
        .attr("d", path)
        .attr("stroke", "grey");
      //first and second coordinates //inside "abila1", in geometry=>coordinates, there are 2 set of coords (investigate) [they should be street points, but why 2?]
      //I do not insert them: the given image as background of svg is sufficient to understand streets's names
      /***
      _________________________________________________________
             * FIRST SET OF COORDS
             *      svg
              .selectAll(".y")
              .data(props_coords)
              .enter()
              .append("circle")
              .attr("class", "y")
              .attr("r", 3)
              .attr("opacity", 0.4)
              .attr("cx", function (d) {
                var coords = projection([d[0], d[1]]);
                return coords[0];
              })
              .attr("cy", function (d) {
                var coords = projection([d[0], d[1]]);
                return coords[1];
              })
              .attr("stroke", "red")
              .attr("fill", "grey")
              .on("mouseover", function (d, i) {
                d3.select(this).attr("fill", "red");
              })
              .on("mouseout", function (d, i) {
                d3.select(this).attr("fill", "red");
              });
      ______________________________________________________
             * SECOND SET OF COORDS
             * svg
              .selectAll(".y2")
              .data(props_coords2)
              .enter()
              .append("circle")
              .attr("class", "y2")
              .attr("r", 1)
              .attr("cx", function (d) {
                var coords = projection([d[0], d[1]]);
                return coords[0];
              })
              .attr("cy", function (d) {
                var coords = projection([d[0], d[1]]);
                return coords[1];
              })
              .attr("stroke", "green")
              .attr("fill", "green");
      _______________________________________________________
             * TEXT OF FIRST SET OF COORDS (streets's names)
                   svg
              .selectAll(".x_types")
              .data(props_type)
              .enter()
              .append("text")
              .attr("class", "x_types")
              .attr("x", function (d, i) {
                var coords = projection([props_coords[i][0], props_coords[i][1]]);
                return coords[0];
              })
              .attr("y", function (d, i) {
                var coords = projection([props_coords[i][0], props_coords[i][1]]);
                return coords[1];
              })
              .attr("stroke", "transparent")
              .attr("fill", "tranparent")
              .attr("font-size", 3)
              .attr("opacity", 0)
              .text(function (d, i) {
                return props_type[i] + " - " + props_name[i];
              })
              .on("mouseover", function (d, i) {
                d3.select(this)
                  .attr("stroke", "black")
                  .attr("fill", "black")
                  .attr("opacity", 1)
                  .attr("font-size", 25);
              })
              .on("mouseout", function (d, i) {
                d3.select(this)
                  .attr("stroke", "transparent")
                  .attr("fill", "transparent")
                  .attr("font-size", 3)
                  .attr("opacity", 0);
              });
            ***/
    },
    draw_cars(data) {
      let width = 1300;
      let height = 800;
      /*
      var projection = d3
        .geoMercator()
        .center([24.8856, 36.0703]) //kronos: [24.5,35.2]; //abila1: [24.8856, 36.07] //abila2: []
        .scale(600000) //kronos 15000; //abila1: 600000 //abila2: []
        .translate([width / 2, height / 2]);*/
      d3.select("#map_svg").select(".cars_markers").remove();
      let svg = d3.select("#map_svg").append("g").attr("class", "cars_markers");
      svg
        .selectAll(".cars")
        .data(data)
        .enter()
        .append("circle")
        .attr("class", "cars")
        .attr("r", 5)
        .attr("cx", function (d) {
          var coords = projection([d[0], d[1]]);
          return coords[0];
        })
        .attr("cy", function (d) {
          var coords = projection([d[0], d[1]]);
          return coords[1];
        })
        .attr("stroke", "blue")
        .attr("fill", "blue")
        .on("mouseover", function (d, i) {
          d3.select(this).attr("fill", "white");
        })
        .on("mouseout", function (d, i) {
          d3.select(this).attr("fill", "yellow");
        });
      //text
      let names_gps = this.names.map((d) => d);
      let types_gps = this.types.map((d) => d);
      let titles_gps = this.titles.map((d) => d);
      let ids_gps = this.ids.map((d) => d);

      svg
        .selectAll(".cars_infos")
        .data(data)
        .enter()
        .append("text")
        .attr("class", "cars_infos")
        .attr("x", function (d, i) {
          var coords = projection([d[0], d[1]]);
          return coords[0];
        })
        .attr("y", function (d, i) {
          var coords = projection([d[0], d[1]]);
          return coords[1];
        })
        .attr("stroke", "blue")
        .attr("fill", "blue")
        .attr("font-size", 15)
        .attr("opacity", 0.5)
        .text(function (d, i) {
          return `${ids_gps[i]}  -  ${names_gps[i]}  -  ${types_gps[i]} (${titles_gps[i]})`;
        })
        .on("mouseover", function (d, i) {
          d3.select(this)
            .attr("stroke", "blue")
            .attr("fill", "blue")
            .attr("opacity", 1)
            .attr("font-size", 30);
        })
        .on("mouseout", function (d, i) {
          d3.select(this)
            .attr("stroke", "blue")
            .attr("fill", "blue")
            .attr("font-size", 10)
            .attr("opacity", 0.5);
        });
    },
    draw_heat(data) {
      let width = 1300;
      let height = 800;
      d3.select("#map_svg").select(".heat_rects").remove();
      let svg = d3.select("#map_svg").append("g").attr("class", "heat_rects");

      /*var projection = d3
        .geoMercator()
        .center([24.8856, 36.0703]) //kronos: [24.5,35.2]; //abila1: [24.8856, 36.07] //abila2: []
        .scale(600000) //kronos 15000; //abila1: 600000 //abila2: []
        .translate([width / 2, height / 2]);*/

      const approxeq = (
        v1,
        v2,
        epsilon = 0.001 //epsilon=0.001 | 0.01
      ) => Math.abs(v1 - v2) <= epsilon;
      let names_gps = this.names.map((d) => d);
      let types_gps = this.types.map((d) => d);
      let titles_gps = this.titles.map((d) => d);
      let ids_gps = this.ids.map((d) => d);
      this.close_cars = [];
      for (let i = 0; i < this.coords_gps.length; i++) {
        for (let j = 0; j < this.coords_gps.length; j++) {
          if (
            (i != j) &
            approxeq(this.coords_gps[i][0], this.coords_gps[j][0], 0.0025) & //0.001 devono essere uno sopra l'altro, 0.002 le metà dei rettangoli toccano i punti, 0.003 i bordi si toccano
            approxeq(this.coords_gps[i][1], this.coords_gps[j][1], 0.0025)
          ) {
            if (!this.close_cars.map((el) => el[0]).includes(ids_gps[i])) {
              this.close_cars.push([
                ids_gps[i],
                names_gps[i],
                types_gps[i],
                titles_gps[i],
                this.coords_gps[i],
              ]);
            }
            if (!this.close_cars.map((el) => el[0]).includes(ids_gps[j])) {
              this.close_cars.push([
                ids_gps[j],
                names_gps[j],
                types_gps[j],
                titles_gps[j],
                this.coords_gps[j],
              ]);
            }
          }
        }
      }

      if (this.close_cars.length == 0) {
        this.close_cars.push([0, "None", "", "", []]);
      }

      svg
        .selectAll()
        .data(data)
        .enter()
        .append("rect")
        .attr("x", (d) => projection([d[0], d[1]])[0] - 25)
        .attr("y", (d) => projection([d[0], d[1]])[1] - 25)
        .attr("width", 50)
        .attr("height", 50)
        .style("fill", "red")
        .attr("fill-opacity", 0.2)
        .on("click", function () {
          //console.log(data.map((coords_) => coords_));
        });
    },
    complessive_heat() {
      if (
        this.selected_days.length != 0 &&
        !this.selected_days.includes("All")
      ) {
        this.csv_gps = this.data_.filter((d) =>
          this.selected_days.includes(d.Date)
        );
      } else if (
        this.selected_days.length == 1 &&
        this.selected_days[0] == "All"
      ) {
        this.csv_gps = this.data_;
      }
      var lats = this.csv_gps.map((d) => +d.lat);
      var longs = this.csv_gps.map((d) => +d.long);

      var width = 1300;
      var height = 850;

      var data = [
        {
          y: lats.map(Number), 
          x: longs.map(Number),
          histfunc: "sum",
          type: "histogram2d",
          colorscale: [
            [0.0, "white"],
            [0.2, "blue"],
            [0.4, "green"],
            [0.6, "yellow"],
            [0.8, "orange"],
            [1, "red"],
          ], //'Earth',
          showscale: true,
          reversescale: false,
          colorbar: { x: -0.03, ticklabelposition: "inside" },
        },
      ];

      var x_minmax = [
        projection([24.82508806, 24.90848537])[0],
        projection([24.82508806, 24.90848537])[1],
      ];
      var y_minmax = [
        projection([36.04802098, 36.08995956])[0],
        projection([36.04802098, 36.08995956])[1],
      ];

      var layout = {
        paper_bgcolor: "transparent",
        plot_bgcolor: "transparent",

        height: height_svg - 50,
        width: width_svg - 225,

        xaxis: {
          visible: false,
          range: [24.82508806, 24.9084853],
          fixedrange: true,
        },

        yaxis: {
          visible: false,
          range: [36.04802098, 36.08995956],
          fixedrange: true,
        },
      };

      Plotly.newPlot("heat_layer", data, layout, {
        displayModeBar: false,
      });
    },
    cronologic_map() {
      this.filter_gps(document.getElementById("timeRange").value);
      this.draw_heat(this.coords_gps);
      this.draw_cars(this.coords_gps);
    },
  },
  watch: {
    abila1: function (newVal) {
      this.map();
    },
    value_timestamp: function (newVal) {
      this.cronologic_map();
    },

    data_: function (newVal) {
      this.complessive_heat();
      var complessive_heat_div = document.getElementById("heat_layer");
      complessive_heat_div.style.display = "none";
      var days_list_temp = new Set(this.data_.map((d) => d.Date));
      this.days_list = [...days_list_temp];
      this.days_list.unshift("All");
    },
    heatmap: function (newVal) {
      var map_cont=document.getElementById('map_svg')
      var complessive_heat_div = document.getElementById("heat_layer");
      var image= document.getElementById("abila_image")
      if (this.heatmap == true) {
        map_cont.style.display='block'
        complessive_heat_div.style.display = "block";
        image.style.display='none'
        this.show_image=false
        
      } else if (this.heatmap == false) {
        complessive_heat_div.style.display = "none";
        
      }
    },
    show_image: function (newVal) {
      var image = document.getElementById("abila_image");
      var heat_layer = document.getElementById("heat_layer");
      var map_cont = document.getElementById("map_svg");
      var list_cont = document.getElementById("points_list_container");
      if (this.show_image == true) {
        image.style.display = "block";
        heat_layer.style.display = "none";
        map_cont.style.display = "none";
        list_cont.style.display = "none";
        this.heatmap=false
      } else if (this.show_image == false) {
        image.style.display = "none";
        map_cont.style.display = "block";
        list_cont.style.display = "block";
      }
    },
  },
};
</script>

<template>
  <span>
    <div id="meta_map_container">
      <div id="points_list_container">
        <b-list-group id="close_points_list">
          <b-list-group-item
            v-for="el in this.close_cars"
            :key="el[0]"
            class="'d-flex justify-content-between align-items-center"
          >
            <b-badge variant="primary" pill>{{ el[0] }}</b-badge>
            {{
              el[1] +
              " — " +
              el[2] +
              " (" +
              el[3] +
              ") — [" +
              el[4][0] +
              ", " +
              el[4][1] +
              "]"
            }}
          </b-list-group-item>
        </b-list-group>
      </div>
      <b-container>
        <b-row>
          <b-col>
            <b-button
              id="complessive_heat_btn"
              @click="heatmap == true ? (heatmap = false) : (heatmap = true)"
              >{{
                this.heatmap == true
                  ? "Hide heatmap layer"
                  : "Show heatmap layer"
              }}</b-button
            >
            <b-button
              id="show_abila"
              @click="
                show_image == true ? (show_image = false) : (show_image = true)
              "
              >{{
                this.show_image == false
                  ? "Show Abila's image"
                  : "Hide Abila's image"
              }}</b-button
            ></b-col
          >
          <b-col cols="2"
            ><code>Date</code>
            <b-form-select
              id="day_filter_map"
              v-model="selected_days"
              :options="days_list"
              v-on:change="complessive_heat"
              multiple
            >
            </b-form-select
          ></b-col>
        </b-row>
        <b-row>
          <div id="image_wrapper">
            <img
              id="abila_image"
              :src="'static\\MC2-tourist.jpg'"
              alt="Abila's image"
            />
          </div>
        </b-row>
        <b-row>
          <b-col><div id="heat_layer"></div></b-col>
          <b-col>
            <div id="map_container"></div>
          </b-col>
        </b-row>
      </b-container>
    </div>
    <div id="time_handler">
    <b-container id="last_section">
      <b-row>
        <b-col>
          <div id="timestamp_value">
            {{ this.timestamps[0] }}
          </div>
        </b-col>
      </b-row>
      <b-row>
        <b-col>
          <b-button-group id="speed_handler">
            <b-button variant="outline-primary" @click="backwardx1000">
              <b-icon
                icon="forward"
                style="color: black; transform: scaleX(-1)"
              ></b-icon
              >x1000
            </b-button>
            <b-button variant="outline-primary" @click="backwardx100">
              <b-icon
                icon="forward"
                style="color: black; transform: scaleX(-1)"
              ></b-icon
              >x100
            </b-button>
            <b-button variant="outline-primary" @click="backwardx10">
              <b-icon
                icon="forward"
                style="color: black; transform: scaleX(-1)"
              ></b-icon
              >x10
            </b-button>
            <b-button variant="outline-primary" @click="backward">
              <b-icon
                icon="forward"
                style="color: black; transform: scaleX(-1)"
              ></b-icon
              >x1
            </b-button>
            <b-button variant="outline-secondary" @click="pause">
              <b-icon icon="pause" style="color: black"></b-icon>
            </b-button>
            <b-button variant="outline-primary" @click="forward">
              <b-icon icon="forward" style="color: black"></b-icon>x1
            </b-button>
            <b-button variant="outline-primary" @click="forwardx10">
              <b-icon icon="forward" style="color: black"></b-icon>x10
            </b-button>
            <b-button variant="outline-primary" @click="forwardx100">
              <b-icon icon="forward" style="color: black"></b-icon>x100
            </b-button>
            <b-button variant="outline-primary" @click="forwardx1000">
              <b-icon icon="forward" style="color: black"></b-icon>x1000
            </b-button>
          </b-button-group>
        </b-col>
      </b-row>
    </b-container>
    <b-form-input
      id="timeRange"
      v-model="value_timestamp"
      type="range"
      min="1"
      max="303206"
      step="1"
      variant="dark"
    ></b-form-input>
</div>
  </span>
</template>

<style>

#points_list_container {
  position: absolute;
  margin-left: 80%;
  width: 56%;
  margin-top: 17%;
}

#time_handler{
  padding:2%;
  border-style: solid;
  border-width: 1px;
  border-radius:10px;
  width:100vh;
}

#last_section {
  position: relative;
  width: 100%;
  margin-left: 25%;
}

#speed_handler {
  margin-left: -15%;
  width: 80%;
}
#timestamp_value {
  font-weight: bold;
  font-size: larger;
  margin-left: 15%;
  margin-right: auto;
}

#timeRange {
  background-color: grey;
  border-radius: 0%;
  padding:1%
}


#complessive_heat_btn {
  width: 75%;
}


#abila_image {
  width: 90%;
  height: 90%;
  position: relative;
  display: none;
}
#heat_layer {
  position: absolute;
  margin-left: -29%;
  margin-top:4%;
}
#map_svg {
  position: relative;
  margin-left: -5%;
  margin-top:0%
}




</style>