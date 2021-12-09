<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-12 col-sm-3">
        <h4 class="text-bold text-primary" style="margin-bottom: 10px">Login</h4>
        <form @submit.prevent="login">
          <q-input outlined v-model="username" name="email" label="Username" style="margin-top: 10px; margin-bottom: 15px">
            <template v-slot:prepend>
              <q-icon name="account_circle"/>
            </template>
          </q-input>
          <q-input type="password" name="password" outlined v-model="password" label="Пароль"
                   style="margin-bottom: 15px">
            <template v-slot:prepend>
              <q-icon name="lock"/>
            </template>
          </q-input>
          <q-btn push type="submit" color="primary" style="width: 100%; height: 40px" text-color="white" label="Login" />
        </form>
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
import { Loading, Notify, QSpinnerPuff } from 'quasar'
export default {
  name: 'Login',
  data () {
    return {
      username: null,
      password: null
    }
  },
  methods: {
    login () {
      const data = JSON.stringify({
        username: this.username,
        password: this.password
      })
      Loading.show({
        spinner: QSpinnerPuff
      })
      axios.post('https://hcsm.herokuapp.com/api/auth/login', data, {
        headers: {
          'Content-Type': 'application/json'
        }
      }).then(res => {
        Loading.hide()
        Notify.create({
          type: 'positive',
          message: 'Login success'
        })
        localStorage.setItem('token', res.data.token)
        localStorage.setItem('role', res.data.user.roles[0].name)
        if (res.data.user.roles[0].name === 'MANAGER') {
          this.$router.push('manager-cabinet')
        }
        if (res.data.user.roles[0].name === 'CLERK') {
          this.$router.push('clerk-cabinet')
        }
        if (res.data.user.roles[0].name === 'GUEST') {
          this.$router.push('/')
        }
      }).catch(() => {
        Loading.hide()
        Notify.create({
          type: 'negative',
          message: 'Incorrect username or password'
        })
      })
    }
  }
}
</script>

<style scoped>

</style>
