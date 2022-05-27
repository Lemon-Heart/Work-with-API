<template>
  <div id="__app">
    <span v-if="error" class="errorMessage">{{ error }}</span>
    <the-sidebar :balance="balance" :balance-title="balanceTitle" @logout="logout" />
    <main class="content">
      <the-games :games="games" @play="play" />
      <vue-pagination v-if="pageCount" :value="currentPage" :page-count="pageCount" :labels="{ next: false, prev: false }" @input="pagination" />
    </main>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  middleware ({ $cookies, redirect }) {
    if (!$cookies.get('token')) {
      redirect('/login')
    }
  },
  data () {
    return {
      pageCount: null,
      currentPage: 1,
      perPage: 50,
      error: null,
      interval: null,
      timeout: null,
      balance: [],
      balanceTitle: ['Real balance', 'Total balance', 'Free spins'],
      games: []
    }
  },
  mounted () {
    this.getGames() // Получаем список игр
    this.getUser() // Получаем данные пользователя
    this.interval1 = setInterval(() => {
      this.getUser()
    }, 1000 * 30) // Каждые 30 секунд обновляем данные пользователя
    this.autoRefreshToken() // Запускаем автообновление токена
  },
  beforeDestroy () {
    clearInterval(this.interval)
    clearTimeout(this.timeout)
  },
  methods: {
    async getUser () {
      await this.$axios.$get(`/v2/users/me/balance?clientId=default&auth=${this.$cookies.get('token')}`).then((res) => {
        this.balance = res.data
      }).catch((error) => {
        if (error.response) {
          this.error = error.response.data.errors[0].detail
        } else if (error.request) {
          this.error = 'sorry, something wrong'
        }
      })
    },
    autoRefreshToken () {
      const now = this.$moment()
      const tokenLifeTime = this.$moment(this.$cookies.get('tokenLifeTime'))
      this.timeout = setTimeout(() => {
        this.refreshToken()
      }, tokenLifeTime - now)
    },
    async refreshToken () {
      await this.$axios.$post('/auth/token', { refreshToken: this.$cookies.get('refreshToken') }).then((res) => {
        this.$cookies.set('token', res.token, { maxAge: 900 })
        this.$cookies.set('refreshToken', res['refresh-token'], { maxAge: 900 })
        this.$cookies.set('tokenLifeTime', this.$moment().add(800, 'seconds'), { maxAge: 800 })
        this.autoRefreshToken()
      }).catch((error) => {
        if (error.response) {
          this.error = error.response.data.errors[0].detail
        } else if (error.request) {
          this.error = 'sorry, something wrong'
        }
      })
    },
    async getGames () {
      await this.$axios.$get(`v2/casino/games?current-page=${this.currentPage}&per-page=${this.perPage}`).then((res) => {
        this.pageCount = res.meta['page-count']
        this.currentPage = res.meta['current-page']
        this.games = res.data
      }).catch((error) => {
        if (error.response) {
          this.error = error.response.data.errors[0].detail
        } else if (error.request) {
          this.error = 'sorry, something wrong'
        }
      })
    },
    async pagination (page) {
      await this.$axios.$get(`v2/casino/games?page=${page}&per-page=${this.perPage}`).then((res) => {
        this.pageCount = res.meta['page-count']
        this.currentPage = res.meta['current-page']
        this.games = res.data
      }).catch((error) => {
        if (error.response) {
          this.error = error.response.data.errors[0].detail
        } else if (error.request) {
          this.error = 'sorry, something wrong'
        }
      })
    },
    async play (gameId) {
      await this.$axios.$post(`/v2/casino/games/${gameId}/session-demo`, { gameId }).then((res) => {
        window.open(res.data[0].attributes['launch-options']['game-url'])
      }).catch((error) => {
        if (error.response) {
          this.error = error.response.data.errors[0].detail
        } else if (error.request) {
          this.error = 'sorry, something wrong'
        }
      })
    },
    logout () {
      this.$cookies.removeAll()
      this.$router.push('login')
    }
  }
}
</script>
