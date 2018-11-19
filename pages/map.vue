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
    <div id="viewDiv"></div>
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
import { loadModules } from 'esri-loader'
import ResetMapButton from '../components/resetMapButton.vue'
import Vue from 'vue'
var ButtonComponent = Vue.extend(ResetMapButton)
var resetButton = new ButtonComponent()
resetButton.id = 'resetButtonId'
console.log(resetButton.id)
export default {
  components: { ResetMapButton },
  data ({ req }) {
    return {
      name: req ? 'server' : 'client'
    }
  },
  head () {
    return {
      title: `Map Page (${this.name}-side)`
    }
  },
  mounted () {
    console.log('map: mounted')
    loadModules([
      'esri/Map', 'esri/views/SceneView', 'esri/layers/FeatureLayer', 'esri/core/watchUtils'
    ], {
      // use a specific version instead of latest 4.x
      url: 'https://js.arcgis.com/4.2/'
    }).then(([EsriMap, SceneView, FeatureLayer, watchUtils]) => {
      // create map with the given options at a DOM node w/ id 'mapNode'
      /* var renderer = {
        type: 'simple',  // autocasts as new SimpleRenderer()
        symbol: {
          type: 'simple-fill',  // autocasts as new SimpleFillSymbol()
          color: [ 255, 128, 0, 0.5 ],
          outline: {  // autocasts as new SimpleLineSymbol()
            width: 1,
            color: 'white'
          }
        }
      } */

      var ashvlParks = new FeatureLayer({
        // URL to the service
        url: 'https://arcgis.ashevillenc.gov/arcgis/rest/services/Planning/Parks/FeatureServer/0',
        outFields: ['*'],
        id: 'ashvlParks',
        // renderer: renderer,
        opacity: 1,
        visible: true,
        elevationInfo: {
          mode: 'on-the-ground'
        }
      })
      var treeRenderer = {
        type: 'simple',  // autocasts as new SimpleRenderer()
        symbol: {
          type: 'simple-marker',  // autocasts as new SimpleMarkerSymbol()
          size: 6,
          color: 'black',
          outline: {  // autocasts as new SimpleLineSymbol()
            width: 0.5,
            color: 'white'
          }
        }
      }
      var ashvlTrees = new FeatureLayer({
        // URL to the service
        url: 'https://arcgis.ashevillenc.gov/arcgis/rest/services/Arts_Culture_History/OpenTreeMap/FeatureServer/0',
        outFields: ['*'],
        id: 'ashvlTrees',
        opacity: 1,
        visible: true,
        elevationInfo: {
          mode: 'on-the-ground'
        }
      })
      let map
      if (!this.$store.state.map) {
        map = new EsriMap({
          basemap: 'satellite',
          layers: [ashvlParks, ashvlTrees],
          ground: 'world-elevation'
        })
        console.log(treeRenderer)
        // ashvlTrees.renderer = treeRenderer
        console.log('layer.id:')
        map.layers.forEach(layer => {
          console.log(layer.id)
        })
        this.$store.state.map = map
      } else {
        map = this.$store.state.map
      }
      var view = new SceneView({
        container: 'viewDiv',
        map,
        camera: this.$store.state.camera
      })
      view.then(function () {
        console.log('theSceneViewIsNowLoaded')
        // All the resources in the MapView and the map have loaded. Now execute additional processes
      }, function (error) {
        // Use the errback function to handle when the view doesn't load properly
        console.log("The view's resources failed to load: ", error)
      })

      this.$store.state.watchHandle = watchUtils.watch(view, 'camera', (camera) => {
        console.log('change')
        this.$store.state.camera = camera.clone().toJSON()
      })

      // NOTE: important: now that we're using a promise
      // your callback must NOT return any v4.x classes that resolve to promises
      // this will cause a hole in the space-time continum that will kill us all
      // return view
    })
  },
  beforeDestroy () {
    console.log('map: beforeDestroy')
    this.$store.state.watchHandle.remove()
  }
}
</script>

<style scoped>
@import url('https://js.arcgis.com/4.2/esri/css/main.css');

#viewDiv {
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
