<script>
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
import * as d3 from "https://cdn.skypack.dev/d3@7";

export default {
  name: "Distplot_style",
  props: { tokens_: Array },
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
      books: [],
      target_books: [],
    };
  },
  mounted() {
    console.log(this.tokens_);
    this.books = [...new Set(this.tokens_.map((d) => d[0]))];
    this.target_books = [this.books[0]];
    setTimeout(this.draw_distplot, 1000);
  },
  methods: {
    draw_distplot() {
      //THREE MODES
      //1-complessive
      //2-grouped by book
      //3-grouped by book-chapter
      //4-per verses (?)

      var verses_idx = this.tokens_.map((d) => d[2][0]);
      var verses = this.tokens_.map((d) => d[2].slice(1));
      //LEGEND:
      //COORDS=POSITIVE
      //SUBORDS=NEGATIVE
      // the higher the value, the more it is paratactic
      //the lower the value, the more it is hypotactic
      var coords_vals = [];
      var subord_vals = [];

      for (let i = 0; i < this.tokens_.length; i++) {
        var counter_coords = 0;
        for (let j = 0; j < this.tokens_[i][2].length; j++) {
          if (this.coord_conj.includes(this.tokens_[i][2][j].toLowerCase())) {
            counter_coords += 1;
          }
        }
        var counter_subords = 0;
        for (let j = 0; j < this.tokens_[i][2].length; j++) {
          if (this.subord_conj.includes(this.tokens_[i][2][j].toLowerCase())) {
            counter_subords += 1;
          }
        }

        coords_vals.push(counter_coords);
        if (counter_subords == 0) {
          subord_vals.push(counter_subords);
        } else {
          subord_vals.push(-Math.abs(counter_subords));
        }
        //NEGATIVE
      }

      var color_vals = coords_vals.map((d, i) => d + subord_vals[i]);
      console.log("VALS", coords_vals, subord_vals, color_vals);
      var tokens_with_color = this.tokens_.map((d, i) => [
        d[0],
        d[1],
        d[2],
        color_vals[i],
      ]);

      var colorscale = d3.scaleDivergingSymlog(
        [-10, 0, 10],
        d3.interpolatePiYG
      );

      //if BOOKS=ALL make a plot for each book (vertically)
      //else only trace1
      for (let i = 0; i < this.target_books.length; i++) {
        var temp_target_book = this.target_books[i];
        var temp_data = tokens_with_color.filter(function (d) {
          if (d[0] == temp_target_book) {
            return d;
          }
        });
        var trace1 = {
          type: "parcats",
          hoveron: "color",
          hoverinfo: "count+probability",
          name: color_vals.map((d) => String(d)),

          dimensions: [
            { labels: "Book", values: temp_data.map((d) => d[0]) },
            { labels: "Chapter", values: temp_data.map((d) => d[1]) },
            { labels: "Verse", values: temp_data.map((d) => d[2][0]) },

            //categoryorder]
          ],
          line: {
            color: temp_data[3],
            colorscale: colorscale(),
            colorbar: {
              orientation: "v",
            },
          },
        };

        var layout = {
          height: 1000,
        };

        Plotly.newPlot("distplot_style_single" + String(i), [trace1], layout);
      }
    },
  },
  watch: {
    target_books: function () {
      //setTimeout(this.draw_distplot, 1000); //better if with button, otherwise potential lagging on slow multiselect
    },
  },
};
</script>

<template>
  <b-container>
    <b-row
      ><b-select
        v-model="target_books"
        :options="books"
        id="book_selector_parallel"
        multiple
        :select-size="3"
      ></b-select
    ></b-row>
    <b-row><b-button @click="this.draw_distplot">Redraw</b-button></b-row>
    <b-row
      :id="'distplot_style_single' + String(i)"
      v-for="(book, i) in this.books"
      :key="String(i) + book"
    ></b-row>
  </b-container>
</template>