<template>
  <div class="flex flex-col  min-h-screen font-Roboto bg-weather-primary">
    <navigation/>
    <RouterView/>
  </div>
</template>

<script>
import {RouterView} from "vue-router"
import Navigation from './components/Navigation.vue'
export default {
  components: { Navigation },

}
</script>

<style>

</style>