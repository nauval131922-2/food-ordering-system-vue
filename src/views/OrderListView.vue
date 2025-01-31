<template lang="">
  <div>
    <NavBar :name="userName" :role="roleId" />

    <div class="container">
      <h2 class="my-5">Order List</h2>

      <table class="table table-striped">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Customer Name</th>
            <th scope="col">Table No.</th>
            <th scope="col">Order Date</th>
            <th scope="col">Time</th>
            <th scope="col">Total</th>
            <th scope="col">Status</th>
            <th scope="col">Waitress</th>
            <th scope="col">Cashier</th>
            <th scope="col">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(order, index) in orders" :key="order.id">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ order.customer_name }}</td>
            <td>{{ order.table_no }}</td>
            <td>{{ order.order_date }}</td>
            <td>{{ order.order_time }}</td>
            <td>{{ order.total }}</td>
            <td>{{ order.status }}</td>
            <td>{{ order.waitress.name }}</td>
            <td>{{ order.cashier ? order.cashier.name : "-" }}</td>
            <td>
              <RouterLink
                :to="{ name: 'orderDetail', params: { orderId: order.id } }"
              >
                Detail</RouterLink
              >
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script>
import NavBar from "@/components/NavBar.vue";
import axios from "axios";
import router from "@/router";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      userName: "",
      roleId: "",
      orders: [],
    };
  },
  mounted() {
    this.userName = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");

    if (!this.userName) {
      router.push({ name: "login" });
    }

    this.getOrders();
  },
  methods: {
    getOrders() {
      axios
        .get("http://127.0.0.1:8000/api/order", {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          this.orders = response.data.data;
        })
        .catch(function (error) {
          if (error.response.status == 401) {
            localStorage.removeItem("token");
            localStorage.removeItem("email");
            localStorage.removeItem("name");
            localStorage.removeItem("role_id");

            this.$router.push({ name: "login" });
          }

          console.log(error);
        });
    },
  },
};
</script>
<style lang=""></style>
