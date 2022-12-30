<template>
    <div id="uvIndex">
      <p>Index UV: {{ uvindex }}</p>
    </div>
  </template>
  
  <script>
  
  export default {
    props: ["highlights"],
    data () {
      return {
        uvindex: this.highlights.uvIndex
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
  