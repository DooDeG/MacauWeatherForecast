<template>
  <!-- https://api.help.bj.cn/api/ -->
  <div class="bg-black text-white h-screen w-screen font-sans">
    <ul class="navbar">
      <li class="dropdown">
        <button class="dropbtn" @mouseover="show = true" @mouseout="show = false">
          <div class="w-8 h-8">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
              <path fill="#FFFFFF" d="M21 8H7A1 1 0 0 1 7 6H21a1 1 0 0 1 0 2zM21 13H7a1 1 0 0 1 0-2H21a1 1 0 0 1 0 2zM21 18H7a1 1 0 0 1 0-2H21a1 1 0 0 1 0 2zM3 8a.99993.99993 0 0 1-.38037-.08008A1.1515 1.1515 0 0 1 2.29 7.71a1.16213 1.16213 0 0 1-.21045-.33008.94637.94637 0 0 1 0-.75976A1.14883 1.14883 0 0 1 2.29 6.29a.99764.99764 0 0 1 1.08984-.21A1.03418 1.03418 0 0 1 3.71 6.29a1.15772 1.15772 0 0 1 .21.33008.94107.94107 0 0 1 0 .75976A1.17124 1.17124 0 0 1 3.71 7.71.99183.99183 0 0 1 3 8zM3 13a.99993.99993 0 0 1-.38037-.08008A1.1515 1.1515 0 0 1 2.29 12.71a1.16213 1.16213 0 0 1-.21045-.33008.94637.94637 0 0 1 0-.75976A1.03011 1.03011 0 0 1 2.29 11.29a1.1515 1.1515 0 0 1 .32959-.21A.9986.9986 0 0 1 3.71 11.29a1.03724 1.03724 0 0 1 .21.33008.94107.94107 0 0 1 0 .75976 1.17124 1.17124 0 0 1-.21.33008 1.15384 1.15384 0 0 1-.33008.21A.9994.9994 0 0 1 3 13zM3 18a.99993.99993 0 0 1-.38037-.08008A1.1515 1.1515 0 0 1 2.29 17.71a.99108.99108 0 0 1-.21045-1.08984A1.03011 1.03011 0 0 1 2.29 16.29a1.02673 1.02673 0 0 1 .32959-.21.99537.99537 0 0 1 .76025 0 1.03418 1.03418 0 0 1 .33008.21 1.03724 1.03724 0 0 1 .21.33008A.98919.98919 0 0 1 3.71 17.71a1.15384 1.15384 0 0 1-.33008.21A.9994.9994 0 0 1 3 18z" />
            </svg>
          </div>
        </button>
        <transition name="dropdown">
          <ul v-if="show" class="dropdown-content" @mouseover="show = true" @mouseout="show = false">
            <li>
              <div @click="changeCitycode('101330101')">
                <a @click="changeCity('澳门')">澳门</a>
              </div>
            </li>
            <li>
              <div @click="changeCitycode('101330102')">
                <a @click="changeCity('澳门氹仔岛')">澳门氹仔岛</a>
              </div>
            </li>
            <li>
              <div @click="changeCitycode('101330103')">
                <a @click="changeCity('澳门路环岛')">澳门路环岛</a>
              </div>
            </li>
          </ul>
        </transition>
      </li>
    </ul>

    <div class="text-center">
      <div class="-p-10 text-3xl">
        {{ city }}°
      </div>
      <div class="flex justify-center items-center">
        <img :src="forecast.weatherimg"
             class="w-12 h-12 -ml-4 -mb-2"
        >
      </div>
      <div class="-p-10" style="font-size:80px;">
        {{ forecast.temp }}°
      </div>
      <div class="-mt-6 -ml-4 mb-10">
        最高{{ forecast.Ttemp }}° 最低{{ forecast.Ltemp }}°
      </div>
    </div>
    <hr class="bg-white">
    <div class="flex overflow-auto">
      <div v-for="e in forecast.weather36h" :key="e.id" class="text-center px-2 py-1 whitespace-no-wrap">
        <div> {{ e.time }}時 </div>
        <div> {{ e.weather }} </div>
        <div> {{ e.temp }}° </div>
      </div>
      <!-- <ul id="example-1">
        <li v-for="item in items" :key="item.message">
          {{ item.message }}
        </li>
      </ul> -->
    </div>
    <hr class="bg-white">
    <div class="p-2">
      <div v-for="d in forecast.weatherFourDay" :key="d.id" class="whitespace-no-wrap">
        <div class="flex justify-between">
          <div> {{ d.date }} </div>
          <div> {{ d.weather }} </div>
          <div> {{ d.temphigh }} &ensp;&ensp; {{ d.templow }} </div>
        </div>
      </div>
    </div>
    <hr class="bg-white">
  </div>
</template>

