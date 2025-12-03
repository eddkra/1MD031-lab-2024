<template>
  <div class="burger-item">

    <h3>{{ burger.name }}</h3>
    <img :src="burger.url" :alt="burger.name">

    <ul>
      <li>{{ burger.kCal }} kCal</li>
      <li v-if="burger.lactose"><strong>Contains lactose</strong></li>
      <li v-if="burger.gluten"><strong>Contains gluten</strong></li>
    </ul>

    <div class="burger-amount">
      <button @click="decrease">-</button>
      <span>{{ amount }}</span>
      <button @click="increase">+</button>
    </div>

  </div>
</template>

<script>
export default {
  name: "OneBurger",
  props: {
    burger: Object
  },
  data() {
    return {
      amount: 0
    }
  },
  methods: {
    increase() {
      this.amount++
      this.emitUpdate()
    },
    decrease() {
      if (this.amount > 0) {
        this.amount--
        this.emitUpdate()
      }
    },
    emitUpdate() {
      this.$emit("update-amount", {
        name: this.burger.name,
        amount: this.amount
      })
    }
  }
}
</script>

<style scoped>
.burger-item {
  background-color: #e5e5e5;
  border-radius: 6px;
  padding: 12px;
  color: black;
}

.burger-item img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 4px;
}

.burger-amount {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 18px;
  margin-top: 14px;
}

.burger-amount button {
  width: 36px;
  height: 36px;
  background: black;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 20px;
}
</style>
