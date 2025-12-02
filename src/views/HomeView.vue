<template>
  <div>

    <header>
      <span>Welcome to Burgirburgir Online</span>
    </header>

    <main>

      <section id="burger-section">
        <h2>Select burger</h2>
        <p>This is where you execute burger selection</p>

        <div class="burger-grid">
          <div
            class="burger-item"
            v-for="(burger, index) in burgers"
            :key="index"
          >
            <h3>{{ burger.name }}</h3>
            <img :src="burger.url" :alt="burger.name">
            <ul>
              <li>{{ burger.kCal }} kCal</li>
              <li v-if="burger.lactose"><strong>Contains lactose</strong></li>
              <li v-if="burger.gluten"><strong>Contains gluten</strong></li>
            </ul>
          </div>
        </div>
      </section>

      <section id="customer-section">
        <h2>Customer information</h2>

        <form>

          <p>
            <label>Full name</label>
            <input type="text" v-model="customer.fullname">
          </p>

          <p>
            <label>Email</label>
            <input type="email" v-model="customer.email">
          </p>

          <p>
            <label>Payment options</label>
            <select v-model="customer.payment">
              <option>Credit card</option>
              <option>Swish</option>
              <option>PayPal</option>
              <option>Klarna</option>
            </select>
          </p>

          <div id="gender-container">
            <div class="gender-title">Gender</div>

            <div class="gender-row">
              <input type="radio" value="male" v-model="customer.gender">
              <label>Male</label>
            </div>

            <div class="gender-row">
              <input type="radio" value="female" v-model="customer.gender">
              <label>Female</label>
            </div>

            <div class="gender-row">
              <input type="radio" value="nonbinary" v-model="customer.gender">
              <label>Non-binary</label>
            </div>

            <div class="gender-row">
              <input type="radio" value="undisclosed" v-model="customer.gender">
              <label>Undisclosed</label>
            </div>
          </div>

        </form>
      </section>

      <section id="map-section">
        <h2>Select delivery location</h2>

        <div id="map-container">
          <div id="map-wrapper">
            <img id="map-image" src="/img/polacks.jpg" @click="setLocation">

            <div
              v-if="location"
              id="marker"
              :style="{ left: location.x + 'px', top: location.y + 'px' }"
            >
              T
            </div>
          </div>
        </div>
      </section>

    </main>

    <button @click="placeOrder">
      <img
        src="https://cdn-icons-png.flaticon.com/512/4297/4297679.png"
        style="width: 20px; filter: invert(1); margin-right: 6px;"
      >
      Place my order!
    </button>

    <footer>© 2025 Burgirburgir</footer>

  </div>
</template>

<script>
import io from "socket.io-client"
import menuData from "@/assets/menu.json"

const socket = io("http://localhost:3000")

export default {
  data() {
    return {
      location: null,
      customer: {
        fullname: "",
        email: "",
        payment: "Credit card",
        gender: "male"
      },
      burgers: menuData
    }
  },

  methods: {
    getOrderNumber() {
      return Math.floor(1000 + Math.random() * 100000)
    },

    setLocation(event) {
      const rect = event.target.getBoundingClientRect()
      const clickX = event.clientX - rect.left
      const clickY = event.clientY - rect.top

      this.location = {
        x: Math.round(clickX),
        y: Math.round(clickY),
        xPercent: clickX / rect.width,
        yPercent: clickY / rect.height
      }
    },

    placeOrder() {
      if (!this.location) {
        alert("Click the map to choose a delivery location!")
        return
      }

      const orderId = this.getOrderNumber()

      socket.emit("addOrder", {
        orderId: orderId,
        orderItems: this.burgers.map(b => b.name),
        customer: this.customer,
        details: {
          xPercent: this.location.xPercent,
          yPercent: this.location.yPercent
        }
      })

      alert("Order placed!")
    }
  }
}
</script>
