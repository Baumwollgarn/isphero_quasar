<template>
  <q-page padding>
    <h2>Languages</h2>
    <q-select
      v-model="selected"
      :options="languages"
      label="Select a language"
      stack-label
      use-input
      class="q-ma-lg"
    />
    <q-btn @click="selected = ''">Clear</q-btn>
    <q-btn
      color="primary"
      label="Translate"
      class="q-ml-sm"
      :disable="selected === ''"
      @click="addLanguage"
    />
  </q-page>
</template>

<script>
import {QSpinnerIos} from "quasar";

export default {
  name: "Languages",
  data() {
    return {
      selected: '',
      languages: [
        {label: 'English', value: 'en'},
        {label: 'Français', value: 'fr'},
        {label: 'Español', value: 'es'},
        {label: 'Deutsch', value: 'de'},
        {label: 'Italiano', value: 'it'},
        {label: 'Nederlands', value: 'nl'},
        {label: 'Polski', value: 'pl'},
        {label: 'Português', value: 'pt'},
        {label: 'Русский', value: 'ru'},
        {label: 'Svenska', value: 'sv'},
        {label: 'Türkçe', value: 'tr'},
        {label: '中文', value: 'zh'},
        {label: '日本語', value: 'jp'},
        {label: '한국어', value: 'ko'},
        {label: 'العربية', value: 'ar'},
        {label: 'हिन्दी', value: 'hi'},
        {label: 'বাংলা', value: 'bn'},
        {label: 'हिन्दी', value: 'hi'},

      ],
      texts: [],
      textsTranslated: [],
      messageCount: 0,
    }
  },
  methods: {
    async addLanguage() {
      let int = await this.$axios.post('http://localhost:8000/language', {
        body: {
          code: this.selected.value,
          name: this.selected.label
        }
      })
        .then(async (response) => {
          this.$q.notify({
            message: response.data.message,
            color: 'green-5',
            textColor: 'white',
            icon: 'check_circle',
            position: 'bottom'
          })
          await this.translateLanguage(this.selected.value)
        })
        .catch((error) => {
          console.log(error)
          this.$q.notify({
            message: error.response.data.message,
            color: 'red-4',
            textColor: 'white',
            icon: 'error',
            position: 'bottom'
          })
        })
      this.selected = ''
    },
    async translateLanguage(code) {
      let promiseArray = []
      let textAxios = await this.$axios.get('http://localhost:8000/texts')
        .then(async (response) => {
          this.texts = response.data
          this.$q.loading.show({
            message: `Translating ${this.messageCount++} / ${this.texts.length} , please wait...`,
            spinnerSize: 150,
            spinner: QSpinnerIos,
            backgroundColor: 'blue-7',
            spinnerColor: 'orange-7',
          })
        })
        .then(async () => {
          this.texts.forEach((text) => {
            promiseArray.push(this.$axios.post('https://theteacher.codiblau.com/piano/nologin/google/translate', {
                languageFrom: 'en',
                languageTo: code,
                text: text.translate
            }))
          })
        })
        .then(()=> {
          Promise.all(promiseArray)
            .then((values) => {
            values.forEach((value, index) => {
              this.textsTranslated.push({
                translate: value.data,
                lang_code: code,
                key_text_id: index + 1
              })
            })
          })
            .then(() => {
              this.textsTranslated.forEach((text) => {
                this.$axios.post('http://localhost:8000/text', {
                    lang_code: text.lang_code,
                    key_text_id: text.key_text_id,
                    translate: text.translate
                  },
                  {
                    headers: {
                      'Content-Type': 'application/json'
                    }
                  })
                  .then(async (response) => {
                    if (response.status === 200 && this.messageCount < this.texts.length) this.messageCount++
                    this.$q.loading.show({
                      message: `Translating ${this.messageCount} / ${this.texts.length} , please wait...`,
                      spinnerSize: 150,
                      spinner: QSpinnerIos,
                      backgroundColor: 'blue-7',
                      spinnerColor: 'orange-7',
                    })
                  })
                  .then(() => {
                    if (this.messageCount === this.texts.length) {
                      this.$q.loading.hide()
                      this.$q.notify({
                        message: 'All texts translated',
                        color: 'green-5',
                        textColor: 'white',
                        icon: 'check_circle',
                        position: 'bottom'
                      })
                    }
                  })
                  .catch((error) => {
                    console.log(error)
                    this.$q.notify({
                      message: error.response.data.message,
                      color: 'red-4',
                      textColor: 'white',
                      icon: 'error',
                      position: 'bottom'
                    })
                  })
              })
            })

        })

    }
  }
}
</script>

<style scoped>

</style>
