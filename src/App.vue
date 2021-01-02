// It's a little bit dirty code, but i'll make it cleaner later.

<template>
  <div class="container" id="app">
    <main
      :class="
        typeof datas.main != 'undefined' && datas.main.temp > 16 ? 'warm' : ''
      "
    >
      <div class="title">
        <span :class="
        typeof datas.main != 'undefined' && datas.main.temp > 16 ? 'warm' : ''
      ">Worldwide <br> Weather <br> App</span>
        <img
          src="https://media2.giphy.com/media/Ur1ePKk5h82J2nKUmm/giphy.gif"
          alt=""
        />
      </div>

      <div class="search-box">
        <input
          class="search-bar"
          type="text"
          placeholder="Search a city..."
          v-model="selectedCity"
          @keyup.enter="getData()"
          required
        />
        <small class="search-desc"
          >Click the temperature value to see detail on
          'openweathermap.org'</small
        >
      </div>
      <div class="results" v-if="typeof datas.main != 'undefined'">
        <div class="location">{{ datas.name }}, {{ datas.sys.country }}</div>
        <div class="date">{{ dateBuilder() }}</div>
        <div class="temp">
          <a :href="`https://openweathermap.org/city/${id}`" target="_blank"
            >{{ Math.floor(datas.main.temp) }} °C
          </a>
        </div>
        <div class="weather-wrapper">
          <div class="weather">
            <p>{{ datas.weather[0].main }}</p>
            <img
              :src="
                `https://openweathermap.org/img/wn/${datas.weather[0].icon}@2x.png`
              "
              alt=""
            />
          </div>
          <span class="desc">
            -- {{ capitalizeFirstLetter(datas.weather[0].description) }} --
          </span>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import swal from "sweetalert";
export default {
  data() {
    return {
      apiKey: process.env.VUE_APP_WEATHER_API_KEY,
      selectedCity: "",
      datas: {},
      id: "",
      titleEmoji: "",
    };
  },
  methods: {
    errorAlert() {
      swal(
        "Stop right there!",
        "You searched a city that belongs to your dream world or not even existed.",
        "error",
        {
          button: "Sorry for that..",
        }
      );
    },
    handleErrors(response) {
      if (!response.ok) {
        throw Error(response.statusText);
      }
      return response;
    },
    getData() {
      fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${this.selectedCity}&appid=${this.apiKey}&units=metric`
      )
        .then(this.handleErrors)
        .then((res) => res.json())
        .catch((error) => this.errorAlert())
        .then((data) => this.setResults(data));

      this.selectedCity = "";
    },
    setResults(results) {
      this.datas = results;
      this.id = this.datas.id;
    },
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
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
      return `${day}, ${date} ${month} ${year}`;
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,400;0,700;1,400;1,700&display=swap");
* {
  font-family: "Josefin Sans", sans-serif;
}
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.swal-overlay {
  background-color: rgba(red, 0.3);
}
.swal-button{
  background-color: #BE3939;
  color: #ffffff;
  padding: 1rem 1.5rem;
  font-size: 16px;
  border: none;
  outline: none;
  transition: .2s;

  &:hover{
    color: rgba($color: #000000, $alpha: .7);
  }
}

main {
  background-image: url(./assets/cold.png);
  background-size: cover;
  background-position: bottom;
  background-color: black;
  min-height: 100vh;
  display: flex;
  padding: 0 50px;
  flex-direction: column;
  align-items: center;
  transition: 0.4s;
  &.warm {
    background-image: url(./assets/warm.png);
  }
  .title{
    display: flex;
    padding: 2rem;
    span{
      margin: 20px 0;
      transition: .4s;
      font-size: 32px;
      &.warm{
        color: #fff;
      }
      &:hover{
        cursor: pointer;
        transform: scale(1.1);
      }
    }
    img{
      width: 6.2rem;
      height: 6.2rem;
    }
  }
  .search-box {
    margin-bottom: 60px;
    width: 50%;
    .search-bar {
      font-size: 24px;
      padding: 1rem;
      border: none;
      outline: none;
      background: rgba(#fff, $alpha: 0.5);
      border-radius: 0 16px 0 16px;
      transition: 0.4s;
      box-shadow: 0 0 8px rgba($color: #000000, $alpha: 0.3);
      width: 100%;
      margin-bottom: 5px;
      @media (max-width: 650px) {
        font-size: 16px;
      }
      &::placeholder {
        color: rgba($color: #000000, $alpha: 0.7);
      }

      &:focus {
        border-radius: 16px 0 16px 0;
        box-shadow: 0 0 16px rgba($color: #000000, $alpha: 0.7);
      }
    }
    .search-desc {
      font-size: 12px;
      font-weight: 700;
      color: rgba($color: #000000, $alpha: 0.7);
      padding: 0.5rem;

      @media (max-width: 900px) {
        display: none;
      }
    }
  }
  .results {
    font-size: 24px;
    color: white;
    text-align: center;
    transition: 0.4s;
    * {
      margin-bottom: 35px;
    }
    .location {
      font-size: 36px;
      font-weight: 600;
    }
    .date {
      font-size: 20px;
      font-style: italic;
    }
    .temp {
      background: rgba(#fff, $alpha: 0.05);
      font-weight: 700;
      padding: 0.5em;
      font-size: 120px;
      margin-bottom: 0;
      text-align: center;
      border-radius: 16px;
      transition: 0.4s;
      a {
        text-decoration: none;
        color: #fff;
      }
      &:hover {
        transform: scale(1.2);
        cursor: pointer;
      }
      @media (max-width: 420px) and (min-width: 380px) {
        font-size: 80px;
      }
      @media (max-width: 380px) {
        font-size: 60px;
      }
    }
    .weather {
      font-size: 50px;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 0;
      img{
        margin-top: 35px;
      }
      .desc {
        font-size: 24px;
      }
    }
  }
  //  justify-content: center;
}
</style>
