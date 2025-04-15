<template>
  <div class="window-container">
    <div class="weather-wrapper">
      <div v-if="windowHeight < 345" class="weather-info">
        <div class="text-12 text-weight-800">
          {{ getDateFromDt(weather.list[0].dt).toLocaleDateString('en-US', { weekday: 'short' }) }}
        </div>
        <div class="weather-temp text-38 text-weight-700">
          {{ Math.floor(weather.list[0].main.temp) }}
        </div>
        <div class="text-12 text-weight-700">
          {{ getDateFromDt(weather.list[0].dt).getDate() }}
          /
          {{ getDateFromDt(weather.list[0].dt).getMonth() }}
        </div>
      </div>

      <div class="weather-geo">
        <Icon url="/public/icons/geo.svg"/>
        {{ weather.city.name }}, {{ weather.city.country }}
      </div>

      <div
        class="weather-days__forecast"
        v-if="windowHeight >= 345"
        >
        <div
          class="weather-day__forecast"
          v-for="(dayForecast, index) in dailyForecast"
          :key="index"
        >
          <div class="text-16 text-weight-400 forecast-day">{{ getDateFromDt(dayForecast.dt).toLocaleDateString('en-US', { weekday: 'long' }) }}</div>
          <div class="text-32 text-weight-700 weather-temp">{{ Math.floor(dayForecast.main.temp) }}</div>
          <Icon
            class="forecast-picture"
            url="/public/icons/sun-clouds.svg"
            width="50px"
            height="50px"
          />
        </div>
      </div>
    </div>

    <Icon v-if="windowHeight < 345 && windowWidth < 250" class="weather-icon" width="100px" height="100px" url="/public/icons/sun-clouds.svg"/>
    <Icon v-if="windowHeight < 345 && windowWidth > 250" class="weather-icon" width="215px" height="100%" url="/public/icons/sun-clouds.svg"/>
  </div>
</template>

<script>
import { defineComponent } from "vue";
import Icon from '../components/Icon.vue';

export default defineComponent({
  name: "App",
  components: {
    Icon
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
            weather: [
              {
                id: 0,
                main: ''
              }
            ]
          }
        ],
        city: {
          name: '',
          country: ''
        },
      },
      windowHeight: 175,
      windowWidth: 175,
      dailyForecast: []
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
    },
    handleWindowResize() {
      this.windowHeight = window.innerHeight;
      this.windowWidth = window.innerWidth;
    },
    getDailyForecast(hourlyForecast) {
      let list = [];
      let currentDate = this.getDateFromDt(hourlyForecast[0].dt);
      list.push(hourlyForecast[0]);

      hourlyForecast.forEach((item) => {
        const itemDate = this.getDateFromDt(item.dt);

        if (currentDate.getDate() < itemDate.getDate() && itemDate.getHours() == 12) {
          list.push(item);
        }
      })

      return list;
    }
  },
  async mounted() {
    const location = await this.fetchLocation();
    this.weather = await this.fetchWeather(location);
    this.dailyForecast = this.getDailyForecast(this.weather.list);
    console.log(this.dailyForecast);

    window.addEventListener('resize', this.handleWindowResize);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.handleWindowResize);
  }
});
</script>

<style>
.window-container {
  padding: 1rem;

  display: flex;
  justify-content: space-between;
  align-items: center;

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
    right: -1rem;

    background: url(/public/icons/degrees.svg) no-repeat center;
    background-size: contain;
  }
}
.weather-icon {
  position: absolute;
  right: 0;
  bottom: 1rem;
}
@media (min-width: 250px) {
  .weather-icon {
    bottom: 0;
  }
}

.weather-geo {
  display: flex;
  align-items: center;
  gap: 0.25rem;

  font-size: 10px;
  font-weight: 600;
}
@media (min-height: 345px) {
  .weather-geo {
    font-size: 16px;
    font-weight: 700;
  }
}

.weather-days__forecast, .weather-day__forecast {
  width: 100%;
  height: 100%;

  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}
.weather-day__forecast {
  flex-direction: row;
}
.forecast-day {
  max-width: 100px;
  width: 100%;
}
.forecast-picture {
  width: 50px;
}
</style>
