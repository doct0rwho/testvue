<template>
  <div class="app">
    <h1>🎲 Yahtzee Mini Game</h1>

    <div class="balance">
      Balance: <strong>{{ balance }}</strong> coins
    </div>

    <table class="prices">
      <thead>
        <tr>
          <th>Combination</th>
          <th>Multiplier</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Pair</td>
          <td>x1</td>
        </tr>
        <tr>
          <td>4+2</td>
          <td>x2</td>
        </tr>
        <tr>
          <td>Yahtzee</td>
          <td>x3</td>
        </tr>
        <tr>
          <td>Three Pairs</td>
          <td>x4</td>
        </tr>
        <tr>
          <td>Other</td>
          <td>x0</td>
        </tr>
      </tbody>
    </table>

    <div class="bet">
      <input v-model.number="bet" type="number" min="1" />
      <button class="btn-roll" @click="roll" :disabled="rolling || bet <= 0 || bet > balance">ROLL</button>
      <button class="btn-reset" @click="reset" :disabled="rolling">RESET</button>
    </div>

    <div v-if="rolling" class="rolling">Rolling...</div>

    <div v-if="dice.length" class="dice">
      <span v-for="(d, i) in dice" :key="i" class="die">🎲 {{ d }}</span>
    </div>

    <div v-if="combination" class="result">
      <p>
        Combination: <strong>{{ combination }}</strong>
      </p>
      <p v-if="win > 0" class="win">
        You won: <strong>{{ win }}</strong> coins 🎉
      </p>
      <p v-else class="lose">No win 😢</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const balance = ref(0);
const bet = ref(10);
const dice = ref([]);
const rolling = ref(false);
const win = ref(0);
const combination = ref("");

onMounted(async () => {
  const res = await axios.get("http://127.0.0.1:8000/balance");
  balance.value = res.data.balance;
});

async function roll() {
  if (bet.value <= 0) return;
  rolling.value = true;
  dice.value = [];
  win.value = 0;
  combination.value = "";

  const res = await axios.post("http://127.0.0.1:8000/roll", {
    bet: bet.value,
  });

  setTimeout(() => {
    dice.value = res.data.dice;
    combination.value = res.data.combination;
    win.value = res.data.win;
    balance.value = res.data.balance;
    rolling.value = false;
  }, 2000);
}

async function reset() {
  const res = await axios.post("http://127.0.0.1:8000/reset");
  balance.value = res.data.balance;
  dice.value = [];
  combination.value = "";
  win.value = 0;
}
</script>

<style>
.app {
  max-width: 600px;
  margin: auto;
  text-align: center;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  padding: 20px;
}

h1 {
  color: #333;
  margin-bottom: 10px;
}

.balance {
  margin: 15px 0;
  font-size: 20px;
  font-weight: bold;
  color: #2d6a4f;
  background: #e9f5ee;
  padding: 10px;
  border-radius: 8px;
}

.prices {
  width: 100%;
  margin: 15px 0;
  border-collapse: collapse;
  background: #fafafa;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  border-radius: 10px;
  overflow: hidden;
}

.prices th, .prices td {
  border: 1px solid #ddd;
  padding: 10px;
}

.prices th {
  background: #2d6a4f;
  color: white;
}

.prices tr:nth-child(even) {
  background: #f2f2f2;
}

.bet {
  margin: 20px 0;
}

.bet input {
  width: 80px;
  padding: 8px;
  font-size: 16px;
  margin-right: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
}

button {
  padding: 8px 16px;
  font-size: 16px;
  margin: 0 5px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: 0.2s;
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.btn-roll {
  background: #1d3557;
  color: white;
}
.btn-roll:hover:not(:disabled) {
  background: #457b9d;
}

.btn-reset {
  background: #e63946;
  color: white;
}
.btn-reset:hover:not(:disabled) {
  background: #d00000;
}

.dice {
  margin-top: 20px;
  display: flex;
  gap: 10px; 
  justify-content: center; 
  align-items: center;
  flex-wrap: nowrap; 
}

.die {
  font-size: 32px;
  margin: 0 8px;
  display: inline-block;
  background: #f1f1f1;
  border-radius: 8px;
  padding: 10px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.2);
}

.rolling {
  font-size: 20px;
  color: orange;
  margin-top: 15px;
}

.result {
  margin-top: 20px;
  font-size: 18px;
}

.win {
  color: #2d6a4f;
  font-weight: bold;
}

.lose {
  color: #d62828;
}
</style>
