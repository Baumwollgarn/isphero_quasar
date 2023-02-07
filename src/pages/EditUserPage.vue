<template>
  <q-page padding>
    <q-card>
      <q-card-section>
        <div class="text-h6">Edit User with ID: {{id}}</div>
      </q-card-section>
      <q-card-section>
        <q-form>
          <q-input
            v-model="firstName"
            label="Name"
            filled
            value= firstName
            lazy-rules
            :rules="[val => val.length > 0 || 'Please type a name']"
          />
          <q-input
            v-model="lastName"
            label="Name"
            value= lastName
            filled
            lazy-rules
            :rules="[val => val.length > 0 || 'Please type a name']"
          />
          <q-input
            v-model="email"
            label="E-mail"
            value= email
            filled
            lazy-rules
            :rules="[val => val.length > 0 || 'Please type an e-mail']"
          />
          <q-btn
            label="Save"
            color="primary"
            type="submit"
            @click="saveUser"
          />
        </q-form>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
export default {
  name: "EditUserPage",
  data() {
    return {
      id: this.$route.params.id,
      firstName: "",
      lastName: "",
      email: "",

    }
  },
  methods: {
    async getUser() {
      let id = this.$route.params.id;
      const response = await fetch(`http://isphero.com:1234/user/${id}`, {
        method: "GET",
      })
      let user = await response.json();
      this.firstName = user.user_data.first_name;
      this.lastName = user.user_data.last_name1;
      this.email = user.email;
    },

    async saveUser() {
      let id = this.$route.params.id;
      const response = await fetch(`http://isphero.com:1234/user/${id}`, {
        method: "PUT",
        body: JSON.stringify({
          email: this.email,
        }),
      })
      let user = await response.json();
      this.$router.push("/users");
    }
  },
  mounted() {
    this.getUser();
  }
}
</script>

<style scoped>

</style>
