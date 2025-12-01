<template>
  <div id="dispatcher">

    <div id="orderList">
      <div v-for="(order, key) in orders" :key="key">
        <strong>#{{ key }}</strong><br>
        {{ order.orderItems.join(", ") }}<br>
        {{ order.customer.fullname }}
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
