<template>
    <div id="windStatus">
      <p>Wiatr: - {{ windSpeed}}</p>
      <p>Kierunek — {{ windDirection }}</p>
    </div>
  </template>
  
  <script>
  
  export default {
    props: ["highlights"],
    data () {
      return {
        windSpeed: this.highlights.windStatus.windSpeed,
        windDirection: this.highlights.windStatus.windDirection
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
  