<template lang="">
  <div></div>
</template>
<script>
import router from "@/router";

export default {
  data() {
    return {
      userName: "",
    };
  },

  mounted() {
    this.userName = localStorage.getItem("name");
    if (!this.userName) {
      this.$router.push({ name: "login" });
    }
  },
};
</script>
<style lang=""></style>
