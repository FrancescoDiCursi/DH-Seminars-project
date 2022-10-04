
<template>
  <span>
    <b-container
      ><b-row>
        <b-col cols="12"><code>Filter heatmap</code></b-col>
      </b-row>
      <b-row>
        <b-col>
          <b-button-group id="heat_filt_container">
            <b-button id="heat_filt_ratio" @click="heat_ratio">Ratio</b-button>

            <b-button id="heat_filt_price" @click="heat_price">Price</b-button>

            <b-button id="heat_filt_freq" @click="heat_freq"
              >Frequency</b-button
            >
          </b-button-group>
        </b-col>
      </b-row></b-container
    >
    <hr id="section_separator" />
    <b-container>
      <b-row>
        <b-col cols="2">
          <code>Card type</code>
          <b-form-select
            id="cardtype_filter"
            v-model="selected_cardtypes"
            :options="['All', 'Credit card', 'Loyalty card']"
            v-on:change="filter_data"
            multiple
          >
          </b-form-select>
        </b-col>
        <b-col cols="2">
          <code>ID</code>
          <b-form-select
            id="id_filter"
            v-model="selected_nums"
            :options="nums_list"
            v-on:change="filter_data"
            multiple
          >
          </b-form-select>
        </b-col>
        <b-col cols="2">
          <code>Date</code>
          <b-form-select
            id="date_filter"
            v-model.lazy="selected_dates"
            :options="dates_list"
            v-on:change="filter_data"
            multiple
          >
          </b-form-select>
        </b-col>
        <b-col cols="1">
          <code>Hour</code>
          <b-form-select
            id="hour_filter"
            v-model="selected_hours"
            :options="hours_list"
            v-on:change="filter_data"
            multiple
          >
          </b-form-select>
        </b-col>
        <b-col cols="1">
          <code>Minute</code>
          <b-form-select
            id="Minute_filter"
            v-model="selected_minutes"
            :options="minutes_list"
            v-on:change="filter_data"
            multiple
          >
          </b-form-select>
        </b-col>
        <b-col>
          <code>Location</code>
          <b-form-select
            id="location_filter"
            v-model="selected_locations"
            :options="locations_list"
            v-on:change="filter_data"
            multiple
          >
          </b-form-select>
        </b-col>
      </b-row>
      <b-row>
        <b-col>
          <ParallelCoords
            :locations="
              this.start == false
                ? this.cc_loy_filtered.map((d) => d.location)
                : this.cc_loy_locations
            "
            :dates="
              this.start == false
                ? this.cc_loy_filtered.map((d) => d.Date)
                : this.cc_loy_dates
            "
            :hours="
              this.start == false
                ? this.cc_loy_filtered.map((d) => d.Hour)
                : this.cc_loy_hours
            "
            :minutes="
              this.start == false
                ? this.cc_loy_filtered.map((d) => d.Minute)
                : this.cc_loy_minutes
            "
            :prices="
              this.start == false
                ? this.cc_loy_filtered.map((d) => d.price)
                : this.cc_loy_prices
            "
            :nums="
              this.start == false
                ? this.cc_loy_filtered.map((d) => d.num)
                : this.cc_loy_nums
            "
            :start="this.start"
          ></ParallelCoords>
        </b-col>
      </b-row>
    </b-container>
    <hr id="section_separator" />
    <b-container>
      <b-row>
        <b-col>
          <Map :data_="this.gps_data"></Map>
        </b-col>
      </b-row>
    </b-container>

    <b-jumbotron
      class="jumbotron"
      text-variant="white"
      border-variant="danger"
      id="footer"
    >
      <div class="footer_item" id="linkedin">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="30"
          height="30"
          fill="#0072b1"
          class="bi bi-linkedin"
          viewBox="0 0 16 16"
        >
          <path
            d="M0 1.146C0 .513.526 0 1.175 0h13.65C15.474 0 16 .513 16 1.146v13.708c0 .633-.526 1.146-1.175 1.146H1.175C.526 16 0 15.487 0 14.854V1.146zm4.943 12.248V6.169H2.542v7.225h2.401zm-1.2-8.212c.837 0 1.358-.554 1.358-1.248-.015-.709-.52-1.248-1.342-1.248-.822 0-1.359.54-1.359 1.248 0 .694.521 1.248 1.327 1.248h.016zm4.908 8.212V9.359c0-.216.016-.432.08-.586.173-.431.568-.878 1.232-.878.869 0 1.216.662 1.216 1.634v3.865h2.401V9.25c0-2.22-1.184-3.252-2.764-3.252-1.274 0-1.845.7-2.165 1.193v.025h-.016a5.54 5.54 0 0 1 .016-.025V6.169h-2.4c.03.678 0 7.225 0 7.225h2.4z"
          />
        </svg>
        <a href="https://www.linkedin.com/in/francesco-di-cursi-857724204/">
          Visit Linkedin</a
        >
      </div>
      <hr id="item_separetor" />
      <div class="footer_item" id="github">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="30"
          height="30"
          fill="#333"
          class="bi bi-github"
          viewBox="0 0 16 16"
        >
          <path
            d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.012 8.012 0 0 0 16 8c0-4.42-3.58-8-8-8z"
          />
        </svg>
        <a href="https://github.com/FrancescoDiCursi?tab=repositories">
          Visit Github</a
        >
      </div>
      <hr id="item_separetor" />
      <div class="footer_item" id="presentation">
        <b>Francesco Di Cursi</b> — Digital Humanities (Unipi) —
        <b>Visual Analytics </b> — 06/2022
      </div>
    </b-jumbotron>
    {{ this.bar_2d() }}
  </span>
