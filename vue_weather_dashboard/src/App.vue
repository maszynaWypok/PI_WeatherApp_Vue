<template>
  <div id="app">
    <p> Kod kompnentów jest w {{filename}}</p>
    <Content v-bind:weather_data="weather_data"></Content>
  </div>
</template>

<script>
import { computed } from 'vue';
import Content from './components/Content.vue'

export default {
  name: 'app',
  components: {
    'Content': Content
  },
  data () {
    return {
      filename: 'App.vue',
      weather_data: {
        location: "Warsaw",
        temperature: {
          current: "35 C",
        },
      highlights: {
        uvindex: "3",
        windstatus: {
          speed: "20 km/h",
          direction: "N-E",
        },
      visibility: "12 km",
      },
      },
    }
  },
  methods: {


  },
  computed: {


  }
}
</script>

<style>

</style>
