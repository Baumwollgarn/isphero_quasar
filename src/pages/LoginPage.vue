<template>
  <q-layout class="hHh lpR fFf">
    <q-page-container>
      <q-page class="flex flex-center">
        <q-card class="bg-grey-2 q-pa-md">
          <q-card-section>
            <div class="text-h5">Login</div>
          </q-card-section>
          <q-card-section>
            <q-form>
              <q-input
                v-model="username"
                label="Username"
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Username is required']"
              />
              <q-input
                v-model="password"
                label="Password"
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Password is required']"
                type="password"
              />
              <q-btn
                label="Login"
                color="primary"
                @click="login"
              />
            </q-form>
          </q-card-section>
        </q-card>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent } from 'vue'
import {QSpinnerFacebook} from "quasar";

export default defineComponent({
  name: 'App',
  components: {},
  data() {
    return {
      username: '',
      password: '',
    }
  },
  methods: {
    showLoading(){
      this.$q.loading.show({
        message: 'Logging in...',
        spinnerSize: 150,
        spinner: QSpinnerFacebook,
        backgroundColor: 'blue-7',
        spinnerColor: 'orange-7',
      })
    },
    login() {
      this.showLoading()
      fetch(process.env.LOGIN + '/api/login', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          username: this.username,
          password: this.password
        })
      })
        .then(res => res.text())
        .then(res => {
          if (res.includes('admin')) {
            this.$q.loading.hide()
            this.$q.notify({
              message: 'Login successful',
              color: 'positive',
              position: 'bottom',
              timeout: 3500
            })
            localStorage.setItem('token', this.username)
            console.log(localStorage.getItem('token'))
            this.$router.push('/home')
          } else if (res.includes('user')) {
            this.$q.loading.hide()
            this.$q.notify({
              message: res,
              color: 'negative',
              position: 'bottom',
              timeout: 3500
            })
          } else {
            this.$q.loading.hide()
            this.$q.notify({
              message: res,
              color: 'negative',
              position: 'bottom',
              timeout: 3500
            })
          }
        })
    }
  }
})
</script>
