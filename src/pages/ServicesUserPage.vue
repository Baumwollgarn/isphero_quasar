<template>
  <q-page-container>
    <q-card>
      <q-card-section>
        <div class="text-h6">Services</div>
      </q-card-section>
    </q-card>
  </q-page-container>
</template>

<script>
export default {
  name: "ServicesUserPage",
  data() {
    return {
      services: [],
      loading: false,
    };
  },
  methods: {
    getServices() {
      let id = this.$route.params.id;
      this.loading = true;
      this.$axios
        .get(process.env.API + "/user/" + id + "/services")
        .then((response) => {
          this.services = response.data;
          this.loading = false;
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    this.getServices();
  },
}
</script>

<style scoped>

</style>
