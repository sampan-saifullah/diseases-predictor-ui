<template>
  <div>
    <v-row>
      <v-col class="d-flex" cols="12">
        <v-card class="mx-auto" width="100%">
          <v-card-text>
            <div>Login Form</div>
            <v-text-field
              label="Email"
              v-model="email"
              hide-details="auto"
            ></v-text-field>
            <v-text-field
              label="Password"
              v-model="password"
              type="password"
            ></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn
              text
              block
              color="teal accent-4"
              @click="goLogin"
              :disabled="!email || !password"
              :loading="loading"
            >
              Login
            </v-btn>
            {{ msg }}
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: "",
      password: "",
      loading: false,
      msg: "",
    };
  },
  methods: {
    goLogin() {
      this.loading = true;
      const that = this;
      this.$axios
        .post("/login", {
          email: this.email,
          password: this.password,
        })
        .then(function (response) {
          that.loading = false;
          that.msg = response.data.msg;
          console.log(response.data);
          if (that.msg === "Success") {
            that.$router.push("/create");
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
};
</script>

<style>
</style>