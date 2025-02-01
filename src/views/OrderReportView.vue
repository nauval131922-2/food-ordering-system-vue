<template lang="">
  <div>
    <NavBar :name="userName" :role="roleId" />

    <div class="container">
      <h2 class="my-5">Order Report</h2>

      <!-- select box -->
      <div class="mb-3 w-25">
        <label for="month" class="form-label">Month</label>
        <select
          class="form-select"
          id="month"
          v-model="selectedMonth"
          @change="getReport()"
        >
          <option value="">Select Month</option>
          <option v-for="month in months" :value="month.value">
            {{ month.name }}
          </option>
        </select>
      </div>
      <div class="col-12 mt-5">
        <div class="row">
          <div class="col-12 col-sm-4">
            <div class="box">
              <label>Order Count</label>
              <label>{{ data.orderCount }}</label>
            </div>
          </div>
          <div class="col-12 col-sm-4">
            <div class="box">
              <label>Min Payment</label>
              <label>Rp. {{ data.minPayment }}</label>
            </div>
          </div>
          <div class="col-12 col-sm-4">
            <div class="box">
              <label>Max Payment</label>
              <label>Rp. {{ data.maxPayment }}</label>
            </div>
          </div>
        </div>
      </div>
      <div class="col-12 mt-5">
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
            </tr>
          </thead>
          <tbody>
            <tr v-for="(order, index) in data.orders" :key="order.id">
              <th scope="row">{{ index + 1 }}</th>
              <td>{{ order.customer_name }}</td>
              <td>{{ order.table_no }}</td>
              <td>{{ order.order_date }}</td>
              <td>{{ order.order_time }}</td>
              <td>Rp. {{ order.total }}</td>
              <td>{{ order.status }}</td>
              <td>{{ order.waitress.name }}</td>
              <td>{{ order.cashier ? order.cashier.name : "-" }}</td>
            </tr>
          </tbody>
        </table>
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
            months: [
                { value: 1, name: "January" },
                { value: 2, name: "February" },
                { value: 3, name: "March" },
                { value: 4, name: "April" },
                { value: 5, name: "May" },
                { value: 6, name: "June" },
                { value: 7, name: "July" },
                { value: 8, name: "August" },
                { value: 9, name: "September" },
                { value: 10, name: "October" },
                { value: 11, name: "November" },
                { value: 12, name: "December" },
            ],
            selectedMonth: "",
            data: {
                orderCount: 0,
                minPayment: 0,
                maxPayment: 0,
            }
        };
    },
    mounted() {
        this.userName = localStorage.getItem("name");
        this.roleId = localStorage.getItem("role_id");

        if (!this.userName) {
            router.push({ name: "login" });
        }

        if (this.roleId != 4) {
            router.push({ name: "home" });
        }
    },
    methods: {
        getReport() {
            axios
                .get("http://127.0.0.1:8000/api/order-report?month=" + this.selectedMonth, {
                    headers: {
                        Authorization: "Bearer " + localStorage.getItem("token"),
                    },
                })
                .then((response) => {
                    this.data = response.data.data;
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
<style scoped>
.box {
    background-color: #f8f9fa;
    padding: 10px;
    border-radius: 5px;
}

.box label {
    font-size: 20px;
    display: block;
}
</style>
