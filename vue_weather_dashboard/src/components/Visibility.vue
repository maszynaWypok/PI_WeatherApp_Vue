<template>
    <div id="visibility">
      <p>Widoczność: {{ visibility }}</p>
    </div>
  </template>
  
  <script>
  
  export default {
    props: ["highlights"],
    data () {
      return {
        visibility: this.highlights.visibility.toString(),
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
  