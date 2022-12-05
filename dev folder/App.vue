<script>
import Vue from 'vue'
import * as plotly from "https://cdn.plot.ly/plotly-2.11.1.min.js";
//import * as d3 from "https://cdn.skypack.dev/d3@7";
import * as d3 from  "https://cdn.jsdelivr.net/npm/d3@7.7.0/+esm"
import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'

// Import Bootstrap and BootstrapVue CSS files (order is important)
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
import Single_section from './components/Single_section.vue';
import Binary_section from './components/Binary_section.vue';
import Multiple_section from './components/Multiple_section.vue'

// Make BootstrapVue available throughout your project
Vue.use(BootstrapVue)
// Optionally install the BootstrapVue icon components plugin
Vue.use(IconsPlugin)

export default {
  name:'App',
  components:{ Single_section, Binary_section, Multiple_section },
  data(){
    return{

      target_page:'single',
      single:true,
      binary:false,
      multiple:false

    }
  },
  mounted(){

  },
  methods:{
    update_page(name){
      this.target_page = name;

      if (this.target_page=='single'){
        this.binary=false
        this.multiple=false
        this.single=true
      }else if (this.target_page=='binary'){
        this.single=false
        this.multiple=false
        this.binary=true
      }else if (this.target_page=='multiple'){
        this.single=false
        this.binary=false
        this.multiple=true
      }
    }

  },
  watch:{

  }
}
</script>

<template>
  <div id="app">
    
    <div>
  <b-navbar toggleable="lg" type="dark" variant="dark">
    <b-navbar-brand id="navbrand">NavBar</b-navbar-brand>

    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav>
        <b-nav-item id='single_btn' href="#" @click="update_page('single')">Single</b-nav-item>
        <b-nav-item id='binary_btn' href="#" @click="update_page('binary')" >Binary</b-nav-item>
        <b-nav-item id='multiple_btn' href="#" @click="update_page('multiple')" >Multiple</b-nav-item>

      </b-navbar-nav>

    </b-collapse>
  </b-navbar>
</div>


  <section id="single" v-if="single">
  <Single_section></Single_section>
  </section>
  <section id="binary" v-if="binary">
  <Binary_section></Binary_section>
  </section>
  <section id="multiple" v-if="multiple">
  <Multiple_section></Multiple_section>
  </section>


  </div>
</template>

<style scoped>

#navbrand{
  margin-left:2rem
}
#single_btn,#binary_btn, #multiple_btn{
  margin-left:5rem !important
}
</style>

