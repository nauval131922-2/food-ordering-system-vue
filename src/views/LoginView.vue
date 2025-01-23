<template lang="">
  <div
    class="container d-flex justify-content-center align-items-center"
    style="height: 100vh"
  >
    <div class="card shadow-sm p-4" style="width: 100%; max-width: 400px">
      <h3 class="text-center mb-4">Login</h3>
      <form @submit.prevent>
        <div class="mb-3">
          <label for="email" class="form-label">Email address</label>
          <input
            type="email"
            v-model="email"
            class="form-control"
            id="email"
            placeholder="Enter your email"
            required
          />
        </div>
        <div class="mb-3">
          <label for="password" class="form-label">Password</label>
          <input
            type="password"
            v-model="password"
            class="form-control"
            id="password"
            placeholder="Enter your password"
            required
          />
        </div>
        <button @click="login()" type="submit" class="btn btn-primary w-100">
          Login
        </button>
      </form>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import router from "@/router";

export default {
  data() {
    return {
      email: "",
      password: "",
    };
  },
  methods: {
    login() {
      axios
        .post("http://127.0.0.1:8000/api/auth/login", {
          email: this.email,
          password: this.password,
        })
        .then(function (response) {
          console.log(response);
          localStorage.setItem("email", response.data.data.email);
          localStorage.setItem("name", response.data.data.name);
          localStorage.setItem("role_id", response.data.data.role_id);
          localStorage.setItem("token", response.data.data.token);

          router.push({ name: "home" });
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    if (this.userName) {
      this.$router.push({ name: "home" });
    }
  },
};
</script>
<style lang=""></style>