</template>

<script>
import Vue from "vue";
import { BootstrapVue, IconsPlugin } from "bootstrap-vue";
// Import Bootstrap and BootstrapVue CSS files (order is important)
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";


// Make BootstrapVue available throughout your project
Vue.use(BootstrapVue);
// Optionally install the BootstrapVue icon components plugin
Vue.use(IconsPlugin);

import * as d3 from "https://cdn.skypack.dev/d3@7";
import Map from "./components/Map.vue";
import ParallelCoords from "./components/ParallelCoords.vue";

let count_vals = [];
let price_vals = [];
let ratio_vals = [];

export default {
  name: "App",
  components: { Map, ParallelCoords },
  data() {
    return {
      cc_loy_locations: [],
      cc_loy_dates: [],
      cc_loy_hours: [],
      cc_loy_minutes: [],
      cc_loy_prices: [],
      cc_loy_ratio: [],
      cc_loy_nums: [],
      cc_loy_data: {},
      cc_loy_filtered: {},
      selected_nums: [],
      selected_dates: [],
      selected_hours: [],
      selected_minutes: [],
      selected_locations: [],
      selected_cardtypes: ["All"],
      dates_list: [],
      locations_list: [],
      nums_list: [],
      parallel_selections: [],

      start: true,

      gps_data: {},

      plot2d_selected_date: "",
      plot2d_selected_location: "",

      heat_map_filter: "Ratio",
      locations_freq: [],
      locations_price: [],
      locations_ratio: [],
    };
  },
  mounted() {
    Promise.all([
      d3.csv("./static/cc_loy.csv"),
      d3.csv("./static/gps_assign_merged.csv"),
    ]).then((csvs) => {
      this.cc_loy_locations = csvs[0].map((d) => d.location);
      this.cc_loy_dates = csvs[0].map((d) => d.Date);
      this.cc_loy_hours = csvs[0].map((d) => d.Hour);
      this.cc_loy_minutes = csvs[0].map((d) => d.Minute);
      this.cc_loy_prices = csvs[0].map((d) => +d.price);
      this.cc_loy_ratio = csvs[0].map((d) => +d.price_count_ratio);
      this.cc_loy_nums = csvs[0].map((d) => d.num);
      this.cc_loy_data = csvs[0];
      this.cc_loy_filtered = csvs[0];
      var dates_temp = new Set(csvs[0].map((d) => d.Date));
      this.dates_list = [...dates_temp].sort();
      var hours_temp = new Set(csvs[0].map((d) => d.Hour));
      this.hours_list = [...hours_temp].sort();
      var minutes_temp = new Set(csvs[0].map((d) => d.Minute));
      this.minutes_list = [...minutes_temp].sort();
      var locations_temp = new Set(csvs[0].map((d) => d.location));
      this.locations_list = [...locations_temp].sort();
      var nums_temp = new Set(csvs[0].map((d) => d.num));
      this.nums_list = [...nums_temp].sort();

      this.gps_data = csvs[1];

      let c = 0;
      let p = 0;
      let r = 0;

      let locations_list = [...locations_temp].sort();
      let cc_loy_locations = csvs[0].map((d) => d.location);

      let temp_prices = csvs[0].map((d) => +d.price);
      let temp_ratios = csvs[0].map((d) => +d.price_count_ratio);

      for (let i = 0; i < locations_list.length; i++) {
        for (let j = 0; j < cc_loy_locations.length; j++) {
          if (locations_list[i] == cc_loy_locations[j]) {
            //update freq
            c++;
            //update price
            p += temp_prices[j];
            //update ratio
            r += temp_ratios[j];
          }
        }
        count_vals.push(c);
        price_vals.push(p);
        ratio_vals.push(r);
        c = 0;
        p = 0;
        r = 0;
      }
      this.start_count = false;
      this.locations_freq = count_vals;
      this.locations_price = price_vals;
      this.locations_ratio = ratio_vals;
    });
  },
  methods: {
    heat_ratio() {
      this.heat_map_filter = "Ratio";
    },
    heat_price() {
      this.heat_map_filter = "Price";
    },
    heat_freq() {
      this.heat_map_filter = "Frequency";
    },
    filter_data() {
      this.start = false;
      var num_val = this.selected_nums;
      var dates_val = this.selected_dates;
      var locations_val = this.selected_locations;
      var hours_val = this.selected_hours;
      var minutes_val = this.selected_minutes;

      if (this.selected_cardtypes[0] == "Credit card") {
        this.selected_nums = [];
        this.cc_loy_filtered = this.cc_loy_data.filter(
          (d) => !d.num.includes("L")
        );
        if (this.selected_dates.length > 0) {
          this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
            dates_val.includes(d.Date)
          );
        }
        if (this.selected_locations.length > 0) {
          this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
            locations_val.includes(d.location)
          );
        }
        if (this.selected_hours.length > 0) {
          this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
            hours_val.includes(d.Hours)
          );
        }
        if (this.selected_minutes.length > 0) {
          this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
            minutes_val.includes(d.Minute)
          );
        }
      } else if (this.selected_cardtypes[0] == "Loyalty card") {
        this.selected_nums = [];
        this.cc_loy_filtered = this.cc_loy_data.filter((d) =>
          d.num.includes("L")
        );
        if (this.selected_dates.length > 0) {
          this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
            dates_val.includes(d.Date)
          );
        }
        if (this.selected_locations.length > 0) {
          this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
            locations_val.includes(d.location)
          );
        }
      } else if (this.selected_cardtypes[0] == "All") {
        if (
          num_val.length == 0 &&
          dates_val.length == 0 &&
          locations_val.length == 0 &&
          hours_val.length == 0 &&
          minutes_val.length == 0
        ) {
          this.cc_loy_filtered = this.cc_loy_data;
        } //incolla qui
        else {
          this.cc_loy_filtered = this.cc_loy_data;
          if (num_val.length > 0) {
            this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
              num_val.includes(d.num)
            );
          }
          if (dates_val.length > 0) {
            this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
              dates_val.includes(d.Date)
            );
          }
          if (hours_val.length > 0) {
            this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
              hours_val.includes(d.Hour)
            );
          }
          if (minutes_val.length > 0) {
            this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
              minutes_val.includes(d.Minute)
            );
          }
          if (locations_val.length > 0) {
            this.cc_loy_filtered = this.cc_loy_filtered.filter((d) =>
              locations_val.includes(d.location)
            );
          }
        }
      }
    },
    filter_marginal_x(values, max, width_) {
      var data = [
        {
          x: this.cc_loy_locations.map(String),
          type: "histogram",
          marker: {
            cmax: max,
            cmin: 0,
            color: values,
            colorscale: "Hot",
            colorbar: {
              colorscale: "Hot",
            },
          },
        },
      ];
      var layout = {
        xaxis: { categoryorder: "category ascending", showticklabels: false },
        margin: {
          b: 0,
          l: 100,
          r: 30,
        },
        width: width_,
        height: 200,
      };

      return Plotly.newPlot("marginal_x", data, layout, {
        displayModeBar: false,
      });
    },
    marginal_x() {
      if (
        (this.heat_map_filter == "Frequency") &
        (this.locations_freq.length != 0)
      ) {
        this.filter_marginal_x(
          this.locations_freq,
          Math.max(...this.locations_freq) + 100,
          1007
        );
      }
      if (
        (this.heat_map_filter == "Ratio") &
        (this.locations_ratio.length != 0)
      ) {
        this.filter_marginal_x(
          this.locations_ratio.map((d) => Math.sqrt(d)),
          Math.sqrt(Math.max(...this.locations_ratio)) + 100,
          1000
        );
      }
      if (
        (this.heat_map_filter == "Price") &
        (this.locations_price.length != 0)
      ) {
        this.filter_marginal_x(
          this.locations_price.map((d) => Math.sqrt(d)),
          Math.sqrt(Math.max(...this.locations_price)) + 100,
          1000
        );
      }
    },
    bar_2d() {
      if (!document.getElementById("heat_")) {
        //create header dynamically
        let navbar = document.createElement("b-navbar");
        navbar.id = "header_";
        let brand = document.createElement("b-navbar-brand");
        let link_gastech = document.createElement("a");
        link_gastech.href = "https://vast-challenge.github.io/2021/MC2.html";
        let gastech_logo = document.createElement("img");
        gastech_logo.id = "gastech_logo";
        gastech_logo.src = "static//gastech_logo.png";
        let header_title = document.createElement("b-nav-item");
        let title_text = document.createTextNode(
          "VAST Mini-Challenge 2 (2021)"
        );
        header_title.id = "title_text";
        header_title.append(title_text);

        link_gastech.append(gastech_logo);
        brand.append(link_gastech);
        navbar.append(brand);
        navbar.append(header_title);

        //create first section dinamically
        let div_marginalx = document.createElement("div");
        let div_bar2d = document.createElement("div");
        let div_bar2d_filter = document.createElement("div");
        div_marginalx.id = "marginal_x"; //cambiare ovunque da heat_ a bar2d_
        div_bar2d.id = "heat_";
        div_bar2d_filter.id = "bar2d_filter";
        document.body.insertBefore(div_bar2d, document.body.firstChild);
        document.body.insertBefore(div_marginalx, document.body.firstChild);
        document.body.insertBefore(navbar, document.body.firstChild);
      }
      this.marginal_x();
      var data;
      if (this.heat_map_filter == "Ratio") {
        data = [
          {
            y: this.cc_loy_dates.map(String),
            x: this.cc_loy_locations.map(String),
            z: this.cc_loy_ratio.map((d) => Math.sqrt(d)),
            histfunc: "max",
            colorscale: "Hot",
            type: "histogram2d",
          },
        ];
      } else if (this.heat_map_filter == "Price") {
        data = [
          {
            y: this.cc_loy_dates.map(String),
            x: this.cc_loy_locations.map(String),
            z: this.cc_loy_prices.map((d) => Math.sqrt(d)),
            histfunc: "sum",
            colorscale: "Hot",
            type: "histogram2d",
          },
        ];
      } else if (this.heat_map_filter == "Frequency") {
        data = [
          {
            y: this.cc_loy_dates.map(String),
            x: this.cc_loy_locations.map(String),
            histfunc: "sum",
            colorscale: "Hot",
            type: "histogram2d",
          },
        ];
      }

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
          categoryorder: "category ascending",
        },
      };

      Plotly.newPlot("heat_", data, layout, {
        displayModeBar: false,
      });
      var plot_div = document.getElementById("heat_");
      plot_div.on("plotly_click", function (event) {
        this.plot2d_selected_date = event.points[0].y;
        this.plot2d_selected_location = event.points[0].x;

        this.selected_dates = [event.points[0].y];
        this.selected_locations = [event.points[0].x];

        const date_filt = document.getElementById("date_filter");
        const location_filt = document.getElementById("location_filter");

        date_filt.value = this.plot2d_selected_date;
        location_filt.value = this.plot2d_selected_location;

        const event_ = new Event("change");
        date_filt.dispatchEvent(event_);
        location_filt.dispatchEvent(event_);
      });
    },
  }
};
</script>


