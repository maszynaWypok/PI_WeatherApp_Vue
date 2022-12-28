<template>
  <div id="ancestor">
    <div class="container-fluid" id="app">
      <div class="row">
        <div id="sidebar" class="col-md-3 col-sm-4 col-xs-12 sidebar">
          <div id="search">
            <input
              id="location-input"
              type="text"
              ref="input"
              placeholder="Location?"
              @keyup.enter="organizeAllDetails"
            >
            <button id="search-btn" @click="organizeAllDetails">
              <img src="./assets/Search.svg" width="24" height="24">
            </button>
          </div>
          <div id="info">
            <div class="wrapper-left">
              <div id="current-weather">
                {{ currentWeather.temp }}
                <span>°C</span>
              </div>
              <div id="weather-desc">{{ currentWeather.summary }}</div>
              <div class="temp-max-min">
                <div class="max-desc">
                  <div id="max-detail">
                    <i>▲</i>
                    {{ currentWeather.todayHighLow.todayTempHigh }}
                    <span>°C</span>
                  </div>
                  <div id="max-summary">at {{ currentWeather.todayHighLow.todayTempHighTime }}</div>
                </div>
                <div class="min-desc">
                  <div id="min-detail">
                    <i>▼</i>
                    {{ currentWeather.todayHighLow.todayTempLow }}
                    <span>°C</span>
                  </div>
                  <div id="min-summary">at {{ currentWeather.todayHighLow.todayTempLowTime }}</div>
                </div>
              </div>
            </div>
            <div class="wrapper-right">
              <div class="date-time-info">
                <div id="date-desc">
                  <img src="./assets/calendar.svg" width="20" height="20">
                  {{ currentWeather.time }}
                </div>
              </div>
              <div class="location-info">
                <div id="location-desc">
                  <img
                    src="./assets/location.svg"
                    width="10.83"
                    height="15.83"
                    style="opacity: 0.9;"
                  >
                  {{ currentWeather.full_location }}
                  <div id="location-detail" class="mt-1">
                    Lat: {{ currentWeather.formatted_lat }}
                    <br>
                    Long: {{ currentWeather.formatted_long }}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <dashboard-content
          class="col-md-9 col-sm-8 col-xs-12 content"
          id="dashboard-content"
          :highlights="highlights"
          :tempVar="tempVar"
        ></dashboard-content>
      </div>
    </div>
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
      weatherDetails: false,
      location: '',
      lat: '',
      long: '',
      completeWeatherApi: '',
      rawWeatherData: '',
      currentWeather: {
        full_location: '',
        formatted_lat: '',
        formatted_long: '',
        time: '',
        temp: '',
        todayHighLow: {
          todayTempHigh: '',
          todayTempHighTime: '',
          todayTempLow: '',
          todayTempLowTime: ''
        },
      summary: '',
      possibility: '',
      },

      filename: 'App.vue',
      tempVar: {
        tempToday: [
          {hour: '11.00 AM', temp: '35'},
          {hour: '12.00 AM', temp: '36'},
          {hour: '1.00 AM', temp: '37'},
          {hour: '2.00 AM', temp: '38'},
          {hour: '3.00 AM', temp: '36'},
          {hour: '4.00 AM', temp: '35'},
          //bedzie dynamiczne
        ],
      },
      highlights: {
        uvIndex: '',
        visibility: '',
        windStatus: {
          windSpeed: '',
          windDirectionDegrees: '',
          windDirectionDir: ''
        },
      },
    }
  },
  methods: {
    //just utility stuff
    convertToTitleCase: function(str) {
     str = str.toLowerCase().split(' ');
     for (var i = 0; i < str.length; i++) {
       str[i] = str[i].charAt(0).toUpperCase() + str[i].slice(1);
     }
     return str.join(' ');
   },
    //action-methods
    makeInputEmpty: function() {
      this.$refs.input.value = '';
    },
    makeTempVarTodayEmpty: function() {
      this.tempVar.tempToday = [];
    },
    detectEnterKeyPress: function() {
      var input = this.$refs.input;
      input.addEventListener('keyup', function(event) {
        event.preventDefault();
        var enterKeyCode = 13;
        if (event.keyCode === enterKeyCode) {
          this.setHitEnterKeyTrue();
        }
      });
    },
    locationEntered: function() {
      var input = this.$refs.input;
      if (input.value === '') {
        this.location = "Katowice";
      } else {
        this.location = this.convertToTitleCase(input.value);
      }
      this.makeInputEmpty();
      this.makeTempVarTodayEmpty();
    },

    //api call
    fixWeatherApi: function() {
      var weatherApi = 'https://api.openweathermap.org/data/2.5/weather?q=' + this.location + '&appid=' + process.env.VUE_APP_OPEN_WEATHER_API_KEY + '&units=metric';
      this.completeWeatherApi = weatherApi;
    },
    fetchWeatherData: async function() {
      await this.fixWeatherApi();
      var axios = require('axios');
      var weatherApiResponse = await axios.get(this.completeWeatherApi);
      if (weatherApiResponse.status === 200) {
        this.rawWeatherData = weatherApiResponse.data;
      } else {
        alert("Houston, we don't have a forecast for you yet");
      }
    },
    //methods for data processing
    getTimezone: function() {
     return this.rawWeatherData.timezone;
   },
   getSetCurrentTime: function() {
     var currentTime = this.rawWeatherData.currently.time;
     var timezone = this.getTimezone();
     this.currentWeather.time = this.unixToHuman( //unix to human still to write
       timezone,
       currentTime
     ).fullTime;
   },
   getSetSummary: function() {
    var currentSummary = this.convertToTitleCase(
      this.rawWeatherData.currently.summary
    );
    if (currentSummary.includes(' And')) {
      currentSummary = currentSummary.replace (' And', ',');
    }
    this.currentWeather.summary = currentSummary;
   },
   getSetPossibility: function() {
     var possible = this.formatPossibility(this.rawWeatherData.daily.icon);
     if (possible.includes(' And')) {
       possible = possible.replace(' And', ',');
     }
     this.currentWeather.possibility = possible;
   },
   getSetCurrentTemp: function() {
     currentWeather.temp = this.rawWeatherData.currently.temperature;
     
   },
   getTodayDetails: function() {
     return this.rawWeatherData.daily.data[0];
   },
   getSetTodayTempHighLowWithTime: function() {
     var timezone = this.getTimezone();
     var todayDetails = this.getTodayDetails();
     this.currentWeather.todayHighLow.todayTempHigh = todayDetails.temperatureMax;
     
     this.currentWeather.todayHighLow.todayTempHighTime = this.unixToHuman(
       timezone,
       todayDetails.temperatureMaxTime
     ).onlyTime;
     this.currentWeather.todayHighLow.todayTempLow = todayDetails.temperatureMin;
     this.currentWeather.todayHighLow.todayTempLowTime = this.unixToHuman(
       timezone,
       todayDetails.temperatureMinTime
     ).onlyTime;
   },
   getHourlyInfoToday: function() {
     return this.rawWeatherData.hourly.data;
   },
   getSetHourlyTempInfoToday: function() {
     var unixTime = this.rawWeatherData.currently.time;
     var timezone = this.getTimezone();
     var todayMonthDate = this.unixToHuman(timezone, unixTime).onlyMonthDate;
     var hourlyData = this.getHourlyInfoToday();
     for (var i = 0; i < hourlyData.length; i++) {
       var hourlyTimeAllTypes = this.unixToHuman(timezone, hourlyData[i].time);
       var hourlyOnlyTime = hourlyTimeAllTypes.onlyTime;
       var hourlyMonthDate = hourlyTimeAllTypes.onlyMonthDate;
       if (todayMonthDate === hourlyMonthDate) {
         var hourlyObject = { hour: '', temp: '' };
         hourlyObject.hour = hourlyOnlyTime;
         hourlyObject.temp = this.fahToCel(hourlyData[i].temperature).toString();
         this.tempVar.tempToday.push(hourlyObject);
         /*
         Since we are using array.push(), we are just adding elements
         at the end of the array. Thus, the array is not getting emptied
         first when a new location is entered.
         to solve this problem, a method this.makeTempVarTodayEmpty()
         has been created, and called from this.locationEntered().
         */
       }
     }
     /*
     To cover the edge case where the local time is between 10 — 12 PM,
     and therefore there are only two elements in the array
     this.tempVar.tempToday. We need to add the points for minimum temperature
     and maximum temperature so that the chart gets generated with atleast four points.
     */
     if (this.tempVar.tempToday.length <= 2) {
       var minTempObject = {
         hour: this.currentWeather.todayHighLow.todayTempHighTime,
         temp: this.currentWeather.todayHighLow.todayTempHigh
       };
       var maxTempObject = {
         hour: this.currentWeather.todayHighLow.todayTempLowTime,
         temp: this.currentWeather.todayHighLow.todayTempLow
       };
       /*
       Typically, lowest temp are at dawn,
       highest temp is around mid day.
       Thus we can safely arrange like min, max, temp after 10 PM.
       */
       // array.unshift() adds stuff at the beginning of the array.
       // the order will be: min, max, 10 PM, 11 PM.
       this.tempVar.tempToday.unshift(maxTempObject, minTempObject);
     }
   },
   getSetUVIndex: function() {
     var uvIndex = this.rawWeatherData.currently.uvIndex;
     this.highlights.uvIndex = uvIndex;
   },
   getSetVisibility: function() {
     this.highlights.visibility = this.rawWeatherData.currently.visibility;
   },
   getSetWindStatus: function() {
     this.highlights.windStatus.windSpeed = this.rawWeatherData.currently.windSpeed;
     var absoluteWindDir = this.rawWeatherData.currently.windBearing;
     this.highlights.windStatus.windDirection = absoluteWindDir;
     this.highlights.windStatus.derivedWindDirection = this.deriveWindDir(
       absoluteWindDir
     );
   },




  },
  computed: {


  }
}
</script>

<style>

</style>
