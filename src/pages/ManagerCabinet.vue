<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-8">
        <h4 class="text-bold">Manager Panel</h4>
        <q-card v-for="(h, index) in hotels" :key="index" style="padding: 15px 30px; margin-bottom: 30px;">
          <p style="font-size: 24px; margin-bottom: 5px; font-weight: bold">Hotel Name: {{ h.name }}</p>
          <p style="font-size: 16px">Description: {{ h.description }}</p>
          <p style="font-size: 16px">Phone: {{ h.phone }}</p>
          <p style="font-size: 16px">Address: {{ h.address }}</p>
          <p style="font-size: 16px;">Employees: </p>
          <div :key="e.id" v-for="e in h.employees" style="border: 1px solid black; padding: 10px; font-size: 16px">
            <p style="margin-bottom: 5px;"><span class="text-bold">FullName:</span> {{ e.name + ' ' + e.surname }}</p>
            <p style="margin-bottom: 5px;"><span class="text-bold">Hours Worked:</span> {{ e.hoursWorked }}</p>
            <p style="margin-bottom: 5px"><span class="text-bold">Pay per hour:</span> {{ e.payPerHour }}$</p>
            <p class="text-bold">Payment: <span class="text-bold text-green">{{ e.payPerHour * e.hoursWorked }}$</span></p>
            <q-btn @click="payEmployee(e.id, e.name, e.surname, e.mobilePhone, e.payPerHour)" class="bg-green text-white" no-caps push :label="'Pay ' + (e.payPerHour * e.hoursWorked) + '$' "></q-btn>
          </div>
        </q-card>
        <q-card style="padding: 15px 30px">
          <p class="text-bold" style="font-size: 24px">Add Hotel</p>
          <q-input outlined label="Name" v-model="name" style="margin-bottom: 20px;"/>
          <q-input outlined label="Description" v-model="description" style="margin-bottom: 20px;"/>
          <q-input outlined label="Address" v-model="address" style="margin-bottom: 20px;"/>
          <q-input outlined label="Phone" v-model="phone" style="margin-bottom: 20px;"/>
          <q-btn @click="createHotel" class="bg-primary text-white" label="Submit" push></q-btn>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
import { Loading, Notify, QSpinnerPuff } from 'quasar'
export default {
  name: 'ManagerCabinet',
  data () {
    return {
      hotels: null,
      name: null,
      description: null,
      address: null,
      phone: null
    }
  },
  methods: {
    async createHotel () {
      Loading.show({
        spinner: QSpinnerPuff
      })
      const data = JSON.stringify({
        name: this.name,
        description: this.description,
        address: this.address,
        phone: this.phone
      })
      await axios.post(`https://hcsm.herokuapp.com/api/hotels/?name=${this.name}&description=${this.description}&address=${this.address}&phone=${this.phone}`, data, {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      this.name = null
      this.description = null
      this.address = null
      this.phone = null
      await this.getInit()
    },
    async getInit () {
      Loading.show({
        spinner: QSpinnerPuff
      })
      const hotels = await axios.get('https://hcsm.herokuapp.com/api/hotels', {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      Loading.hide()
      this.hotels = hotels.data
    },
    async payEmployee (id, name, surname, mobilePhone, payPerHour) {
      Loading.show({
        spinner: QSpinnerPuff
      })
      await axios.put(`https://hcsm.herokuapp.com/api/employees/${id}?name=${name}&surname=${surname}&mobilePhone=${mobilePhone}&payPerHour=${payPerHour}&hoursWorked=0`, null, {
        headers: {
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      await this.getInit()
    }
  },
  async mounted () {
    if (!localStorage.getItem('token')) {
      await this.$router.push('/')
      Notify.create({
        type: 'negative',
        message: 'You are unauthorized'
      })
    } else {
      await this.getInit()
    }
  }
}
</script>

<style scoped>

</style>
