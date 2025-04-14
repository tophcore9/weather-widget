<template>
  <div class="window-container">
    <div class="weather-wrapper">
      <div class="weather-info">
        <div class="text-12 text-weight-800">
          {{ getDateFromDt(weather.list[0].dt).getUTCMonth() }}
        </div>
        <div class="weather-temp text-38 text-weight-700">
          {{ weather.list[0].main.temp }}
        </div>
        <div class="text-12 text-weight-700">
          {{ getDateFromDt(weather.list[0].dt).getFullYear() }}
        </div>
      </div>

      <div class="weather-geo text-10 text-weight-600">
        <!-- <Icon/> -->
        {{ weather.city.name }}, {{ weather.city.country }}
      </div>
    </div>

    <!-- <Icon /> -->
  </div>
</template>

<script>
import { defineComponent } from "vue";
// import Icon from '../components/Icon.vue';

export default defineComponent({
  name: "App",
  components: {
    // Icon
  },
  data() {
    return {
      weather: {
        list: [
          {
            dt: 0,
            main: {
              temp: 0
            },
            weather: {
              id: 0,
              main: ''
            }
          }
        ],
        city: {
          name: '',
          country: ''
        },
      }
    }
  },
  methods: {
    async fetchLocation() {
      try {
        const response = await fetch('https://ipapi.co/json/');
        const data = await response.json();

        return {
          lat: data.latitude,
          lon: data.longitude,
          city: data.city,
          country: data.country_name
        }
      } catch (error) {
        console.error('IP location error: ', error);
      }
    },
    async fetchWeather(location) {
      const response = await fetch(`https://api.openweathermap.org/data/2.5/forecast?lat=${location.lat}&lon=${location.lon}&units=metric&appid=${process.env.WEATHER_API_KEY}`);
      const data = await response.json();

      return data;
    },
    getDateFromDt(dt) {
      return new Date(dt * 1000);
    }
  },
  async mounted() {
    const location = await this.fetchLocation();
    this.weather = await this.fetchWeather(location);
    console.log(this.weather);
  }
});
</script>

<style>
.window-container {
  padding: 1rem;

  display: flex;
  justify-content: space-between;

  width: 100%;
  height: 100%;

  position: relative;
  overflow: hidden;

  -webkit-app-region: drag;

  background: linear-gradient(135deg, #0064FB 0%, #0074E0 100%);
  background-size: cover;

  &::after {
    content: '';

    width: 100%;
    height: 100%;

    position: absolute;
    top: 0;
    left: 0;

    background-image: url(/public/icons/texture.svg);
    opacity: 0.3;
  }
}
.weather-wrapper, .weather-info {
  height: 100%;
  width: 100%;

  display: flex;
  flex-direction: column;
}
.weather-wrapper {
  justify-content: space-between;
  gap: 1.5rem;
}
.weather-temp {
  position: relative;

  width: fit-content;

  &::after {
    content: '';

    width: 1rem;
    height: 1rem;

    position: absolute;
    top: 0;
    right: -10px;

    background: url(/public/icons/degrees.svg) no-repeat center;
    background-size: contain;
  }
}
</style>
