<template>
    <div class="col-xs-6">
        <div v-if="isLoading">
            Loading weather...
        </div>
        <div v-if="!isLoading">
            <h1>
                Weather
            </h1>
            <table class="table table-striped table-bordered">
                <tbody>
                    <tr>
                        <th scope="row">City</th>
                        <td>{{cityName}}</td>
                    </tr>
                    <tr>
                        <th scope="row">Temperature</th>
                        <td>{{temperature}}</td>
                    </tr>
                    <tr>
                        <th scope="row">Feels like</th>
                        <td>{{feelsLike}}</td>
                    </tr>
                    <tr>
                        <th scope="row">Sunrise</th>
                        <td>{{sunrise}}</td>
                    </tr>
                    <tr>
                        <th scope="row">Sunset</th>
                        <td>{{sunset}}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import CryptoJS from 'crypto-js';

export default {
  name: 'WeatherComponent',
  data() {
    return {
      isLoading: true,
      cityName: '',
      temperature: '',
      feelsLike: '',
      sunrise: '',
      sunset: '',
      latitude: 0,
      longitude: 0
    };
  },
  methods: {
    async getData() {
       const success = (position) => {
            this.latitude = position.coords.latitude;
            this.longitude = position.coords.longitude;

             axios.get('https://api.openweathermap.org/data/2.5/weather', {
                        params: {
                            lat: this.latitude,
                            lon: this.longitude,
                            appid: CryptoJS.AES.decrypt('U2FsdGVkX1/K1Ny0L0WmLj+XvKAc1yDksAKpL4xhPI96iMfWAW/grY9UlGujQDdeP8v/l6vPlynmygkDUYkQug==', 'max').toString(CryptoJS.enc.Utf8),
                            units: 'imperial'
                        }
                    })
                    .then(response => {
                       const data = response.data;
                       this.cityName = data.name;

                        // Format temp and feels like to a single decimal point as this is most human readable.
                        // Designate temperature unit is Fahrenheit
                        this.temperature = data.main.temp.toFixed(1) + '° F';
                        this.feelsLike = data.main.feels_like.toFixed(1) + '° F';

                        this.sunrise = new Date(data.sys.sunrise * 1000).toLocaleTimeString('en-US', {
                                  hour: 'numeric',
                                  minute: '2-digit',
                                  hour12: true
                                });

                        this.sunset = new Date(data.sys.sunset * 1000).toLocaleTimeString('en-US', {
                                  hour: 'numeric',
                                  minute: '2-digit',
                                  hour12: true
                                });

                        this.isLoading = false;
                    })
                    .catch(e => {
                         console.log(e);
                    });
        };

        const error = (err) => {
            console.log(err)
        };

        // This will open permission popup
        navigator.geolocation.getCurrentPosition(success, error);
    },
  },
  beforeMount() {
    this.getData();
  }
}
</script>