<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-12 col-sm-3">
        <h4 class="text-bold text-primary" style="margin-bottom: 20px">Register</h4>
        <q-input outlined v-model="email" label="Email" style="margin-bottom: 15px"/>
        <q-input outlined v-model="name" label="First Name" style="margin-bottom: 15px"/>
        <q-input outlined v-model="surname" label="Surname" style="margin-bottom: 15px"/>
        <q-input outlined v-model="username" label="Username" style="margin-bottom: 15px"/>
        <q-input outlined v-model="password" type="password" label="Password" style="margin-bottom: 15px"/>
        <q-select outlined v-model="idType" :options="['National ID', 'Passport']" label="ID Type"
                  style="margin-bottom: 15px"/>
        <q-input outlined v-model="idNumber" label="ID Number" style="margin-bottom: 15px"/>
        <q-input outlined v-model="address" label="Address" style="margin-bottom: 15px"/>
        <q-input outlined v-model="homePhone" label="Home Phone" style="margin-bottom: 15px"/>
        <q-input outlined v-model="mobilePhone" label="Mobile Phone" style="margin-bottom: 15px"
                 mask="# (###) ### - ####" placeholder="# (###) ### - ###"/>
        <q-btn push @click="register" color="primary" style="width: 100%; height: 40px; margin-bottom: 15px"
               text-color="white" label="Register"/>
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
import { Loading, QSpinnerPuff, Notify } from 'quasar'

export default {
  name: 'Register',
  data () {
    return {
      name: null,
      username: null,
      email: null,
      surname: null,
      password: null,
      idType: null,
      idNumber: null,
      address: null,
      homePhone: null,
      mobilePhone: null
    }
  },
  methods: {
    register () {
      const data = JSON.stringify({
        name: this.name,
        username: this.username,
        email: this.email,
        surname: this.surname,
        password: this.password,
        idType: this.idType,
        idNumber: this.idNumber,
        address: this.address,
        homePhone: this.homePhone,
        mobilePhone: this.mobilePhone
      })
      Loading.show({
        spinner: QSpinnerPuff
      })
      axios.post('https://hcsm.herokuapp.com/api/auth/register', data, {
        headers: {
          'Content-Type': 'application/json'
        }
      }).then(() => {
        Loading.hide()
        Notify.create({
          type: 'positive',
          message: 'Registration is successful'
        })
        this.$router.push('/login')
      }).catch(() => {
        Loading.hide()
        Notify.create({
          type: 'negative',
          message: 'Please fill all the fields correctly'
        })
      })
    }
  }
}
</script>

<style scoped>

</style>
