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
              <div id="current-icon">
                <img :src="currentWeather.fullPossibility">
              </div>
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
      completeGeoApi: '',
      completeWeatherApi: '',
      rawGeoData: '',
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
      fullPossibility: '',
      },

      filename: 'App.vue',
      tempVar: {
        tempToday: [
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
    convertToTitleCase: function(str) {
     str = str.toLowerCase().split(' ');
     for (var i = 0; i < str.length; i++) {
       str[i] = str[i].charAt(0).toUpperCase() + str[i].slice(1);
     }
     return str.join(' ');
   },
    deriveWindDir: function(windDir) {
     var wind_directions_array = [
       { minVal: 0, maxVal: 30, direction: 'N' },
       { minVal: 31, maxVal: 45, direction: 'NNE' },
       { minVal: 46, maxVal: 75, direction: 'NE' },
       { minVal: 76, maxVal: 90, direction: 'ENE' },
       { minVal: 91, maxVal: 120, direction: 'E' },
       { minVal: 121, maxVal: 135, direction: 'ESE' },
       { minVal: 136, maxVal: 165, direction: 'SE' },
       { minVal: 166, maxVal: 180, direction: 'SSE' },
       { minVal: 181, maxVal: 210, direction: 'S' },
       { minVal: 211, maxVal: 225, direction: 'SSW' },
       { minVal: 226, maxVal: 255, direction: 'SW' },
       { minVal: 256, maxVal: 270, direction: 'WSW' },
       { minVal: 271, maxVal: 300, direction: 'W' },
       { minVal: 301, maxVal: 315, direction: 'WNW' },
       { minVal: 316, maxVal: 345, direction: 'NW' },
       { minVal: 346, maxVal: 360, direction: 'NNW' }
     ];
     var wind_direction = '';
     for (var i = 0; i < wind_directions_array.length; i++) {
       if (
         windDir >= wind_directions_array[i].minVal &&
         windDir <= wind_directions_array[i].maxVal
       ) {
         wind_direction = wind_directions_array[i].direction;
       }
     }
     return wind_direction;
   },
    unixToHuman: function(timezone, timestamp) {
     var moment = require('moment-timezone'); // for handling date & time
     var decipher = new Date((timestamp) * 1000);
     var human = moment(decipher)
       .tz(timezone)
       //.utcOffset(timezone)
       .format('llll');
     var timeArray = human.split(' ');
     var timeNumeral = timeArray[4];
     var timeSuffix = timeArray[5];
     var justTime = timeNumeral + ' ' + timeSuffix;
     var monthDateArray = human.split(',');
     var monthDate = monthDateArray[1].trim();
    
     return {
       fullTime: human,
       onlyTime: justTime,
       onlyMonthDate: monthDate
     };
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
      this.makeTempVarTodayEmpty();
    },
    fixGeoApi: function() {
      this.locationEntered();
      var geoApi = 'http://api.openweathermap.org/geo/1.0/direct?q='+ this.location +'&limit=1&appid=2ba26978d7e6ca228d0f6ce8e3d0b68b';
      this.completeGeoApi = geoApi;
    },
    fetchCoordinates:async function() {
      await this.fixGeoApi();
      var axios = require('axios');
      var geoApiResponse = await axios.default.get(this.completeGeoApi);
      if (geoApiResponse.status === 200) {
        this.rawGeoData = geoApiResponse.data[0];
        this.lat = this.rawGeoData.lat;
        this.long = this.rawGeoData.lon;
        this.currentWeather.full_location = this.rawGeoData.name +', ' + this.rawGeoData.state + ', ' + this.rawGeoData.country;
        this.beautyfiCoords();
      } else {
        alert("Problem with geolocation occured");
      }
    },
    beautyfiCoords: function() {
      var latit = this.lat;
      var longtit = this.long;
      if (latit > 0) {
       this.currentWeather.formatted_lat =
         (Math.round(latit * 10000) / 10000).toString() + '°N';
     } else if (latit < 0) {
       this.currentWeather.formatted_lat =
         (-1 * (Math.round(latit * 10000) / 10000)).toString() +
         '°S';
     } else {
       this.currentWeather.formatted_lat = (
         Math.round(latit * 10000) / 10000
       ).toString();
     }
     if (longtit > 0) {
       this.currentWeather.formatted_long =
         (Math.round(longtit * 10000) / 10000).toString() + '°E';
     } else if (longtit < 0) {
       this.currentWeather.formatted_long =
         (-1 * (Math.round(longtit * 10000) / 10000)).toString() +
         '°W';
     } else {
       this.currentWeather.formatted_long = (
         Math.round(longtit * 10000) / 10000
       ).toString();
     }
    },
    //api call
    fixWeatherApi: async function() {
      await this.fetchCoordinates();
      var weatherApi = 'https://api.openweathermap.org/data/3.0/onecall?lat=' + this.lat + '&lon=' + this.long +'&appid=2ba26978d7e6ca228d0f6ce8e3d0b68b'+ '&units=metric';
      this.completeWeatherApi = weatherApi;
    },
    fetchWeatherData: async function() {
      await this.fixWeatherApi();
      var axios = require('axios');
      var weatherApiResponse = await axios.default.get(this.completeWeatherApi);
      if (weatherApiResponse.status === 200) {
        this.rawWeatherData = weatherApiResponse.data;
      } else {
        alert("Houston, we don't have a forecast for you yet");
      }
      this.makeInputEmpty();
    },
    //methods for data processing
   getTimezone: function() {
     return this.rawWeatherData.timezone;
   },
   getSetCurrentTime: function() {
     var currentTime = this.rawWeatherData.current.dt;
     var timezone = this.getTimezone();
     this.currentWeather.time = this.unixToHuman( 
       timezone,
       currentTime
     ).fullTime;
   }, 
   getSetSummary: function() {
    var currentSummary = this.convertToTitleCase(
      this.rawWeatherData.current.weather[0].description
    );
    if (currentSummary.includes(' And')) {
      currentSummary = currentSummary.replace (' And', ',');
    }
    this.currentWeather.summary = currentSummary;
   },
   getSetPossibility: function() {
     this.currentWeather.possibility = this.rawWeatherData.current.weather[0].icon;
     var poss = 'http://openweathermap.org/img/wn/'+ this.rawWeatherData.current.weather[0].icon +'@4x.png';
    this.currentWeather.fullPossibility = poss;
   },
   getSetCurrentTemp: function() {
     this.currentWeather.temp = this.rawWeatherData.current.temp;
     
   },
   getTodayDetails: function() {
     return this.rawWeatherData.daily[0];
   },
   getSetTodayTempHighLowWithTime: function() { //still time to add?
     //var timezone = this.getTimezone();
     var todayDetails = this.getTodayDetails();
     this.currentWeather.todayHighLow.todayTempHigh = todayDetails.temp.max;
     
    /* this.currentWeather.todayHighLow.todayTempHighTime = this.unixToHuman(
       timezone,
       todayDetails.temperatureMaxTime
     ).onlyTime; */
     this.currentWeather.todayHighLow.todayTempLow = todayDetails.temp.min;
    /* this.currentWeather.todayHighLow.todayTempLowTime = this.unixToHuman(
       timezone,
       todayDetails.temperatureMinTime
     ).onlyTime; */
   },
   getHourlyInfoToday: function() {
    return this.rawWeatherData.hourly[0];
   },
   getSetHourlyTempInfoToday: function() {
     var unixTime = this.rawWeatherData.current.dt;
     var timezone = this.getTimezone();
     var todayMonthDate = this.unixToHuman(timezone, unixTime).onlyMonthDate;
     //var hourlyData = this.getHourlyInfoToday();
     for (var i = 0; i < 24; i++) {
      var hourlyTimeAllTypes = this.unixToHuman(timezone, this.rawWeatherData.hourly[i].dt);
      var hourlyOnlyTime = hourlyTimeAllTypes.onlyTime;
      var hourlyMonthDate = hourlyTimeAllTypes.onlyMonthDate;
      //below shows just the same day
      if (todayMonthDate === hourlyMonthDate) {
         var hourlyObject = { hour: '', temp: '' };
         hourlyObject.hour = hourlyOnlyTime;
         hourlyObject.temp = this.rawWeatherData.hourly[i].temp.toString();
         this.tempVar.tempToday.push(hourlyObject);
     }
     //below shows all 24 hours
         /* var hourlyObject = { hour: '', temp: '' };
         hourlyObject.hour = hourlyOnlyTime;
         hourlyObject.temp = this.rawWeatherData.hourly[i].temp.toString();
         this.tempVar.tempToday.push(hourlyObject); */
         
         /*
         Since we are using array.push(), we are just adding elements
         at the end of the array. Thus, the array is not getting emptied
         first when a new location is entered.
         to solve this problem, a method this.makeTempVarTodayEmpty()
         has been created, and called from this.locationEntered().
         */
       }
     },
/*      /*
     To cover the edge case where the local time is between 10 — 12 PM,
     and therefore there are only two elements in the array
     this.tempVar.tempToday. We need to add the points for minimum temperature
     and maximum temperature so that the chart gets generated with atleast four points.
     */
     /* if (this.tempVar.tempToday.length <= 2) {
       var minTempObject = {
         hour: this.currentWeather.todayHighLow.todayTempHighTime,
         temp: this.currentWeather.todayHighLow.todayTempHigh
       };
       var maxTempObject = {
         hour: this.currentWeather.todayHighLow.todayTempLowTime,
         temp: this.currentWeather.todayHighLow.todayTempLow
       }; */
       /*
       Typically, lowest temp are at dawn,
       highest temp is around mid day.
       Thus we can safely arrange like min, max, temp after 10 PM.
       */
       // array.unshift() adds stuff at the beginning of the array.
       // the order will be: min, max, 10 PM, 11 PM.
/*        this.tempVar.tempToday.unshift(maxTempObject, minTempObject); */ 
   getSetUVIndex: function() {
     var uvIndex = this.rawWeatherData.current.uvi;
     this.highlights.uvIndex = uvIndex;
   },
   getSetVisibility: function() {
     this.highlights.visibility = this.rawWeatherData.current.visibility;
   },
   getSetWindStatus: function() {
     this.highlights.windStatus.windSpeed = this.rawWeatherData.current.wind_speed;
     var absoluteWindDir = this.rawWeatherData.current.wind_deg;
     this.highlights.windStatus.windDirectionDegrees = absoluteWindDir;
     this.highlights.windStatus.windDirectionDir = this.deriveWindDir(
       absoluteWindDir
     );
   },
   //info section
   organizeCurrentWeatherInfo: function() {
    this.getSetCurrentTime();
    this.getSetCurrentTemp();
    this.getSetTodayTempHighLowWithTime();
    this.getSetSummary();
    this.getSetPossibility();
   },
   organizeTodayHighlights: function() {
     this.getSetUVIndex();
     this.getSetVisibility();
     this.getSetWindStatus();
   },
   organizeAllDetails: async function() {
    await this.fetchCoordinates();
    await this.fetchWeatherData();
    this.organizeCurrentWeatherInfo();
    this.organizeTodayHighlights();
    this.getSetHourlyTempInfoToday();    
   },
  },

  mounted: 
  async function() {
    //this.location = "Katowice";
    await this.organizeAllDetails();
  },
  computed: {


  }
};
</script>

<style>

</style>
