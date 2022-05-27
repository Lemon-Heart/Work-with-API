<template>
  <div id="__app" class="col">
    <span v-if="error" class="errorMessage">{{ error }}</span>
    <form @submit.prevent="auth">
      <AppInput v-model="login.value" :label="login.label" :name="login.name" />
      <AppInput v-model="password.value" :label="password.label" :name="password.name" />
      <button class="btn">
        Log in
      </button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'LoginPage',
  middleware ({ $cookies, redirect }) {
    if ($cookies.get('token')) {
      redirect('/')
    }
  },
  data () {
    return {
      error: null,
      login: {
        label: 'Your login',
        name: 'login',
        value: ''
      },
      password: {
        label: 'Your password',
        name: 'password',
        value: ''
      }
    }
  },
  methods: {
    async auth () {
      const login = this.login.value
      const password = this.password.value
      await this.$axios.$post('/v2/login', { login, password }).then((res) => {
        this.$cookies.set('id', res.data[0].id)
        this.$cookies.set('token', res.data[0].attributes.token, { maxAge: 900 })
        this.$cookies.set('refreshToken', res.data[0].attributes['refresh-token'], { maxAge: 900 })
        this.$cookies.set('tokenLifeTime', this.$moment().add(800, 'seconds'), { maxAge: 800 })
        this.$router.push('/')
      }).catch((error) => {
        if (error.response) {
          // client received an error response
          this.error = error.response.data.errors[0].detail
        } else if (error.request) {
          // client never received a response, or request never left
          this.error = 'sorry, something wrong'
        } else {
          // anything else
        }
      })
    }
  }
}
</script>

<style lang="scss">
  form {
    display: flex;
    align-items: flex-end;
    flex-direction: column;
    margin: auto;
    padding: 40px;
    border-radius: 10px;
    box-shadow: 0 2px 6px white;
    background: white;
    & > *:not(:last-child) {
      margin-bottom: 30px;
    }
  }
</style>
