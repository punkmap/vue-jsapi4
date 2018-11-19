<template>
  <section class="container">
    <img src="../assets/img/logo.png" alt="Nuxt.js Logo" class="logo" />
    <h1 class="title">
      This page is loaded from the {{ name }}
    </h1>
    <h2 class="info" v-if="name === 'client'">
      Please refresh the page
    </h2>
    <!--todo: pull map out into component-->
    <ESRIMap id="thisIsMap"/>
    <nuxt-link class="button" to="/">
      Home page
    </nuxt-link>
    <nuxt-link class="button" to="/about">
      About page
    </nuxt-link>
    <resetMapButton type="button">I'm a button. Here I am.</resetMapButton>
  </section>
</template>
<script>
// import { loadModules } from 'esri-loader'
import ResetMapButton from '../components/resetMapButton.vue'
import ESRIMap from '../components/ESRIMap.vue'
import Vue from 'vue'
var ButtonComponent = Vue.extend(ResetMapButton)
var resetButton = new ButtonComponent()
resetButton.id = 'resetButtonId'
console.log(resetButton.id)
export default {
  components: { ResetMapButton, ESRIMap },
  data ({ req }) {
    return {
      name: req ? 'server' : 'client'
    }
  },
  head () {
    return {
      title: `Map Page (${this.name}-side)`
    }
  }
}
</script>

<style scoped>
#thisIsMap
{
  height: 1000px;
  width: 100%;
}
.title
{
  margin-top: 50px;
}
.info
{
  font-weight: 300;
  color: #9aabb1;
  margin: 0;
  margin-top: 10px;
}
.button
{
  margin-top: 50px;
}
</style>
