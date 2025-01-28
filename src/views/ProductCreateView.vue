<template lang="">
  <NavBar :name="userName" :role="roleId" />

  <div class="container mt-5">
    <h3 class="mb-3">Create Product</h3>

    <form @submit.prevent="createProduct" enctype="multipart/form-data">
      <div class="mb-3">
        <label for="name" class="form-label">Name</label>
        <input type="text" class="form-control" id="name" v-model="name" />
      </div>
      <div class="mb-3">
        <label for="price" class="form-label">Price</label>
        <input type="number" class="form-control" id="price" v-model="price" />
      </div>
      <div class="mb-3">
        <label for="image" class="form-label">Image</label>
        <input
          type="file"
          class="form-control"
          id="image"
          @change="onFileChange($event)"
        />
      </div>
      <button type="submit" class="btn btn-primary">Submit</button>
    </form>
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
      name: "",
      price: "",
      file: "",
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
  },
  methods: {
    createProduct() {
      if (!this.name || !this.price) {
        alert("Please fill all fields");
        return;
      }

      let formData = new FormData();
      formData.append("name", this.name);
      formData.append("price", this.price);
      formData.append("image_file", this.file);

      axios
        .post("http://127.0.0.1:8000/api/item", formData, {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          this.$router.push({ name: "product" });
        })
        .catch(function (error) {
            console.log("Error response:", error.response.data);
            alert(error.response.data.message || "An error occurred");
        });
    },
    onFileChange(e) {
      let file = e.target.files[0];
      this.file = file;
    },
  },
};
</script>
<style lang=""></style>
