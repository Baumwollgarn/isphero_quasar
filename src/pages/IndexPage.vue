<template>
  <q-page class="flex flex-center">
    <img
      alt="Quasar logo"
      src="https://cdnisphero.blob.core.windows.net/cdn/logo.png"
      style="width: 200px; height: 200px"
    >
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import Chatwindow from "components/Chatwindow.vue";

export default defineComponent({
  name: 'IndexPage',
  components: {Chatwindow}
})
</script>
