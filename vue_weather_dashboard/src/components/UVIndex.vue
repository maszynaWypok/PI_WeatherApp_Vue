<template>
  <div class="highlights-item col-md-4 col-sm-6 col-xs-12 border-top">
    <div id="fusion-uv">
      <fusioncharts
        :type="type"
        :width="width"
        :height="height"
        :containerbackgroundopacity="containerbackgroundopacity"
        :dataformat="dataFormat"
        :dataSource="dataSource"
      ></fusioncharts>
      <p class="card-value mt-4">{{ highlights.uvIndex }}</p>
    </div>
  </div>
 </template>
 
 <script>
 export default {
  props: ["highlights"],
  components: {},
  data() {
    return {
      type: "angulargauge",
      renderAt: "fusion-uv",
      width: "100%",
      height: "100%",
      containerbackgroundopacity: 0,
      dataFormat: "json",
      dataSource: {
        chart: {
          caption: "UV Index",
          captionFontBold: "0",
          captionFontColor: "#000000",
          captionPadding: "30",
          lowerLimit: "0",
          upperLimit: "15",
          lowerLimitDisplay: "1",
          upperLimitDisplay: "1",
          showValue: "0",
          theme: "fusion",
          baseFont: "Roboto",
          bgAlpha: "0",
          canvasbgAlpha: "0",
          gaugeInnerRadius: "75",
          gaugeOuterRadius: "110",
          pivotRadius: "0",
          pivotFillAlpha: "0",
          valueFontSize: "20",
          valueFontColor: "#000000",
          valueFontBold: "1",
          tickValueDistance: "3",
          autoAlignTickValues: "1",
          majorTMAlpha: "20",
          chartTopMargin: "30",
          chartBottomMargin: "40"
        },
        colorrange: {
          color: [
            {
              minvalue: "0",
              maxvalue: this.highlights.uvIndex.toString(),
              code: "#7DA9E0"
            },
            {
              minvalue: this.highlights.uvIndex.toString(),
              maxvalue: "15",
              code: "#D8EDFF"
            }
          ]
        },
        annotations: {
          groups: [
            {
              items: [
                {
                  id: "val-label",
                  type: "text",
                  text: this.highlights.uvIndex.toString(),
                  fontSize: "20",
                  font: "Source Sans Pro",
                  fontBold: "1",
                  fillcolor: "#212529",
                  x: "$gaugeCenterX",
                  y: "$gaugeCenterY"
                }
              ]
            }
          ]
        },
        dials: {
          dial: [
            {
              value: this.highlights.uvIndex.toString(),
              baseWidth: "0",
              radius: "0",
              borderThickness: "0",
              baseRadius: "0"
            }
          ]
        }
      }
    };
  },
  methods: {},
  computed: {},
  watch: {
    highlights: {
      handler: function() {
        this.dataSource.colorrange.color[0].maxvalue = this.highlights.uvIndex.toString();
        this.dataSource.colorrange.color[1].minvalue = this.highlights.uvIndex.toString();
        this.dataSource.annotations.groups[0].items[0].text = this.highlights.uvIndex.toString();
      },
      deep: true
    }
  }
 };
 </script>