<template>
    <div id="windStatus">
      <p>Wiatr: - {{ windSpeed}}</p>
      <p>Kierunek — {{ windDirectionDegrees }} stopni - {{windDirectionDir}}. 0 stopni = N</p>
    </div>
  </template>
  
  <script>
  
  export default {
    props: ["highlights"],
    data () {
      return {
        windSpeed: this.highlights.windStatus.windSpeed,
        windDirectionDegrees: this.highlights.windStatus.windDirectionDegrees,
        windDirectionDir: this.highlights.windStatus.windDirectionDir
      }
    },
    methods: {
  
    },
    computed: {
  
    },
  }
  </script>
  
  <style>
  
  </style>
  