<script>
export default {
  data: () => ({
    city: '澳门',
    citycode: '101330101',
    forecast: {
      temp: '',
      Ttemp: '',
      Ltemp: '',
      weatherimg: '',
      weather36h: [
        { }, { }, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}, {}
      ],
      // weather36h: [],
      weatherFourDay: []

    },
    time36h: [],
    weatherinfo36h: [],
    temp36h: [],
    show: false,
    map: new Map()
    // forecast: []

  }),
  created () {
    this.getTLTemp()
    this.getEThreeHourTemp()
    this.getEFourDayTemp()
  },

  methods: {
    async getTLTemp () {
      this.axios.get('https://api.help.bj.cn/apis/weather2d/?id=' + this.city)
        .then(function (response) {
          var res = JSON.parse(JSON.stringify(response.data))
          console.log('Top&Low temp:')
          console.log(res)
          this.forecast.Ttemp = res.temp.slice(0, 2)
          this.forecast.Ltemp = res.temp.slice(3, 5)
          this.forecast.weatherimg = res.weatherimg
        }.bind(this))
        // .catch(function (error) {
        //   alert('请求失败 ' + error)
        // })
    },
    async getEThreeHourTemp () {
      this.axios.get('https://api.help.bj.cn/apis/weather36h/?id=' + this.city)
        .then(function (response) {
          var res = JSON.parse(JSON.stringify(response.data))
          console.log('Three hour:')
          console.log(res)
          this.forecast.temp = res.weather36h[0].temp

          this.forecast.weather36h = res.weather36h
          console.log(this.forecast.weather36h)
          this.forecast.weather36h[0].time = '現'
          for (var i = 1; i <= 16; i++) {
            this.forecast.weather36h[i].time = res.weather36h[i].time.slice(11, 13)
          }

          // this.forecast.weather36h = JSON.parse(JSON.stringify(this.forecast.weather36h))
          // console.log(this.forecast.weather36h)

          // this.time36h = res.weather36h.map(n => n.time.slice(11, 13))
          // this.time36h[0] = '現'
          // this.weatherinfo36h = res.weather36h.map(n => n.weather)
          // this.temp36h = res.weather36h.map(n => n.temp)
          // console.log(this.time36h)
          // console.log(this.weatherinfo36h)
          // console.log(this.temp36h)
          // for (var i = 0; i <= 16; i++) {
          //   // this.map = this.map.set('temp', 'this.time36h[i]')
          //   // this.forecast.weather36h.set('temp', this.time36h[i])
          //   this.forecast.weather36h.push('{ time:' + this.time36h[i], 'weather:' + this.weatherinfo36h[i], 'time:' + this.temp36h[i] + '}')
          // }
          // this.forecast.weather36h = JSON.parse(this.forecast.weather36h)
          // console.log(this.forecast.weather36h)
          // // console.log(this.map.temp)
        }.bind(this))
        // .catch(function (error) {
        //   alert('请求失败 ' + error)
        // })
    },
    async getEFourDayTemp () {
      this.axios.get('https://api.help.bj.cn/apis/weather6d/?id=' + this.citycode)
        .then(function (response) {
          var res = JSON.parse(JSON.stringify(response.data))
          console.log('Four day:')
          console.log(res)
          this.forecast.weatherFourDay = res.data.forecast
          this.forecast.weatherFourDay[0].date = this.forecast.weatherFourDay[0].date.slice(0, 4) + '今天'
          console.log(this.forecast.weatherFourDay)
        }.bind(this))
    },
    changeCity: function (city) {
      this.city = city
      this.getEThreeHourTemp()
      this.getTLTemp()
    },
    changeCitycode: function (citycode) {
      this.citycode = citycode
      this.getEFourDayTemp()
    }
  }

}

</script>

<style scoped>

ul {
  list-style-type: none;
  overflow: hidden;
  background-color: #333;
  font-family: Arial;
  margin: 0;
  padding: 0;
}

.navbar a {
  float: left;
  font-size: 16px;
  color: white;
  text-align: center;
  padding: 14px 16px;
  text-decoration: none;
}

.navbar a:hover, .dropdown:hover .dropbtn {
  background-color: black;
}

.dropdown {
  float: left;
  overflow: hidden;
}

.dropdown .dropbtn {
  font-size: 12px;
  border: none;
  outline: none;
  color: white;
  padding: 7px 7px;
  background-color: inherit;
  font-family: inherit;
  margin: 0;
}

.dropdown-content {
  /* position: absolute; */
  background-color: #f9f9f9;
  min-width: 160px;
}

.dropdown-content a {
  float: none;
  color: black;
  text-decoration: none;
  display: block;
  text-align: left;
}

.dropdown-content a:hover {
  background-color: #ddd;
}

.dropdown-enter,
.dropdown-leave-to {
  transform: scaleY(0.7);
  opacity: 0;
}

.dropdown-enter-to,
.dropdown-leave {
  opacity: 1;
  transform: scaleY(1);
}

.dropdown-enter-active,
.dropdown-leave-active {
  transition: all 0.3s ease-out;
  transform-origin: top center;
}
</style>
