<script>
import axios from 'axios'

export default {
  data() {
    return {
      city: '',
      error: '',
      info: null,
      localInfo: null,
      localCity: '',
      localTemp: '',
      localFeelsTemp: '',
      localMinTemp: '',
      localMaxTemp: '',
      localSpeedWind: ''
    }
  },
  computed: {
    cityName() {
      return `- «${this.city}»`
    },
    showCity() {
      return `${this.info.name}:`
    },
    showTemp() {
      return `Температура: ${this.info.main.temp} \u2103`
    },
    showFeelsTemp() {
      return `Ощущается как: ${this.info.main.feels_like} \u2103`
    },
    showMinTemp() {
      return `Минимальная температура: ${this.info.main.temp_min} \u2103`
    },
    showMaxTemp() {
      return `Максимальная температура: ${this.info.main.temp_max} \u2103`
    },
    showSpeedWind() {
      return `Ветер: ${this.info.wind.speed} м/с`
    }
  },
  methods: {
    getWeather() {
      if (this.city.trim().length < 2) {
        this.error = 'Нужно название больше одного символа'
        return false
      }
      this.error = ''

      axios
        .get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=26e25ec24b86fbaeb9d9aa736cbe3256`
        )
        .then((res) => (this.info = res.data))
        .catch((error) => {
          this.error = `Не удалось получить данные о погоде`
          console.error('Weather API error:', error)
        })
    }
  },
  mounted() {
    navigator.geolocation.getCurrentPosition((position) => {
      this.lat = position.coords.latitude
      this.lon = position.coords.longitude

      axios
        .get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${this.lat}&lon=${this.lon}&units=metric&appid=26e25ec24b86fbaeb9d9aa736cbe3256`
        )
        .then((res) => {
          this.localCity = `${res.data.name}:`
          this.localTemp = `Температура: ${res.data.main.temp} \u2103`
          this.localFeelsTemp = `Ощущается как: ${res.data.main.feels_like} \u2103`
          this.localMinTemp = `Минимальная температура: ${res.data.main.temp_min} \u2103`
          this.localMaxTemp = `Максимальная температура: ${res.data.main.temp_max} \u2103`
          this.localSpeedWind = `Ветер: ${res.data.wind.speed} м/с`
          this.localInfo = res.data
        })
        .catch((error) => {
          console.error('Local Weather API error:', error)
        })
    })

    document.querySelector('.wrapper__input').addEventListener('keydown', (event) => {
      if (event.key === 'Enter') {
        document.querySelector('.wrapper__button').click()
      }
    })
    document.querySelector('.wrapper__input').addEventListener('input', () => {
      this.info = null
    })
  }
}
</script>

<template>
  <main>
    <div class="wrapper">
      <h1>Погода</h1>
      <p v-if="city === ''" class="text">Узнать погоду в вашем городе!</p>
      <p v-else class="text">
        Погода в городе <span class="text__transform">{{ cityName }}</span>
      </p>
      <div class="wrapper__search">
        <input type="text" v-model="city" placeholder="Название города" class="wrapper__input" />
        <button v-if="city !== ''" class="wrapper__button" @click="getWeather()">
          Узнать погоду
        </button>
        <button v-else class="wrapper__button" disabled>Ввод</button>
      </div>
      <p class="error">{{ city.trim().length < 2 && city.trim().length !== 0 ? error : '' }}</p>
      <div class="weather">
        <div v-if="info !== null" class="result">
          <p class="weather__info">{{ showCity }}</p>
          <p class="weather__info">{{ showTemp }}</p>
          <p class="weather__info">{{ showFeelsTemp }}</p>
          <p class="weather__info">{{ showMinTemp }}</p>
          <p class="weather__info">{{ showMaxTemp }}</p>
          <p class="weather__info">{{ showSpeedWind }}</p>
        </div>
        <div class="local__result">
          <p class="weather__info">{{ localCity }}</p>
          <p class="weather__info">{{ localTemp }}</p>
          <p class="weather__info">{{ localFeelsTemp }}</p>
          <p class="weather__info">{{ localMinTemp }}</p>
          <p class="weather__info">{{ localMaxTemp }}</p>
          <p class="weather__info">{{ localSpeedWind }}</p>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
.wrapper {
  max-width: 1200px;
  width: 100%;
  border-radius: 20px;
  background-color: #353535;
  text-align: center;
  padding: 30px;
  margin: 0 auto;
  box-shadow: 0 10px 10px rgba(0, 0, 0, 0.5);
}

.wrapper h1 {
  font-size: 36px;
  font-weight: normal;
  color: #fff;
  text-shadow: 0 3px 5px rgb(0, 0, 0, 1);
}
.text {
  font-size: 26px;
  margin-top: 10px;
  color: #fff;
  text-shadow: 0 3px 5px rgb(0, 0, 0, 1);
}

@media (max-width: 500px) {
  .wrapper h1 {
    font-size: 26px;
  }
  .text {
    font-size: 20px;
  }
}

.text__transform {
  text-transform: capitalize;
}

.wrapper__search {
  display: flex;
  gap: 30px;
  margin-top: 50px;
}
.wrapper__input {
  padding: 5px;
  width: 100%;
  font-size: 18px;
  background-color: transparent;
  border: none;
  outline: none;
  box-shadow: inset 0 -6px 10px -9px rgba(0, 0, 0, 0.7);
  color: #fff;
}
.wrapper__input::placeholder {
  font-size: 18px;
  color: rgba(255, 255, 255, 0.3);
}
.wrapper__button {
  max-width: 180px;
  width: 100%;
  border: none;
  font-size: 14px;
  color: #f5f5f5;
  background-color: #c0ae09;
  padding: 10px;
  border-radius: 5px;
  height: 100%;
  cursor: pointer;
  transition: 0.3s;
}

.wrapper__button:hover {
  background-color: #61c009;
  box-shadow: 0 5px 10px 0 rgba(0, 0, 0, 0.7);
}

.wrapper__button:active {
  box-shadow: none;
}

.wrapper__button:disabled {
  opacity: 0.5;
  cursor: default;
}

.error {
  font-size: 14px;
  text-align: left;
  margin-top: 10px;
  color: #cb2e2e;
  height: 20px;
}

@media (max-width: 500px) {
  .wrapper__input,
  .wrapper__input::placeholder,
  .wrapper__button,
  .error {
    font-size: 12px;
  }
}

.weather {
  display: flex;
  justify-content: space-between;
  gap: 10px;
}

.result,
.local__result {
  margin-top: 30px;
  display: flex;
  flex-direction: column;
  align-items: baseline;
  gap: 10px;
}

.result .weather__info {
  text-align: left;
}

.weather__info {
  color: #fff;
  font-size: 16px;
}

@media (max-width: 500px) {
  .weather__info {
    font-size: 12px;
  }
}

.local__result {
  align-items: flex-end;
  margin-left: auto;
}

.local__result .weather__info {
  text-align: right;
}
</style>
