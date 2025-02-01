<template lang="">
  <!-- navbar component -->
  <NavBar :name="userName" :role="roleId" />

  <div class="container">
    <h2 class="my-5">Hi, {{ userName }}</h2>
  </div>
</template>
<script>
import router from "@/router";
import NavBar from "@/components/NavBar.vue";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      userName: "",
      roleId: "",
    };
  },

  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");
    if (!this.userName) {
      this.$router.push({ name: "login" });
    }
  },
};
</script>
<style lang=""></style>
