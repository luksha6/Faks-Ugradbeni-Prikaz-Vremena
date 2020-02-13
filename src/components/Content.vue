<template>
    <div class="page-content page-container" id="page-content">
        <div class="padding">
            <div class="row container d-flex justify-content-center">
                <div class="col-lg-8 grid-margin stretch-card">
                    <form @submit.prevent="getWeather($data)" name="weatherForm">
                        <h5>Unesite grad u formu ispod.</h5>
                        <div class="form-group row justify-content-center">
                            <div class="col-lg-6">
                                <input type="text" class="form-control" v-model="city" id="cityInput"
                                       placeholder="Grad">
                                <input type="submit" class="form-control btn btn-info mt-2" value="Posalji">
                            </div>
                        </div>
                    </form>
                    <div class="alert alert-danger" role="alert" v-if="isError()">
                        {{errorMessage}}
                    </div>
                    <div class="card card-weather" v-if="weatherData.timestamp && weatherData.temperature && weatherData.humidity &&
                    weatherData.pressure && weatherData.windSpeed && weatherData.windDirection && weatherData.city">
                        <div class="card-body">
                            <div class="weather-date-location">
                                <h3 class="text-black">{{ formatDate(weatherData.timestamp).day }}</h3>
                                <p class="text-black"><span class="weather-date"><b>Datum - </b>{{ formatDate(weatherData.timestamp).date }},</span>
                                    <span class="weather-data"><br><b> Vrijeme - </b>{{ formatDate(weatherData.timestamp).time }}, </span><span
                                            class="weather-location">{{ weatherData.city }}</span></p>
                            </div>
                            <div class="weather-data d-flex">
                                <div class="mr-auto">
                                    <h4 class="display-3 text-black">{{ weatherData.temperature }}<span class="symbol">°</span>C
                                    </h4>
                                    <p><b>Temperatura</b></p>
                                </div>
                            </div>
                        </div>
                        <div class="card-body p-0">
                            <div class="d-flex weakly-weather">
                                <div class="weakly-weather-item">
                                    <p class="mb-0"> Vlažnost </p> <i class="mdi mdi-weather-cloudy"></i>
                                    <p class="mb-0"> {{ weatherData.humidity }} </p>
                                </div>
                                <div class="weakly-weather-item">
                                    <p class="mb-1"> Tlak </p> <i class="mdi mdi-weather-hail"></i>
                                    <p class="mb-0"> {{ weatherData.pressure }} </p>
                                </div>
                                <div class="weakly-weather-item">
                                    <p class="mb-1"> Smjer Vjetra </p> <i class="mdi mdi-weather-partlycloudy"></i>
                                    <p class="mb-0"> {{ weatherData.windDirection }} </p>
                                </div>
                                <div class="weakly-weather-item">
                                    <p class="mb-1"> Brzina Vjetra </p> <i class="mdi mdi-weather-pouring"></i>
                                    <p class="mb-0"> {{ weatherData.windSpeed }} </p>
                                </div>
                            </div>
                        </div>
                    </div>
                    <!--weather card ends-->
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        name: "Content",
        data() {
            return {
                city: '',
                error: false,
                errorMessage: '',
                weatherData: {}
            }
        },
        methods: {
            formatDate: function (timestamp) {
                let a = new Date(timestamp);
                let months = ['Siječanj', 'Veljača', 'Ožujak', 'Travanj', 'Svibanj', 'Lipanj', 'Srpanj', 'Kolovoz', 'Rujan', 'Listopad', 'Studeni', 'Prosinac'];
                let year = a.getFullYear();
                let month = months[a.getMonth()];
                let date = a.getDate();
                let hour = a.getHours();
                let min = a.getMinutes();
                let sec = a.getSeconds();
                let day = a.getDay();

                let data = {};
                data.date = date + ' ' + month + ' ' + year;
                data.time = hour + ':' + min + ':' + sec;
                data.day = this.getWeekDay(day);
                return data;
            },
            getWeekDay: function (dayNum) {
                let weekday = new Array(7);
                weekday[0] = "Nedjelja";
                weekday[1] = "Ponedjeljak";
                weekday[2] = "Utorak";
                weekday[3] = "Srijeda";
                weekday[4] = "Četvrtak";
                weekday[5] = "Petak";
                weekday[6] = "Subota";
                return weekday[dayNum];
            },
            getWeather: function (data) {
                let self = this;
                axios.get('https://enigmatic-forest-52909.herokuapp.com/meteostationdatabyname', {
                    params: {
                        name: data.city
                    },
                })
                    .then(function (response) {
                        console.log(response);
                        self.weatherData = response.data[0];
                        self.weatherData.city = response.config.params.name;
                        self.error = false;
                        console.log(self.weatherData);

                    })
                    .catch(function (error) {
                        self.error = true;
                        self.weatherData = {};
                        self.errorMessage = error;
                        console.log(error);
                    })

            },
            isError: function () {
                return this.error;
            }
        }
    }
</script>

<style scoped>

</style>
