<template>
  <div class="app">
    <h1>🎲 Yahtzee Mini Game</h1>

    <div class="balance">
      Balance: <strong>{{ balance }}</strong> coins
    </div>

    <div class="bet">
      <input v-model.number="bet" type="number" min="1" />
      <button @click="roll" :disabled="rolling || bet <= 0">ROLL</button>
    </div>

    <div v-if="rolling" class="rolling">Rolling...</div>

    <div v-if="dice.length" class="dice">
      <span v-for="(d, i) in dice" :key="i" class="die">🎲 {{ d }}</span>
    </div>

    <div v-if="combination">
      <p>Combination: <strong>{{ combination }}</strong></p>
      <p v-if="win > 0">You won: <strong>{{ win }}</strong> coins 🎉</p>
      <p v-else>No win 😢</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue"
import axios from "axios"

const balance = ref(0)
const bet = ref(10)
const dice = ref([])
const rolling = ref(false)
const win = ref(0)
const combination = ref("")

// Завантажуємо баланс при старті
onMounted(async () => {
  const res = await axios.get("http://127.0.0.1:8000/balance")
  balance.value = res.data.balance
})

async function roll() {
  if (bet.value <= 0) return

  rolling.value = true
  dice.value = []
  win.value = 0
  combination.value = ""

  // Виклик бекенду
  const res = await axios.post("http://127.0.0.1:8000/roll", { bet: bet.value })

  setTimeout(() => {
    dice.value = res.data.dice
    combination.value = res.data.combination
    win.value = res.data.win
    balance.value = res.data.balance
    rolling.value = false
  }, 2000) // 2 секунди "анімації"
}
</script>

<style>
.app {
  max-width: 500px;
  margin: auto;
  text-align: center;
  font-family: Arial, sans-serif;
}

.balance {
  margin: 15px 0;
  font-size: 20px;
}

.bet input {
  width: 80px;
  padding: 5px;
  margin-right: 10px;
}

button {
  padding: 6px 12px;
  font-size: 16px;
}

.dice {
  margin-top: 20px;
}

.die {
  font-size: 24px;
  margin: 0 5px;
}

.rolling {
  font-size: 20px;
  color: orange;
}
</style>
