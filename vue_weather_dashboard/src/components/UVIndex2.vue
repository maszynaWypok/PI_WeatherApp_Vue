<template>
    <div class="highlights-item col-md-4 col-sm-6 col-xs-12 border-left border-right border-top">
    <div class="card-heading pt-5">UV Index</div>
    <div id="gauge" class="row pt-4 mt-4 text-center align-middle">
        <GChart :type="type" :data="chartData" :options="chartOptions" :settings="settings" />
        </div>
        <p class="card-value mt-4">{{ highlights.uvIndex }} </p>
    </div>
   </template>
   
   <script>
   import { GChart } from 'vue-google-charts/legacy'
   export default {
    props: ["highlights"],
    components: {
        GChart
    },
    methods: {
    onChartReady (chart, google) {
      this.chartsLib = google},
        },
    computed: {},
    data() {
      return {
        type: 'Gauge',
        settings: {
            packages: ['gauge'],
        },
        chartData: [
            ["Label", "Value"],
            ["UV Index", parseInt(this.highlights.uvIndex)],
        ],
        chartOptions: {
            max: 12,
            redTo: 12,
            redFrom: 8,
            yellowFrom: 3,
            yellowTo: 8,
            chartArea: {
                height: '75%',
                top: 10
            }
            
        }
        }
    },
    watch: {
        highlights: {
            handler: function(value) {
                console.log(value)
            }
        },
    deep: true,
    immediate: true
    }
  }
   </script>
   <style>
    #gauge > div > table {
        width: auto;
        margin: 0 auto !important;
    }
</style>
   