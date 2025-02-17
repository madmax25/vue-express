<template>
    <div class="col-xs-6">
        <div v-if="isLoading">
            Loading weather...
        </div>
        <div v-if="isError">
            Error loading weather component.
        </div>
        <div v-if="!isLoading && !isError">
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

             axios.get('https://news-weather-api.vercel.app/api/weather', {
                        params: {
                            latitude: this.latitude,
                            longitude: this.longitude
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
                         error(e);
                    });
        };

        const error = (err) => {
            this.isLoading = false;
            this.isError = true;
            console.log(err);
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