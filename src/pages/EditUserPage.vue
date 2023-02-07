<template>
  <q-page padding>
    <q-card>
      <q-card-section>
        <div class="text-h6">Edit User with ID: {{id}}</div>
      </q-card-section>
      <q-card-section>
        <q-form>
          <!-- Form with 2 columns -->
          <div class="row justify-evenly">
            <div class="col-12 col-md-5">
              <q-input
                v-model="username"
                label="Username"
                value= username
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Please type a username']">
                <template v-slot:before>
                  <q-icon name="account_circle" />
                </template>
              </q-input>
              <q-input
                v-model="firstName"
                label="Name"
                filled
                value= firstName
                lazy-rules
                :rules="[val => val.length > 0 || 'Please type a name']">
                <template v-slot:before>
                  <q-icon name="person" />
                </template>
              </q-input>
              <q-input
                v-model="lastName"
                label="Last Name"
                value= lastName
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Please type a name']">
                <template v-slot:before>
                  <q-icon name="person_outline" />
                </template>
              </q-input>
              <q-input
                v-model="email"
                label="E-mail"
                value= email
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Please type an e-mail']">
                <template v-slot:before>
                  <q-icon name="mail" />
                </template>
              </q-input>
            </div>
            <div class=" col-12 col-md-5">
              <q-input
                v-model="address"
                label="Address"
                value= address
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Please type an address']">
                <template v-slot:before>
                  <q-icon name="home" />
                </template>
              </q-input>
              <q-input
                v-model="city"
                label="City"
                value= city
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Please type a city']">
                <template v-slot:before>
                  <q-icon name="location_city" />
                </template>
              </q-input>
              <q-input
                v-model="postCode"
                label="Post Code"
                value= postCode
                filled
                lazy-rules
                :rules="[val => val.length > 0 || 'Please enter a post code']">
                <template v-slot:before>
                  <q-icon name="tag" />
                </template>
              </q-input>
              <q-select
                v-model="role"
                label="Role"
                filled
                :options="['ADMIN', 'USER']"
                lazy-rules
                :rules="[val => val.length > 0 || 'Please select a role']">
                <template v-slot:before>
                  <q-icon name="admin_panel_settings" />
                </template>
              </q-select>
            </div>
          </div>
          <q-btn
            label="Save"
            color="primary"
            type="submit"
            @click="saveUser"
            class="self-center"
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
      verified: false,
      address: "",
      city: "",
      country: "",
      postCode: "",
      username: "",
      role: "",
    }
  },
  methods: {
    async getUser() {
      let id = this.$route.params.id;
      const response = await fetch(`http://isphero.com:1234/user/${id}`, {
        method: "GET",
      })
      let user = await response.json();
      this.firstName = user.user_data ? user.user_data.first_name : "";
      this.lastName = user.user_data ? user.user_data.last_name : "";
      this.email = user.email;
      this.verified = user.verified;
      this.address = user.user_data ? user.user_data.address : "";
      this.city = user.user_data ? user.user_data.city : "";
      this.country = user.user_data ? user.user_data.country : "";
      this.username = user.username;
      this.role = user.role;
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
    },

  },
  mounted() {
    this.getUser();
  }
}
</script>

<style scoped>

</style>
