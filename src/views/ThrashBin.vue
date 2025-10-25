<script setup>
// const weatherData = ref(null)
// const currentHourData = ref(null)
// const currentDay = ref(null)
// let intervalId = null
// const isLoading = ref(false)
// const error = ref('')
// const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
// let city = inject('city')
// let localCity = ref('')
// let num = ref(0)
// const activeIndex = ref(7)
// let formattedYear = ref('')
// let buttons = [-7, -6, -5, -4, -3, -2, -1, 0, +1, +2, +3, +4, +5, +6, +7]
// function addDaysToToday(extraDays = 0) {
//   const today = new Date()
//   today.setDate(today.getDate() + extraDays)
//   const year = today.getFullYear()
//   const month = String(today.getMonth() + 1).padStart(2, '0')
//   const day = String(today.getDate()).padStart(2, '0')

//   const dateStr = `${year}-${month}-${day}`
//   formattedYear.value = dateStr
//   num.value = extraDays
//   return dateStr
// }
// const getWeather = async () => {
//   try {
//     const currentRes = await axios.get(
//       `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${city.value}/${formattedYear.value}?key=${apiKey}`,
//     )
//     weatherData.value = currentRes.data
//     currentDay.value = new Date(weatherData.value.days[0].datetime).getDay()
//     return currentRes
//   } catch {
//     error.value = 'error found'
//   } finally {
//     isLoading.value = false
//   }
// }
// addDaysToToday(0)
// getWeather()

// async function updateCurrentHourData() {
//   const currentRes = await axios.get(
//     `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${city.value}/${addDaysToToday(num.value)}?key=${apiKey}`,
//   )
//   const now = Math.floor(Date.now() / 1000)
//   currentHourData.value = currentRes.data.days[0].hours.find((obj) => {
//     const start = obj.datetimeEpoch
//     const end = start + 3600
//     return now >= start && now < end
//   })
//   if (currentHourData.value?.datetime == '00:00:00') {
//     getWeather()
//   }
// }

// onMounted(() => {
//   updateCurrentHourData()
//   intervalId = setInterval(updateCurrentHourData, 18 * 100000)
// })
// onUnmounted(() => {
//   clearInterval(intervalId)
// })
// watch(activeIndex, (newVal) => {
//   if (!city.value) return
//   const date = buttons[newVal]
//   addDaysToToday(date)
//   getWeather()
//   // updateCurrentHourData()
//   // console.log(getWeather())
// })
// function test(e) {
//   if (e.key == 'Enter') {
//     city.value = inputCity.value
//     inputCity.value = ''
//   }
//   getWeather()
//   updateCurrentHourData()
//   addDaysToToday()
// }
</script>
