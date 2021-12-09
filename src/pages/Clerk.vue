<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-8">
        <h4 class="text-bold">Clerk Panel</h4>
        <q-card v-for="(r, index) in reservations" :key="index" style="padding: 15px 30px; margin-bottom: 30px;">
          <p style="font-size: 24px; margin-bottom: 5px; font-weight: bold">Reservation: #{{ r.id }}</p>
          <p style="font-size: 16px">Cost: {{ r.cost }}</p>
          <p style="font-size: 16px">Check In date: {{ r.checkinDate }}</p>
          <p style="font-size: 16px">Check Out date: {{ r.checkoutDate }}</p>
          <p style="font-size: 24px">Room: #{{ r.room.number }} Floor: {{ r.room.floor }}</p>
          <q-btn @click="deleteReservation(r.id)" label="Delete reservation" push class="bg-red text-white" />
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
import { Loading, Notify, QSpinnerPuff } from 'quasar'
export default {
  name: 'Clerk',
  data () {
    return {
      reservations: null
    }
  },
  methods: {
    async deleteReservation (id) {
      Loading.show({
        spinner: QSpinnerPuff
      })
      await axios.delete(`https://localhost:8080/api/reservations/${id}`, {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      await this.getInit()
    },
    async getInit () {
      Loading.show({
        spinner: QSpinnerPuff
      })
      const res = await axios.get('http://localhost:8080/api/reservations', {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      Loading.hide()
      this.reservations = res.data
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
