<template>
  <div class="container">
    <!-- <div class="get-token">
      <p>Введите токен с openweathermap.org</p>
      <input type="text">
      <button>go</button>
    </div>

    <button @click="$store.commit('increment')">
      {{ $store.state.counter }}
    </button> -->

    <div class="weather">
      <h2>Дата</h2>
      <div v-for="item in selected" :key="item">
        <p>{{item}}</p>
      </div>

      <h1>ДАТА</h1>
      <div>{{date}}</div>

      <div class="pagination">
        <div @click="pagination(0)">Назад</div>
        <div @click="pagination(1)">Вперед</div>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require('axios')
const moment = require('moment')

export default {
  // //////////
  // ASYNC DATA
  async asyncData ({ params }) {

    let response
    try {
      response = await axios.get('http://api.openweathermap.org/data/2.5/forecast?q=Chelyabinsk&appid=4fdc094b2420b4c0e98280405e847f8f')
    } catch (e) {
      console.log(e)
    }
     console.log(response.data.list)
    // console.log(response)

    let intermediateResult = Array(5).fill([])

    // Разбивка по датам
    response.data.list.forEach(data => {
      // console.log(data.dt_txt)
    })

    return {
      response
    }
  },

  // //////////
  // DATA
  data() {
    return {
      date: moment().format('YYYY-MM-DD')
    }
  },

  // //////////
  // METHODS
  methods: {

    // Метод пагинации
    pagination(direction) {

      // Назад
      if(direction === 0) {
        this.date = moment(this.date).subtract(1, 'days').format('YYYY-MM-DD')
      }
      // Вперед
      else {
        this.date = moment(this.date).add(1, 'days').format('YYYY-MM-DD')

      }
    }
  },

  // //////////
  // COMPUTED
  computed: {
    // геттер вычисляемого значения
    selected: function () {

      let arr = []

      this.response.data.list .forEach(date => {
        date.dt_txt = moment(date.dt_txt).format('YYYY-MM-DD')

        if (date.dt_txt === this.date) {
          arr.push(date)
        }
      })

      return arr
    }
  }
}
</script>

<style lang="scss">
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;

  .get-token {
    padding: 30px;
    border: 1px solid grey;

    p {
      margin-bottom: 20px;
    }
  }

  .pagination {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}
</style>
