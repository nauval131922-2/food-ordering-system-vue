<template lang="">
  <NavBar :name="userName" :role="roleId" />

  <div class="container">
    <h2 class="my-5">Product List</h2>

    <RouterLink to="/product/create" class="btn btn-primary mb-3"
      >Create Product</RouterLink
    >

    <table class="table table-striped">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Name</th>
          <th scope="col">Price</th>
          <th scope="col">Image</th>
          <th scope="col">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in items" :key="index">
          <td>{{ index + 1 }}</td>
          <td>{{ item.name }}</td>
          <td>{{ `Rp. ${item.price}` }}</td>
          <td>
            <img
              :src="url + item.image"
              alt="item image"
              style="width: 100px; height: 100px"
              class="object-fit-cover"
            />
          </td>
          <td>
            <RouterLink
              :to="{ name: 'productId', params: { productId: item.id } }"
            >
              Update</RouterLink
            >
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
import NavBar from "@/components/NavBar.vue";
import axios from "axios";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      userName: "",
      roleId: "",
      items: [],
      url: "http://127.0.0.1:8000/storage/items/",
    };
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName) {
      this.$router.push({ name: "login" });
    }

    if (this.roleId != 4) {
      this.$router.push({ name: "home" });
    }

    this.getItems();
  },
  methods: {
    getItems() {
      axios
        .get("http://127.0.0.1:8000/api/item", {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          this.items = response.data.data;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
};
</script>
<style lang=""></style>
