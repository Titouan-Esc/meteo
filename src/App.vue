<template>
  <main>
    <div class="left_container">
      <img src="./assets/cloudy.png" alt="logo de la meteo" class="logo" />
      <form class="search-location" @submit.prevent="getWeather">
        <input
          type="text"
          placeholder="Type in a city"
          v-model="citySearch"
          autocomplete="off"
        />
      </form>
    </div>
    <div class="right_container" :class="isDay ? 'night' : 'days'">
      <div class="not_found" v-if="!searchResult">
        <h3>City not found</h3>
      </div>
      <header class="localisation-info" v-if="searchResult">
        <h1>{{ weather.cityName }}</h1>
        <p class="country">{{ weather.country }}</p>
      </header>
      <section class="details" v-if="searchResult">
        <h2 class="temp">Température estimée à: {{ weather.temperature }}°C</h2>
        <p class="temp_max">Temperature maximum: {{ weather.lowTemp }}°C</p>
        <p class="temp_min">Temperature minimum: {{ weather.highTemp }}°C</p>
        <p class="humidity">Le taux d'humidité: {{ weather.humidity }}%</p>
      </section>
      <footer v-if="searchResult">
        <p>{{ weather.description }}</p>
        <p class="feels_like">Resentie: {{ weather.feelsLike }}°C</p>
      </footer>
    </div>
  </main>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      isDay: false,
      searchResult: false,
      citySearch: "",
      weather: {
        cityName: "Bordeaux",
        country: "FR",
        temperature: 9,
        description: "Cloudy with a chance of meatballs",
        lowTemp: 0,
        highTemp: 15,
        feelsLike: "Death",
        humidity: 55,
      },
    };
  },

  methods: {
    getWeather: async function() {
      console.log(this.citySearch);
      const key = "69b47498249a5304a0dac0017a13d253";
      const callURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric `;

      //? Appel à l'API avec await dans un try
      try {
        const reponse = await fetch(callURL);
        const data = await reponse.json();
        console.log(data);
        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_max);
        this.weather.highTemp = Math.round(data.main.temp_min);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = Math.round(data.main.humidity);

        this.searchResult = true;
        const dayNight = data.weather[0].icon;

        if (dayNight.includes("d")) {
          this.isDay = false;
        } else {
          this.isDay = true;
        }
      } catch (error) {
        console.log(error);
        this.searchResult = false;
      }
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  list-style: none;
  text-decoration: none;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

main {
  height: 100vh;
  display: flex;
}

.left_container {
  width: 20%;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: gray;
  padding-top: 50px;
}

.left_container input {
  width: 50%;
}

.logo {
  width: 10vw;
}

.right_container {
  width: 80%;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 50px;
}

.localisation-info {
  margin-bottom: 50px;
}

.details {
  margin-bottom: 500px;
}

.days {
  background-image: url(./assets/pexels-josh-sorenson-1478524.jpg);
}

.night {
  background-image: url(./assets/pexels-neale-lasalle-631477.jpg);
  color: white;
}
</style>
