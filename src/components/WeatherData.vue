<template>
    <div id="weatherDiv">

      <h1 id="WeatherCompnentTitle">Get Weather!</h1>
      <p>Powered by DarkSky.net (and made by Sean Cohen)</p>

     <div id="optionsPicker">
        <button v-on:click="getForYourLocation" class="btn btn-primary">Get For Your Location</button> <br>
        
        <div id="customLocationDiv">
         <input id="address" v-model="customLocation" type="textbox">
         <button  v-on:click="getForCustomLocation" class="btn btn-primary" >Get For Custom Location</button>
        </div>


        <label>custom date (optional)</label><input type="date" id="datePicker" v-model="customDate"> <br>
      </div>
      
      <p class="tall"><i>{{locationShowingFor}}</i></p> 
        <div class="weatherInfo" v-if="weather != undefined && weather.currently != undefined"> 
          <div id="currently">
            <p class="timeHeading tall">{{new Date(weather.currently.time * 1000).toDateString()}}</p>  
            <p class="summary">{{weather.currently.summary}}</p>
            <p class="temperature">{{weather.currently.temperature}} degrees</p>
            <img v-bind:src="getImageForIconString(weather.currently.icon)" alt="Mountain View" style="width:304px;height:228px;">
          </div>
        </div>
        <div class="weatherInfo" v-if="weather != undefined && weather.currently != undefined"> 
          <div id="daily" v-if="weather.daily != undefined && weather.daily.data.length > 1">
            <p class="timeHeading tall">Daily</p>  
            <div v-for = "dayData in weather.daily.data">
                <p class="date">{{new Date(dayData.time * 1000).toDateString()}}</p>
                <p class="summary">{{dayData.summary}}</p>
                <p class="temperature">High of {{dayData.temperatureHigh}} and low of {{dayData.temperatureLow}}</p>
                <img class="weatherIcon" v-bind:src="getImageForIconString(dayData.icon)" alt="Mountain View" style="width:200px;height:130px;">
            </div>
          </div>
        </div>
    <h4>Historic Data For Your Location</h4>
    <historic-chart
      v-bind:tempHighs="temperatureHighs"
      v-bind:key="999999000">
    </historic-chart>
    </div>
</template>

<script>

import HistoricChart from './HistoricChart';

export default {
  name: 'WeatherData',
  components: {
    HistoricChart
  },
  data: function(){
    return {
            cloudyIcon: '../../static/cloudy.png',
            currentLocation: {},
            customLocation: '',
            customLocationCoordinates: {},
            weather:{},
            customDate: '',
            locationShowingFor : '',
            previousSearches: '',
            temperatureHighs: [80,70,100,100]
        };
  },
        

    methods: {
        getForYourLocation: function(){
          this.locationShowingFor = 'Your Location';
          this.getWeather(this.currentLocation.lat, this.currentLocation.lng); 
        },
        getForCustomLocation: function(){
            var app = this;
            if(this.customLocation.length < 5){
              alert('please enter valid location');
              return;
            }
            this.locationShowingFor = this.customLocation;
            this.geocodeAddress(this.customLocation).then(function(results){
                app.customLocationCoordinates = results;
                app.getWeather(results.lat, results.lng);
            });
            
        },
        getWeather: function(lat, lng){
            var pSearches = localStorage.previousSearches;
            pSearches = this.locationShowingFor + " - " + this.customDate + "," + (pSearches? pSearches:'');
            localStorage.previousSearches = pSearches;
            var app = this;
            var isForCurrent = new Date(app.customDate).getUTCDate() == new Date().getUTCDate();
            var weatherUrl = "https://api.darksky.net/forecast/API_KEY/" + lat + "," + lng;
            if(!isForCurrent){
              var time = (new Date(app.customDate).getTime())/ 1000;
              weatherUrl += "," + time
            }
            console.log(weatherUrl);
            $.ajax({
                url: weatherUrl,
                dataType: "jsonp"
                }).done(app.setWeatherVar);

        },
        // getHistoricalWeather: function(lat, lng){

        // },
        geocodeAddress: function(addressString){
            var geoPromise = new Promise(function(resolve, reject){
                var geocoder = new google.maps.Geocoder();
                geocoder.geocode({'address': addressString}, function(results, status) {
                    console.log(results);
                    var locationObj = {
                        lat: results[0].geometry.location.lat(),
                        lng: results[0].geometry.location.lng()
                    }
                    resolve(locationObj);
                });
            });
            return geoPromise;
        },
        setWeatherVar: function(json) {
            if (!json.Error) {
                console.log(json);
                this.weather = json;
            }
            else {
                console.log('error');
            }
        },
        getImageForIconString: function(iconString){
          switch(iconString){
            case 'clear-day':
              return '../../static/sunny.jpg';
              break;
            case 'clear-night':
              return '../../static/clearnight.jpg';
              break;  
            case 'rain':
              return '../../static/rain.png';
              break; 
            case 'snow':
              return '../../static/snow.png';
              break; 
            case 'sleet':
              return '../../static/sleet.png';
              break;   
            case 'wind':
              return '../../static/wind.png';
              break; 
            case 'fog':
              return '../../static/fog.png';
              break; 
            case 'cloudy':
              return '../../static/cloudy.png';
              break; 
            case 'partly-cloudy-day':
              return '../../static/partlycloudy.png';
              break; 
            case 'partly-cloudy-night':
              return '../../static/cloudynight.png';
              break; 
            default:
              return '../../static/partlycloudy.png';                                                                                                                
          }
        }
    },
    created: function(){
            var app = this;
            var dateString = new Date().toString();
            app.customDate = dateString;
            navigator.geolocation.getCurrentPosition(function(position) {
                app.currentLocation = {
                    lat: position.coords.latitude,
                    lng: position.coords.longitude
                };     
            });
    }
};

//v-bind:src="getImageForIconString(weather.currently.icon)"
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

*{
  box-sizing: border-box;
}
#WeatherCompnentTitle{
  color: #005bb2;
}
#weatherDiv{
   font-family: 'Oswald', sans-serif;
   padding: 20px;
   box-sizing: border-box;
}
#customLocationDiv{
  padding: 5px;
  margin-top: 15px;
  margin-bottom: 15px;
}
#currently, #daily{ 
  background-color: white;
  color: #c41c00;
  padding: 10px;
  width: 450px;
  max-width: 100%;
  margin-left: auto;
  margin-right: auto;
}
#optionsPicker{
  width: 300px;
  padding: 15px;
  margin: auto;
  background-color: #6ab7ff;
}
.timeHeading{
  margin-top:10px;
  border-top: 1px solid grey;
  color: #005bb2; 
}
.tall, h1{
  font-size: 3em;
}
label{
  margin: 10px;
}
.summary{
  font-size: 2em;
}
.date{
  font-size: 2em;
  background-color: #005bb2;
  color: white;
}
.temperature{
  font-size: 1.5em;
  color: #005bb2;
}
.weatherIcon{
  margin-bottom: 25px;
}
</style>
