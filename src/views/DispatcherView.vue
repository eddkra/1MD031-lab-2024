<template>
  <div id="dispatcher">

    <div id="orderList">
      <div 
        v-for="(order, key) in orders" 
        :key="key"
        class="order-card"
      >
        <div class="order-id">Order #{{ key }}</div>

        <div class="order-section">
          <strong>Items:</strong>
          <ul>
            <li v-for="item in order.orderItems" :key="item">{{ item }}</li>
          </ul>
        </div>

        <div class="order-section">
          <strong>Customer:</strong>
          <div>{{ order.customer.fullname }}</div>
          <div>{{ order.customer.email }}</div>
          <div>Payment: {{ order.customer.payment }}</div>
          <div>Gender: {{ order.customer.gender }}</div>
        </div>
      </div>

      <button @click="clearQueue">Clear Queue</button>
    </div>

    <div id="map-area">
      <img id="dispatch-map" src="/img/polacks.jpg">

      <div
        v-for="(order, key) in orders"
        :key="'dot' + key"
        class="dot"
        :style="{ 
          left:  (order.details.xPercent * 1920) + 'px',
          top:   (order.details.yPercent * 1078) + 'px'
        }"
      >
        {{ key }}
      </div>
    </div>

  </div>
</template>

<script>
import io from "socket.io-client"
const socket = io("http://localhost:3000")

export default {
  name: "DispatcherView",

  data() {
    return {
      orders: {}
    }
  },

  created() {
    socket.on("currentQueue", (data) => {
      this.orders = data.orders
    })

    socket.on("orderAdded", (data) => {
      this.orders[data.orderId] = data
    })

    socket.on("queueCleared", () => {
      this.orders = {}
    })
  },

  methods: {
    clearQueue() {
      socket.emit("clearQueue")
    }
  }
}
</script>

<style scoped>
#dispatcher {
  position: relative;
}

#orderList {
  position: absolute;
  top: 1em;
  left: 1em;
  z-index: 10;
  background: rgba(255,255,255,0.9);
  padding: 1em;
  border-radius: 6px;
  font-size: 14px;
  width: 300px;
}

.order-card {
  background: rgba(255,255,255,0.95);
  padding: 1em 1.2em;
  margin-bottom: 1em;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.15);
}

.order-id {
  font-weight: bold;
  font-size: 18px;
  margin-bottom: 0.6em;
}

.order-section {
  margin-bottom: 0.8em;
}

.order-section ul {
  margin: 0.3em 0 0 1.2em;
  padding: 0;
}

#map-area {
  position: relative;
  width: 1920px;
  height: 1078px;
  margin: 0 auto;
}

#dispatch-map {
  width: 1920px;
  height: 1078px;
  display: block;
}

.dot {
  position: absolute;
  width: 22px;
  height: 22px;
  background: black;
  color: white;
  border-radius: 50%;
  text-align: center;
  line-height: 22px;
  font-size: 12px;
  transform: translate(-50%, -50%);
}
</style>
