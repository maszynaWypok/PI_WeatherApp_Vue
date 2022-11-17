<template>
    <div id="uvindex">
      <p>Index UV: {{ uvindex }}</p>
    </div>
  </template>
  
  <script>
  
  export default {
    props: ["highlights"],
    data () {
      return {
        uvindex: this.highlights.uvindex
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
  