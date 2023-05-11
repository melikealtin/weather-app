<template>
  <div>
    <div
      :class="
        weather.main != 'undefined' && weather.main?.temp > 16 ? 'warm' : 'cold'
      "
    >
      <main>
        <div class="search-box">
          <input
            type="text"
            class="search-bar"
            placeholder="Search..."
            v-model="query"
            @keypress="fetchWeather"
          />
        </div>
        <div class="weather-wrap" v-if="weather.main != undefined">
          <div class="location-box">
            <div class="location">
              {{ weather.name }}, {{ weather.sys?.country }}
            </div>
            <div class="date">{{ dateBuilder() }}</div>
          </div>
          <div class="weather-box">
            <div class="temp">{{ Math.round(weather.main?.temp) }} °C</div>
            <div class="weather">{{ weather.weather?.[0].main }}</div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      api_key: import.meta.env.VITE_WEATHER_API_KEY,
      url_base: import.meta.env.VITE_WEATHER_URL_BASE,
      query: "",
      weather: {},
    };
  },
  methods: {
    async fetchWeather(e) {
      if (e.key == "Enter") {
        try {
          const { data } = await axios.get(`${this.url_base}/weather`, {
            params: {
              q: this.query,
              units: "metric",
              APPID: this.api_key,
            },
          });

          this.weather = data;
        } catch ({ response }) {
          if (response.status == 401)
            alert("Kullanıcı yetkisi bulunmamaktadır!");
          if (response.status == 404) alert("Sonuç Bulunamadı!");
        }
      }
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "montserrat", sans-serif;
  main {
    min-height: 100vh;
    padding: 25px;
    background-image: linear-gradient(
      to bottom,
      rgba(0, 0, 0, 0.25),
      rgba(0, 0, 0, 0.75)
    );
    .search-box {
      width: 100%;
      margin-bottom: 30px;

      .search-bar {
        display: block;
        width: 100%;
        padding: 15px;
        color: #313131;
        font-size: 20px;
        appearance: none;
        border: none;
        outline: none;
        background: none;
        box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
        background-color: rgba(255, 255, 255, 0.5);
        border-radius: 0px 16px 0px 16px;
        transition: 0.4s;
        &:focus {
          box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
          background-color: rgba(255, 255, 255, 0.75);
          border-radius: 16px 0px 16px 0px;
        }
      }
    }
    .location-box {
      .location {
        color: #fff;
        font-size: 32px;
        font-weight: 500;
        text-align: center;
        text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
      }
      .date {
        color: #fff;
        font-size: 20px;
        font-weight: 300;
        font-style: italic;
        text-align: center;
      }
    }
    .weather-box {
      text-align: center;
      .temp {
        display: inline-block;
        padding: 10px 25px;
        color: #fff;
        font-size: 102px;
        font-weight: 900;
        text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
        background-color: rgba(255, 255, 255, 0.25);
        margin: 30px 0px;
        border-radius: 16px;
        box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
      }
      .weather {
        color: #fff;
        font-size: 48px;
        font-weight: 700;
        font-style: italic;
        text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
      }
    }
  }
}

#app {
  .cold {
    background-image: url("./assets/coldweather.png");
    background-size: cover;
    background-position: bottom;
    transition: 1s;
  }
  .warm {
    background-image: url("./assets/warmweather.png");
    background-size: cover;
    background-position: bottom;
    transition: 1s;
  }
}
</style>
