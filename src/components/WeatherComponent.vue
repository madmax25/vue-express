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
                            appid: 'c75f89dee2df592ecb5f3386294f4add',
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