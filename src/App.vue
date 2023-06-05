<template>
  <div class="weather_app" :class="typeof weather.main !='undefined' && Math.round(weather.main.temp) > 16 ? 'warm' : ''">
    <main class="min-h-[100vh] p-6">
      <!-- 
        검색창 영역
      -->
      <div class="search-box flex justify-center text-3xl">
        <input
          type="text"
          class="search-bar w-full h-12 rounded-tl-lg rounded-br-lg text-lg hover:rounded-none hover:rounded-tr-lg hover:rounded-bl-lg focus:rounded-none focus:rounded-tr-lg focus:rounded-bl-lg"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
        <div>
          <ul>
            <li v-for="city in filteredCities" :key="city">{{ city.city }}</li>
          </ul>
      </div>
        <!-- 
          v-model은 해당 input에 입력된 값을 앞서 정의한 query에 저장해주는 역할
          @keypress는 
        -->
      </div>
      <!--  
        날씨 본문 영역
      -->
      <template v-if="weather.main">
        <div class="weather-wrap text-center">
          <div class="location-box">
          <!-- 장소 -->
            <div class="location text-white mt-20 mb-5 text-5xl md:text-7xl lg:text-8xl font-black">{{weather.name}}, {{weather.sys.country}}</div>
            <!-- 날짜 -->
            <div class="date text-white mb-5 text-2xl md:text-3xl md:mt-8 lg:text-4xl lg:mt-10 lg:mb-10">{{dateBuilder()}}</div>
          </div>
          <div class="weather-box text-center">
            <!-- 온도 -->
            <div class="temp inline-block text-white text-8xl font-black rounded-2xl py-4 px-5 mt-7 md:py-5 md:px-7">{{weather.main.temp}}&#8451;</div>
            <!-- 날씨 -->
            <div class="weather text-white text-5xl font-bold italic mt-7 md:mt-9">{{weather.weather[0].main}}</div>
            <div class="weather text-white text-5xl font-bold italic mt-7 md:mt-9">{{weather.weather[0].icon}}</div>
            <img :src="weather.weather[0].icon">
          </div>
        </div>
      </template>
    </main>

    

  </div>
</template>

<script>
import Cities from '@/assets/cities.json'

export default {
  data: function () {
    return {
      api_key: "6a2561f5c40aa678c6bff6078a026ede",
      url_base: "https://api.openweathermap.org/data/2.5/",
      // url_base: "https://api.openweathermap.org/data/2.5/weather?id=524901&lang=kr&appid=6a2561f5c40aa678c6bff6078a026ede",
      // search시 입력한 데이터가 들어갈 공간
      query: "",
      // weather은 검색 결과 데이터가 들어갈 공간
      weather: {},
      cities: Cities

    };
  },
  computed: {
    filteredCities() {
      const filterKey = this.query && this.query.toLowerCase()
      let data = this.cities
      if (filterKey) {
        data = data.filter((row) => {
          return Object.keys(row).some((key) => {
            console.log(key)
            // 중간에 있는 것도 포함
            // return String(row[key]).toLowerCase().indexOf(filterKey) > -1
            // 시작하는 것들로만 찾기
            return String(row[key]).toLowerCase().indexOf(filterKey) == 0
          })
        })
      }
      return data
    }
  },
  methods: {
    fetchWeather: function (e) {
      // fetchWeather 함수는 search에 값을 입력하고 엔터를 누를 경우 해당 값을 사용해 날씨를 검색하는 작업을 수행, 내부에서 fetch 함수를 사용하고 있으며 수행 결과를 promise를 사용해 json으로 변환한 뒤 결과를 data에 저장하는 작업을 수행
      if (e.key == "Enter") {
        let fetchUrl = `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`;
        fetch(fetchUrl)
          .then((res) => {
            return res.json();
          })
          .then((results) => {
            return this.setResult(results);
            // setResult는 입력받은 결과 값을 앞서 정의한 weather에 저장하는 역할을 수행
          });
      }
    },
    setResult: function (results) {
      this.weather = results;
    },
    dateBuilder: function () {
    // dateBuilder는 현재 시간을 보기 졸게 만들어주는 역할을 수행
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
  mounted() {
    // // 도시명 한글 입력 시 오픈웨더에서 영어로 변화해보기 위해
    // fetch(``)
    //   .then((res) => {
    //     return res.json();
    //   })
    //   .then((results) => {
    //     return this.setResult(results);
    //     // setResult는 입력받은 결과 값을 앞서 정의한 weather에 저장하는 역할을 수행
    //   });
  },
};

</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.weather_app {
  /* background-image: url("./assets/images/cold-bg.jpg"); */
  background-color: #91bdf0;
  transition: 0.4s;
}
.weather_app.warm {
  /* background-image: url("./assets/images/warm-bg.jpg"); */
  background-color: #f7c985;
}
.search-bar{
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background:rgba(255, 255, 255, 0.5);
  transition: 0.4s;
}
.search-bar:focus{
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
}
.weather-wrap .location{
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.weather-wrap .date{
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.weather-box .temp{
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather{
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
</style>