<style>
.plot_container {
  background: white;
  width: 100%;
}
#marginal_x {
  top: 10%;
}
#marginal_x,
#heat_ {
  width: 100%;
  margin-left: 20vh;
  margin-right: "auto";
}
#section_separator {
  background-color: black;
  width: 250vh;
}
#parallel_ {
  width: 100vh;
  margin-left: -10vh;
}

.jumbotron {
  padding-top: 5px;
  padding-bottom: 5px;
  margin: 0;
  margin-top: 25px;
}
.footer_item {
  background: white;
  border-color: grey;
  border-width: 0.01px;
  border-style: ridge;
  text-align: center;
}
#linkedin {
  margin-bottom: -10px;
}
#github {
  margin-top: -10px;
}
#header_ {
  background: grey;
  width: 250vh; /* USE VH AS MEASURE FOR WIDTH IN @MEDIA (use 100,200,300) */

  position: absolute;
  z-index: 1;
}
#gastech_logo {
  width: 70px;
  border-radius: 45%;
  margin-top: 5px;
  margin-bottom: 5px;
  margin-left: 5px;
}

#title_text {
  color: white;
  font-family: "Jet";

  font-size: 30px;

  position: absolute;
  left: 75vh;

  transform: translateY(30%);
}

#presentation {
  color: black;
}
</style>
