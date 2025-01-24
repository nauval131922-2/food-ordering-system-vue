<template lang="">
  <div>
    <NavBar :name="userName" :role="roleId" />

    <div class="container-fluid mt-5">
      <div class="row">
        <div class="col-12 col-sm-8 mb-3">
          <!-- search box -->
          <div class="col-12">
            <input
              type="text"
              v-model="keyword"
              class="form-control"
              placeholder="Search"
              :onchange="searchItem()"
            />
          </div>

          <hr />

          <!-- item list box -->
          <div class="col-12">
            <div class="row">
              <div
                v-for="item in filterredItems"
                :key="item.id"
                class="col-12 col-sm-6 col-md-4 col-lg-3 mb-3"
              >
                <div class="card">
                  <img
                    class="card-img-top object-fit-cover"
                    height="250px"
                    :src="url + item.image"
                    alt="Card image cap"
                  />
                  <div class="card-body text-center">
                    <h5 class="card-title">{{ item.name }}</h5>
                    <p class="card-text">{{ `Rp. ${item.price}` }}</p>
                    <p>
                      <button class="btn btn-success">Order</button>
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 col-sm-4 mb-3 bordered">Ordered Item</div>
      </div>
    </div>
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
      filterredItems: [],
      keyword: "",
      url: "http://127.0.0.1:8000/storage/items/",
    };
  },

  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");
    if (!this.userName) {
      this.$router.push({ name: "login" });
    }
    if (this.roleId != 4 && this.roleId != 1) {
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
    searchItem() {
      this.filterredItems = this.items.filter((item) => {
        return item.name.toLowerCase().includes(this.keyword.toLowerCase());
      });
    },
  },
};
</script>
<style>
.bordered {
  border: 1px solid black;
}
</style>
