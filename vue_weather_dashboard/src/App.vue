<template>
  <div id="app">
    <p> Kod kompnentów jest w {{filename}}</p>
    <dashboard-content :highlights="highlights" :tempVar="tempVar"></dashboard-content>
  </div>
</template>

<script>
import { computed } from 'vue';
import Content from './components/Content.vue'

export default {
  name: 'app',
  components: {
    'dashboard-content': Content
  },
  data () {
    return {
      filename: 'App.vue',
      tempVar: {
        tempToday: [
          {hour: '11.00 AM', temp: '35'},
          {hour: '12.00 AM', temp: '36'},
          {hour: '1.00 AM', temp: '37'},
          {hour: '2.00 AM', temp: '38'},
          {hour: '3.00 AM', temp: '36'},
          {hour: '4.00 AM', temp: '35'},
        ],
      },
      highlights: {
        uvIndex: 4,
        visibility: 10,
        windStatus: {
          windSpeed: '30 km/h',
          windDirectionDegrees: '30',
          windDirectionDir: 'NNE',
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
