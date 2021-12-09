<template>
  <q-layout view="hHh lpR fFf">
    <q-header class="bg-white text-black">
      <q-toolbar style="padding: 10px 30px">
        <q-toolbar-title style="font-size: 28px">
          <router-link class="text-primary text-bold" to="/" style="text-decoration: none">Hotel Management</router-link>
        </q-toolbar-title>
        <q-btn to="/" push no-caps class="bg-black text-white def-btn" label="Main Page" />
        <q-btn to="/login" push no-caps class="bg-black text-white def-btn" label="Login" />
        <q-btn to="register" push no-caps label="Register" class="def-btn"/>
        <q-btn @click="logout" push no-caps label="Logout" class="def-btn"/>
      </q-toolbar>
    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>

  </q-layout>
</template>

<script>
import { Notify } from 'quasar'

export default {
  name: 'MainLayout',
  data () {
    return {
      // token: localStorage.getItem('token')
    }
  },
  computed: {
    token () {
      return localStorage.getItem('token')
    }
  },
  methods: {
    logout () {
      localStorage.removeItem('token')
      localStorage.removeItem('role')
      this.$router.push('/')
      Notify.create({
        type: 'positive',
        message: 'Logout success'
      })
    }
  },
  mounted () {
  }
}
</script>

<style lang="scss">
.def-btn {
  border-radius: 5px;
  margin-right: 10px;
  font-size: 16px;
}
</style>
