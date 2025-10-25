<script setup>
import { ref, onMounted, onUnmounted, inject } from 'vue'
import searchIcon from '/search-icon.svg'
import axios from 'axios'
import TheWelcome from '@/components/TheWelcome.vue'

const weatherData = ref(null)
const currentHourData = ref(null)
const activeIndex = ref(7)
const buttons = [-7, -6, -5, -4, -3, -2, -1, 0, +1, +2, +3, +4, +5, +6, +7]
const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']

const city = inject('city')
const inputCity = ref('')
const apiKey = 'HT8TLHZAA3JP82N3XPUD6PWQW'
const intervalId = ref(null)

function formatDate(offset = 0) {
  const today = new Date()
  today.setDate(today.getDate() + offset)
  const year = today.getFullYear()
  const month = String(today.getMonth() + 1).padStart(2, '0')
  const day = String(today.getDate()).padStart(2, '0')
  return `${year}-${month}-${day}`
}
const selectedDate = ref(formatDate(0))
async function fetchHourDataFor(offset = 0) {
  const date = formatDate(offset)
  selectedDate.value = date
  const res = await axios.get(
    `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${city.value}/${date}?key=${apiKey}`,
  )
  const currentHour = new Date().getHours()
  const formattedHour = currentHour.toString().padStart(2, '0') + ':00:00'
  currentHourData.value = res.data.days[0].hours.find((obj) => obj.datetime === formattedHour)
  weatherData.value = res.data
  console.log(JSON.parse(JSON.stringify(weatherData.value)))
}

function startAutoRefresh() {
  if (intervalId.value) clearInterval(intervalId.value)

  fetchHourDataFor(0)

  intervalId.value = setInterval(
    () => {
      fetchHourDataFor(0)
    },
    30 * 60 * 1000,
  )
}

onMounted(() => {
  startAutoRefresh()
})

onUnmounted(() => {
  clearInterval(intervalId.value)
})

function handleDayClick(offset, index) {
  activeIndex.value = index
  fetchHourDataFor(offset)
}

