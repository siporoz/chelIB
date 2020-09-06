<template>
  <div class="container">

    <div class="date-info">
      <h2>Погода в Челябинске - {{moment(date).format('MM.DD')}}</h2>

      <!-- Пагинация -->
      <div class="pagination">
        <div class="butt" @click="pagination(0)">Предыдущий день -</div>
        <div class="butt" @click="pagination(1)">Следующий день +</div>
      </div>
    </div>

    <!-- <div>{{token}}</div> -->

    <div class="weather">
      <h2>Погода в течение дня</h2>

      <!-- Список с погодой -->
      <div class="items">
        <div v-if="selected.length === 0">
          <h2>К сожалению данные отсутвуют :( </h2>
        </div>

        <div class="all-time-weather">
          <div v-for="item in selected" :key="item" class="time-weather">

            <div class="item">
              <p class="time">{{moment(item.dt_txt).format('HH:mm')}}</p>
              <div class="main-info">
                <img src="https://i.ibb.co/p3tQxRy/2020-09-06-00-54-41.png">

                <div class="format">
                  <p>{{Math.round(item.main.temp - 273.15)}}</p>
                  <p class="celsius">°C</p>
                </div>
              </div>
              <p>Ощущается как: {{Math.round(item.main.feels_like - 273.15)}}°C</p>
              <p>Давление: {{item.main.pressure}}</p>
              <p>{{item.weather.main}}</p>
            </div>
          </div>
        </div>
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

    let token

    // Получение токена с localStorage
    if(localStorage.token) token = localStorage.token

    console.log(token)

    // Если токен отсутвует у пользователя
    if(token === 'null' || token === undefined) {

      while(localStorage.token === undefined || localStorage.token === 'null') {
        localStorage.token = prompt("Введите ключь от openweathermap.org", "")
      }
    }

    let response
    try {
      response = await axios.get(`http://api.openweathermap.org/data/2.5/forecast?q=Chelyabinsk&appid=${localStorage.token}`)
    } catch (e) {
      alert('Что пошло не так...')
    }
    console.log(response.data.list)

    let intermediateResult = Array(5).fill([])

    return {
      response, token
    }
  },

  // //////////
  // DATA
  data() {
    return {
      date: moment().format('YYYY-MM-DD'),

      moment: moment,

      // На удаление
      name:'',

      token: null
    }
  },

  // //////////
  // METHODS
  methods: {

    some() {
      const some = prompt("Введите ключь от openweathermap.org", "")

      return some
    },

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
        const newData = moment(date.dt_txt).format('YYYY-MM-DD')
        if (newData === this.date) {
          arr.push(date)
        }
      })

      return arr
    }
  },

  // //////////
  // WATCH
  watch:{
    token(newToken) {
      localStorage.token = newToken
    }
  }
}
</script>

<style lang="scss">
.container {
  margin: 0 auto;
  min-height: 100vh;
  padding: 50px;
  color: #3c414b;

  // Заголовок с датой
  .date-info {
    margin-bottom: 30px;
    display: flex;

    // Пагинация
    .pagination {
      display: grid;
      grid-template-columns: 1fr 1fr;
      grid-gap: 10px;
      align-items: center;
      margin-left: 20px;

      // Кнопки
      .butt {
        padding: 5px 10px;
        background: #eee;
        border-radius: 35px;
        cursor: pointer;
      }
    }
  }

  // Блок с погодой
  .weather {
    padding: 30px;
    border: 1px solid #d4d4d4;
    border-radius: 20px;

    h2 {
      margin-bottom: 40px;
    }

    // Погода по часам
    .items {
      display: flex;

      .all-time-weather {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;

        @media screen and (max-width: 1180px) {
          grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
        }

        @media screen and (max-width: 800px) {
          grid-template-columns: 1fr 1fr 1fr 1fr;
        }

        @media screen and (max-width: 690px) {
          grid-template-columns: 1fr 1fr;
        }

        .time-weather {
          width: 100%;
          border-radius: 10px;

          .item {

            margin-right: 40px;

            // Время
            .time {
              font-size: 18px;
              font-weight: 500;
              margin-bottom: 10px;
            }

            .main-info {
              display: grid;
              grid-template-columns: 1fr 1fr;
              align-items: center;

              img {
                width: 58px;
              }

              .format {
                display: flex;

                p {
                  font-size: 28px;
                  font-weight: 600;
                }

                .celsius {
                  font-size: 14px;
                  font-weight: 400;
                }
              }
            }
          }
        }
      }
    }
  }

  .get-token {
    padding: 30px;
    border: 1px solid grey;

    p {
      margin-bottom: 20px;
    }
  }
}
</style>
