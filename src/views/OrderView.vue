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
                  <img v-if="item.image"
                    class="card-img-top object-fit-cover"
                    height="250px"
                    :src="url + item.image"
                    alt="Card image cap"
                  />
                  <img v-else
                    class="card-img-top object-fit-cover"
                    height="250px"
                    src="@/assets/images/no-img.jpg"
                    alt="Card image cap"
                  />


                  <div class="card-body text-center">
                    <h5 class="card-title">{{ item.name }}</h5>
                    <p class="card-text">{{ `Rp. ${item.price}` }}</p>
                    <p>
                      <button class="btn btn-success" @click="addToCart(item.id)">
                        Add to Cart
                      </button>
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-12 col-sm-4 mb-3 order-box">
          <h2>Order List</h2>
          <hr />
          <div class="item-box">
            <div v-for="order in orders" class="d-flex justify-content-between">
              <span>{{ order.name }} (x{{ order.qty }})</span>
              <span>{{ `Rp. ${order.price}` }}</span>
            </div>
          </div>
          <hr />
          <div class="total-box">
            <div class="d-flex justify-content-between">
              <span>Total</span>
              <span>{{ `Rp. ${orders.reduce((acc, order) => acc + order.price, 0)}` }}</span>
            </div>
          </div>
        </div>
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
      orders: [],
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
    addToCart(id) {
      let item = this.filterredItems.filter((item) => item.id == id)[0];
      let orderItem = Object.assign({}, item);

      let indexOfArrayItem = this.orders.map(e => e.id).indexOf(orderItem.id);

      if (indexOfArrayItem != -1) { //jika item sudah ada di orders, maka qty + 1
        this.orders[indexOfArrayItem].qty++;
        // harga total = harga * qty
        this.orders[indexOfArrayItem].price = this.orders[indexOfArrayItem].price * this.orders[indexOfArrayItem].qty;
      } else {
        orderItem.qty = 1;
        this.orders.push(orderItem);
        // harga total = harga * qty
        orderItem.price = orderItem.price * orderItem.qty;
      }
    },
  },
};
</script>
<style>
.order-box {
  border-left: 1px solid #ccc;
}

.item-box {
  font-size: 24px;
}

.total-box {
  font-size: 24px;
  font-weight: bold;
}
</style>
