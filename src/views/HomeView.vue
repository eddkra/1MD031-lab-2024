<template>
  <div>

    <header>
      <span>Welcome to Burgirburgir Online</span>
    </header>

    <main>

      <section id="burger-section">
        <h2>Select burger</h2>
        <p>This is where you execute burger selection</p>

        <div 
          class="burger-item"
          v-for="(burger, index) in burgers"
          :key="index"
        >
          <h3>{{ burger.name }}</h3>
          <img :src="burger.img" :alt="burger.name">
          <ul>
            <li>{{ burger.kCal }} kCal</li>
            <li v-if="burger.lactose">Contains lactose</li>
            <li v-if="burger.gluten">Contains gluten</li>
          </ul>
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
            <label>Street</label>
            <input type="text" v-model="customer.street">
          </p>

          <p>
            <label>House</label>
            <input type="number" v-model="customer.house">
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

          <p>
            <label>Gender</label>
            <label><input type="radio" value="male" v-model="customer.gender"> Male</label>
            <label><input type="radio" value="female" v-model="customer.gender"> Female</label>
            <label><input type="radio" value="nonbinary" v-model="customer.gender"> Non-binary</label>
            <label><input type="radio" value="undisclosed" v-model="customer.gender"> Undisclosed</label>
          </p>

        </form>
      </section>

      <section id="map-section">
        <h2>Select delivery location</h2>

        <div id="map-container">
          <div id="map-wrapper">
            <img id="map-image" src="/img/polacks.jpg" @click="setLocation">

            <div v-if="location"
                id="marker"
                :style="{ 
                  left: location.x + 'px', 
                  top: location.y + 'px' 
                }">
            </div>
          </div>
        </div>
      </section>

    </main>

    <button @click="placeOrder">
      <img src="https://cdn-icons-png.flaticon.com/512/4297/4297679.png"
           style="width: 20px; filter: invert(1); margin-right: 6px;">
      Place my order!
    </button>

    <footer>© 2025 Burgirburgir</footer>

  </div>
</template>

<script>
import io from "socket.io-client"
const socket = io("http://localhost:3000")

function MenuItem(name, kCal, lactose, gluten, img) {
  this.name = name
  this.kCal = kCal
  this.lactose = lactose
  this.gluten = gluten
  this.img = img
}

export default {
  data() {
    return {
      location: null,

      customer: {
        fullname: "",
        email: "",
        street: "",
        house: "",
        payment: "Credit card",
        gender: "male"
      },

      burgers: [
        new MenuItem("The Fire Burger", 750, true, true, "/img/fire-burger.jpg"),
        new MenuItem("Fried Turkey Burger", 600, true, true, "/img/turkey-burger.jpg"),
        new MenuItem("A Double w/ Cheese", 1800, true, true, "/img/double-cheese.jpg")
      ]
    }
  },

  methods: {
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

      socket.emit("addOrder", {
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

<style scoped>
#map-wrapper {
  position: relative;
  width: 100%;
  aspect-ratio: 1920 / 1078;
  overflow: hidden;
  border: 2px solid #ccc;
}

#map-image {
  width: 100%;
  height: 100%;
  object-fit: contain;
  cursor: crosshair;
}

#marker {
  position: absolute;
  width: 20px;
  height: 20px;
  background: blue;
  border-radius: 50%;
  z-index: 10;
  transform: translate(-50%, -50%);
}
</style>
