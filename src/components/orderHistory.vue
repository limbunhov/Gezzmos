<template>
  <div class="history">
    <h1>Order History</h1>
    <table class="table">
      <thead>
        <tr>
          <th>Order ID</th>
          <th>Product</th>
          <th>Quantity</th>
          <th>Total Price</th>
          <th>Order Date</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="order in orderedItems" :key="order._id">
          <td>{{ order.orderID }}</td>
          <td>{{ order.product.name }}</td>
          <td>{{ order.quantity }}</td>
          <td>{{ order.totalPrice }}</td>
          <td>{{ formatDate(order.orderDate) }}</td>
          <td>{{ order.status }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      orderedItems: [],
    };
  },
  created() {
    this.fetchOrderedHistory();
  },
  methods: {
    async fetchOrderedHistory() {
      try {
        const userId = localStorage.getItem('userId');
        const response = await axios.get(`https://colorful-dog-teddy.cyclic.app/orders/${userId}`);
        this.orderedItems = response.data;
      } catch (error) {
        console.error('Error fetching ordered history:', error);
      }
    },
    formatDate(dateString) {
      return new Date(dateString).toLocaleDateString();
    },
  },
};
</script>
