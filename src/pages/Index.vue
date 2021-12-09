<template>
  <q-page>
    <div class="main-search bg-primary">
      <h4 class="text-white w-100 text-center text-bold" style="margin: 0; margin-bottom: 20px">Book a room</h4>
      <div v-if="hotels" class="row justify-center" style="width: 100%">
        <div class="col-8">
          <q-select v-model="hotel" label="Choose Hotel" class="bg-white" :options="hotelsArray" outlined
                    style="width: 100%; margin-bottom: 30px;"/>
          <q-select v-model="roomType" label="Choose Room Type" class="bg-white" :options="roomTypesArr" outlined style="width: 100%"/>
        </div>
      </div>
    </div>
      <h3 v-if="rooms" class="q-ml-lg">Available Rooms</h3>
    <div v-if="rooms" class="row q-px-lg">
      <q-card v-for="r in rooms" :key="r.id" style="padding: 20px; width: 30%">
        <h6 class="q-my-none">Room #{{ r.number }}</h6>
        <p>Floor {{ r.floor }}</p>
        <div style="margin-bottom: 20px;">
          <label>Check In Date: </label>
          <input v-model="checkin" type="date" style="margin-right: 20px;"/>
        </div>
        <div>
          <label>Check Out Date: </label>
          <input v-model="checkout" type="date"/>
        </div>
        <q-btn @click="book(r.id)" push label="Book" style="margin-top: 20px;"></q-btn>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { Loading, Notify, QSpinnerPuff } from 'quasar'
import axios from 'axios'

export default {
  name: 'PageIndex',
  data () {
    return {
      hotels: null,
      hotel: null,
      roomTypes: null,
      roomType: null,
      rooms: null,
      checkin: null,
      checkout: null
    }
  },
  methods: {
    async book (id) {
      Loading.show({
        spinner: QSpinnerPuff
      })
      await axios.post(`http://localhost:8080/api/guest/reservations?checkinDate=${this.checkin}&checkoutDate=${this.checkout}&room=${id}`, null, {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      Loading.hide()
      Notify.create({
        type: 'positive',
        message: 'Booking success'
      })
    },
    async getHotels () {
      Loading.show({
        spinner: QSpinnerPuff
      })
      const h = await axios.get('http://localhost:8080/api/guest/hotels', {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      this.hotels = h.data
      Loading.hide()
    },
    async getRoomTypes (id) {
      Loading.show({
        spinner: QSpinnerPuff
      })
      const h = await axios.get(`http://localhost:8080/api/guest/roomTypes?hotel=${id}`, {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      this.roomTypes = h.data
      Loading.hide()
    },
    async getRooms (id) {
      Loading.show({
        spinner: QSpinnerPuff
      })
      const h = await axios.get(`http://localhost:8080/api/guest/rooms?roomType=${id}`, {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${localStorage.getItem('token')}`
        }
      })
      this.rooms = h.data
      Loading.hide()
    }
  },
  mounted () {
    if (localStorage.getItem('role') === 'GUEST') {
      this.getHotels()
    }
  },
  watch: {
    hotel (val) {
      this.getRoomTypes(val.value)
    },
    roomType (val) {
      this.getRooms(val.value)
    }
  },
  computed: {
    hotelsArray () {
      const arr = []
      this.hotels.forEach(h => {
        arr.push({
          value: h.id,
          label: h.name
        })
      })
      return arr
    },
    roomTypesArr () {
      const arr = []
      if (this.roomTypes) {
        this.roomTypes.forEach(h => {
          arr.push({
            value: h.id,
            label: h.name
          })
        })
      }
      return arr
    }
  }
}
</script>

<style lang="scss">
.main-search {
  width: 100%;
  justify-content: center;
  padding: 50px 0;
}
</style>
