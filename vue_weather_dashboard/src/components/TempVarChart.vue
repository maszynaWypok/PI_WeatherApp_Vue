<template>
    <div class="custom-card header-card card">
    <div class="card-heading pt-5">Temperature</div>
      <div id="fusion" class="card-body pt-0">
        <GChart 
                :data="chartData" 
                :options="chartOptions" 
                :settings="settings"
                :createChart="(el, google) => new google.charts.Line(el)" />
      </div>
    <!-- <p class="card-value mt-4">{{ tempVar.tempToday }} </p> -->
     </div>
   </template>
   
   <script>
   import { GChart } from 'vue-google-charts/legacy'
   export default {
    props: ["tempVar"],
    components: {
        GChart
    },
    
mounted: {
 function() {
   this.setChartData(); }
    }, 
    computed: {},
    data() {
      return {
        settings: {
            packages: ['line'],
        },
        chartData: [
            ["Hour", "Temp"],
            [this.tempVar.tempToday[0].hour, parseFloat(this.tempVar.tempToday[0].temp)],
            [this.tempVar.tempToday[1].hour, this.tempVar.tempToday[1].temp],
            [this.tempVar.tempToday[2].hour, this.tempVar.tempToday[2].temp],
            [this.tempVar.tempToday[3].hour, this.tempVar.tempToday[3].temp],
            [this.tempVar.tempToday[4].hour, this.tempVar.tempToday[4].temp],
            [this.tempVar.tempToday[5].hour, this.tempVar.tempToday[5].temp],
            [this.tempVar.tempToday[6].hour, this.tempVar.tempToday[6].temp],
            [this.tempVar.tempToday[7].hour, this.tempVar.tempToday[7].temp],
            [this.tempVar.tempToday[8].hour, this.tempVar.tempToday[7].temp],
            [this.tempVar.tempToday[9].hour, this.tempVar.tempToday[9].temp],
            [this.tempVar.tempToday[10].hour, this.tempVar.tempToday[10].temp],
            [this.tempVar.tempToday[11].hour, this.tempVar.tempToday[11].temp],
            
        ], 
        
        chartOptions: {
            legend: 'none',
            chartArea: {
                width: '80%'
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
   </style>
   