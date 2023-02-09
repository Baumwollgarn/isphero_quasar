<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          ISP Hero - Admin Panel
        </q-toolbar-title>

        <q-btn
          flat
          dense
          round
          icon="logout"
          aria-label="Logout"
          @click="logout"
        />
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
    >
      <q-list>
        <q-item-label
          header
        >
          Management
        </q-item-label>

        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
import EssentialLink from 'components/EssentialLink.vue'

const linksList = [
  {
    title: 'Dashboard',
    caption: 'Overview of environment',
    icon: 'dashboard',
    link: '#/home/dashboard'
  },
  {
    title: 'Users',
    caption: 'Manage users',
    icon: 'people',
    link: '#/home/users'
  },
  {
    title: 'Newsletter',
    caption: 'People who want to receive emails',
    icon: 'email',
    link: '#/home/newsletter'
  }
]

export default defineComponent({
  name: 'MainLayout',

  components: {
    EssentialLink
  },
  methods: {
    logout() {
      localStorage.removeItem('token')
      this.$router.push('/')
    }
  },
  async mounted() {
    let token = localStorage.getItem('token')
    let verifyToken = await fetch(process.env.LOGIN + '/auth/verify', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        token: token
      })
    })
    let response = await verifyToken.text()
    if (response.includes('false')) {
      localStorage.removeItem('token')
      this.$router.push('/')
    }
  },

  setup () {
    const leftDrawerOpen = ref(false)

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      }
    }
  }
})
</script>
