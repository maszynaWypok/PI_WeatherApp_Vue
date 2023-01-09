<template>
  <div class="highlights-item col-md-4 col-sm-6 col-xs-12 border-left border-right border-top">
    <div class="card-heading pt-5">Visibility</div>
    <div class="row pt-4 mt-4">
      <GChart :settings="{ packages: ['bar'] }" :data="chartData" :options="chartOptions"
        :createChart="(el, google) => new google.charts.Bar(el)" @ready="onChartReady" />
    </div>
    <p class="card-value mt-4">{{ highlights.visibility }} </p>
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
    onChartReady(chart, google) {
      this.chartsLib = google
    },
  },
  computed: {},
  data() {
    return {
      visibilityNow: '',
      chartData: [
        ["Condition", "Visibility", "Fog", "Haze", "Clear"],
        ["Now", parseInt(this.highlights.visibility), 0, 0, 0],
        ["Conditions", '', 4000, 4000, 2000],
      ],
      chartOptions: {
        title: 'Visibility in metres',
        width: "100%",
        height: "100%",
        bars: 'horizontal',
        stacked: true,
        hAxis: {
          viewWindow: {
            min: 0,
            max: 10000
          },
          gridlines: {
            count: 10
          },
          ticks: [4000, 8000, 10000]
        },
        chartArea: {
          height: '75%',
          top: 10
        }

      }
    }
  },
  watch: {
    highlights: {
      handler: function () {
        this.highlights.visibility;
      },
      deep: true
    }
  }
}
</script>
