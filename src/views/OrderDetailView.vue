<template lang="">
  <div>
    <NavBar :name="userName" :role="roleId" />

    <div class="container">
      <h2 class="my-5">Order Detail</h2>

      <div class="tale-responseive">
        <table class="table table-bordered">
          <tbody>
            <tr>
              <td>Customer Name: {{ order.customer_name }}</td>
              <td>Table No.: {{ order.table_no }}</td>
              <td>Order Date: {{ order.order_date }}</td>
              <td>Status: {{ order.status }}</td>
            </tr>
            <tr>
              <td>
                Waitress: {{ order.waitress ? order.waitress.name : "-" }}
              </td>
              <td>Cashier: {{ order.cashier ? order.cashier.name : "-" }}</td>
              <td>Order Time: {{ order.order_time }}</td>
              <td>Grand Total: Rp. {{ order.total }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <hr />
      <table class="table table-striped">
        <thead>
          <tr>
            <td>#</td>
            <td>Item Name</td>
            <td>Price</td>
            <td>Qty</td>
            <td>Total</td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in order.order_detail" :key="index">
            <td>{{ index + 1 }}</td>
            <td>{{ item.item.name }}</td>
            <td>Rp. {{ item.price }}</td>
            <td>{{ item.qty }}</td>
            <td>Rp. {{ item.price * item.qty }}</td>
          </tr>
        </tbody>
      </table>

      <div>
          <!-- done if user is chef and order.status == ordered -->
        <button v-if="(order.status == 'ordered') && (roleId == 2)" class="btn btn-primary form-control" @click="doneOrder(order.id)">Done</button>
        <!-- paid if user is cashier or manager and order.status == done -->
        <button v-if="(order.status == 'done') && (roleId == 3 || roleId == 4)" class="btn btn-success form-control" @click="paidOrder(order.id)">Paid</button>
      </div>
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
            order: "",
        };
    },
    mounted() {
        this.userName = localStorage.getItem("name");
        this.roleId = localStorage.getItem("role_id");

        if (!this.userName) {
            router.push({ name: "login" });
        }

        this.getOrder();
    },
    methods: {
        getOrder() {
            axios
                .get("http://127.0.0.1:8000/api/order/" + this.$route.params.orderId, {
                    headers: {
                        Authorization: "Bearer " + localStorage.getItem("token"),
                    },
                })
                .then((response) => {
                    this.order = response.data.data;
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
        doneOrder(orderId) {
            axios
                .get("http://127.0.0.1:8000/api/order/" + orderId + "/set-as-done", {
                    headers: {
                        Authorization: "Bearer " + localStorage.getItem("token"),
                    },
                })
                .then((response) => {
                    if (response.status == 200) {
                        alert("Order has been done");
                    }

                    this.order = response.data.data;
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
        paidOrder(orderId) {
            axios
                .get("http://127.0.0.1:8000/api/order/" + orderId + "/payment", {
                    headers: {
                        Authorization: "Bearer " + localStorage.getItem("token"),
                    },
                })
                .then((response) => {
                    if (response.status == 200) {
                        alert("Order has been paid");
                    }

                    this.order = response.data.data;
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
