<template lang="">
    <NavBar :name="userName" :role="roleId" />

    <div class="container mt-5">
    <h3 class="mb-3">Edit Product</h3>

    <form @submit.prevent="updateProduct" enctype="multipart/form-data">
      <div class="mb-3">
        <label for="name" class="form-label">Name</label>
        <input type="text" class="form-control" id="name" v-model="item.name" />
      </div>
      <div class="mb-3">
        <label for="price" class="form-label">Price</label>
        <input type="number" class="form-control" id="price" v-model="item.price" />
      </div>
      <div class="mb-3">
        <label for="price" class="form-label">Current Image</label><br>
        <img v-if="item.image" :src=url+item.image alt="item image" style="width: 100px; height: 100px" class="object-fit-cover"/>
        <img v-else="item.image" src="@/assets/images/no-img.jpg" alt="item image" style="width: 100px; height: 100px" class="object-fit-cover"/>
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
      <button type="submit" class="btn btn-primary">Update</button>
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
            url: "http://127.0.0.1:8000/storage/items/",
            productId: "",
            item: '',
            file: "",
        };
    },
    mounted() {
        this.productId = this.$route.params.productId;
        this.userName = localStorage.getItem("name");
        this.roleId = localStorage.getItem("role_id");

        if (!this.userName) {
            this.$router.push({ name: "login" });
        }

        if (this.roleId != 4) {
            this.$router.push({ name: "home" });
        }

        this.getItem();
    },
    methods: {
        getItem() {
            axios
                .get("http://127.0.0.1:8000/api/item/" + this.productId, {
                    headers: {
                        Authorization: "Bearer " + localStorage.getItem("token"),
                    },
                })
                .then((response) => {
                    this.item = response.data.data;
                })
                .catch(function (error) {
                    console.log(error);
                });
        },
        updateProduct() {
            if (!this.item.name || !this.item.price) {
                alert("Please fill all fields");
                return;
            }

            let formData = new FormData();
            formData.append("name", this.item.name);
            formData.append("price", this.item.price);
            formData.append("image_file", this.item.image_file);
            formData.append("_method", "patch");

            axios
                .post("http://127.0.0.1:8000/api/item/" + this.productId, formData, {
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
            this.item.image_file = file;
        },
    }
};
</script>
<style lang="">

</style>