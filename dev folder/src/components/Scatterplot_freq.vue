<script>
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
export default {
  name: "Scatterplot_freq",
  props: { freq_: Object },
  data() {
    return {
      x_: [],
      y_: [],
      filt_type: "Frequency",
      filt_options: ["Frequency", "N items"],
      start_flag: true,
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
        } else if (this.filt_type == this.filt_options[0]) {
          var filt_x = [];
          var filt_y = [];
          for (let i = 0; i < this.x_.length; i++) {
            if (this.y_[i] <= filt_value) {
              filt_x.push(this.x_[i]);
              filt_y.push(this.y_[i]);
            }
          }
        }
      } else if(this.start_flag=true){
        var filt_x = this.x_;
        var filt_y = this.y_;
      }
      var trace = {
        x: filt_x,
        y: filt_y,
        mode: "lines+markers",
        type: "scatter",
      };
      var layout = {};
      Plotly.newPlot("freq_plot_single", [trace], layout);
      this.start_flag = false;
    },
  },

  watch: {
    y_: function () {
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