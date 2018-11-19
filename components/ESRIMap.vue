<template>
  <section class="container">
    <div id="viewDiv"></div>
  </section>
</template>
<script>
import { loadModules } from 'esri-loader'
/* import ResetMapButton from '../components/resetMapButton.vue'
import Vue from 'vue'
var ButtonComponent = Vue.extend(ResetMapButton)
var resetButton = new ButtonComponent()
resetButton.id = 'resetButtonId'
console.log(resetButton.id) */
export default {
  // components: { ResetMapButton },
  /* data () {
  },
  head () {
  }, */
  mounted () {
    console.log('map: mounted')
    loadModules([
      'esri/Map', 'esri/views/SceneView', 'esri/layers/FeatureLayer', 'esri/core/watchUtils'
    ], {
      // use a specific version instead of latest 4.x
      url: 'https://js.arcgis.com/4.2/'
    }).then(([EsriMap, SceneView, FeatureLayer, watchUtils]) => {
      // create map with the given options at a DOM node w/ id 'mapNode'
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
        map.layers.forEach(layer => {
          console.log(layer.id)
        })
        this.$store.state.map = map
      } else {
        map = this.$store.state.map
      }
      console.log('this.$store.state.camera.tilt: ' + this.$store.state.camera.tilt)
      var view = new SceneView({
        container: 'viewDiv',
        map,
        camera: this.$store.state.camera
      })
      console.log('sceneSet')
      console.log('afterviewThen')
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

</style>