function searchInput(e) {
  if (e.key == 'Enter') {
    city.value = inputCity.value
    inputCity.value = ''
  }
  fetchHourDataFor(activeIndex.value)
  formatDate(activeIndex.value)
}
</script>
<template>
  <div v-if="isLoading">Loading...</div>
  <div v-if="error">{{ error }}</div>
  <div v-if="weatherData" class="container">
    <div class="searchCity">
      <img :src="searchIcon" alt="img" />
      <input
        v-model="inputCity"
        type="text"
        @keyup.enter="searchInput"
        placeholder="Search for a city or place"
      />
    </div>
    <div class="weatherDetailSection">
      <div class="cityAndDay">
        <p class="country">{{ weatherData.resolvedAddress }}</p>
        <p class="day">{{ days[currentDay] }}</p>
      </div>
      <div class="cloudCondition">
        <div class="">
          <div class="">
            <div class="weatherDegree">
              <img :src="`${currentHourData.icon}.png`" alt="img" />
              <div class="degree">
                <h2>{{ (((currentHourData.temp - 32) * 5) / 9).toFixed(1) }}°C</h2>
                <p>{{ currentHourData.conditions }}</p>
              </div>
            </div>
            <div class="weatherConclusion">
              <p>{{ selectedDate }}</p>
            </div>
            <div class="weatherPreviousAndToday">
              <div class="weatherPrevious">
                <p class="">Humidity</p>
                <p class="">{{ currentHourData.humidity }}</p>
              </div>
              <div class="weatherToday">
                <p class="">Visibility</p>
                <p class="">{{ currentHourData.visibility }}</p>
              </div>
            </div>
          </div>
          <div class="nextAndPrevDays">
            <button
              v-for="(button, index) in buttons"
              :class="[activeIndex === index ? 'active' : 'button']"
              :key="index"
              @click="
                () => {
                  handleDayClick(button, index)
                }
              "
            >
              {{ button }}
            </button>
          </div>
        </div>
        <div class="weatherChartSection">
          <div class="chanceOfRaining">
            <p class="">Chance of rain</p>
            <p class="">{{ currentHourData.precipprob }}%</p>
          </div>
          <div class="chartContainer">
            <TheWelcome :City="city" :formattedYear="selectedDate" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.chartContainer {
  width: 100%;
  height: 100%;
}
p {
  color: white;
}
.container {
  background-color: #2e76af;
  width: 100%;
  height: 100%;
  padding: 25px;
}
.searchCity {
  background-color: #4d8ec2;
  max-width: 600px;
  padding: 10px;
  border-radius: 20px;
  display: flex;
  gap: 10px;
  align-items: center;
}
.searchCity input {
  background-color: transparent;
  outline: none;
  border: none;
  color: #c4e5f7;
  width: 100%;
  font-size: 16px;
}
.searchCity input::placeholder {
  color: #c4e5f7;
  font-size: 16px;
}
.weatherDetailSection {
  display: flex;
  flex-direction: column;
  padding-top: 30px;
  gap: 12px;
}
.country {
  font-size: 50px;
  font-weight: 900;
  color: #fefdfb;
}
.day {
  font-size: 25px;
  font-weight: 500;
  padding-top: 3px;
  color: #cee8f5;
}
.cloudCondition {
  display: flex;
  width: 100%;
  /* ** justify-content: center; ** */
  align-items: top;
  gap: 80px;
}
.cloudCondition > :first-child {
  display: flex;
  flex: 1;
  flex-direction: column;
  justify-content: space-between;
  width: 100%;
}
.weatherDegree {
  display: flex;
  gap: 30px;
  justify-content: space-between;
}
.weatherDegree img {
  width: 150px;
}
.degree h2 {
  font-size: 65px;
  color: #fefcf5;
}
.degree p {
  font-size: 25px;
  font-weight: 500;
}
.weatherConclusion {
  background-color: #549dcc;
  padding: 15px;
  font-size: 24px;
  border-radius: 10px;
  margin-top: 30px;
}
.weatherPreviousAndToday {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}
.weatherPrevious,
.weatherToday,
.weatherPrevious {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
}
.weatherPrevious p,
.weatherToday p,
.weatherPrevious p {
  font-size: 30px;
}
.weatherPrevious img,
.weatherToday img,
.weatherPrevious img {
  width: 40px;
}
.weatherChartSection {
  width: 100%;
  display: flex;
  flex-direction: column;
  flex: 1.2;
}
.chanceOfRaining {
  background-color: #549dcc;
  padding: 15px;
  font-size: 24px;
  border-radius: 10px;
  display: flex;
  justify-content: space-between;
}
.weatherPreviousAndTodayAndTomorrow {
  background-color: #549dcc;
  padding: 25px;
  border-radius: 10px;
}
.weatherPreviousAndTodayAndTomorrow .cloudTOday {
  display: flex;
  align-items: center;
  flex-direction: column;
  font-size: 30px;
}
.weatherPreviousAndTodayAndTomorrow img {
  width: 50px;
}
.nextAndPrevDays {
  background-color: white;
  width: fit-content;
  margin-top: 20px;
}
.nextAndPrevDays > .button {
  padding: 10px;
  cursor: pointer;
  border: #549dcc;
  background-color: transparent;
}
.nextAndPrevDays > .active {
  background-color: #549dcc;
  padding: 6px 10px;
  border: none;
  border-radius: 100%;
  color: white;
}
</style>
<!-- DOCUMENTATION (ABOUT PROJECT)
    FEATURES
    1. SEARCH FILTER (Accept data for any state and fetch the state data)
    2. Can see weather condition for previous and next 7 days
    3. There is a chart that shows the hourly temperature for any day picked
    4. It auto update every 30 minutes in order for data to be current
-->
