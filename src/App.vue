<template>
  <div class="input-block">
    <div>
      <label for="ticker-name">Ticker: {{ ticker }}</label>
      <div style="margin= .5rem">
        <input
          v-model="ticker"
          @keydown.enter="add()"
          type="text"
          id="ticker-name"
          class="ticker-input"
          maxlength="8"
        />
      </div>
    </div>
    <div>
      <button class="ticker-btn" @click="add()"><div>Add ticker</div></button>
    </div>
  </div>
  <template v-if="tickers.length">
    <hr />
      <div class="crypto-block">
        <div v-for="crypto of tickers" :key="crypto.name">
          <div class="currency-name">
            {{crypto.name}} to USD
          </div>
          <div class="currency-value">
            {{crypto.value}} 
          </div>
          <div>
            <button class="crypto-delete" @click="deleteTicker(crypto)">Delete</button>
          </div>
        </div>
      </div>
    <hr />
  </template>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      ticker: "",
      tickers: []
    };
  },

  methods: {
    add() {
      if (this.ticker.trim()) {
        const newTicker = {
          name: this.ticker,
          value: "-"
        }
          this.tickers.push(newTicker)
        }
      this.ticker = ""
    },

    deleteTicker(value) {
      this.tickers = this.tickers.filter((crypto) => crypto != value)
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
label {
  vertical-align: top;
  padding-left: 0.3rem;
  font-weight: 600;
  font-size: 20px;
}
input,
button {
  outline: none;
}
.input-block {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.input-block > div {
  text-align: start;
  margin: 0.4rem;
}
.ticker-input {
  padding: 0.3rem;
  font-size: 22px;
  width: 20vw;
  display: block;
  border-width: 0 0 1px 0;
  border-color: rgb(64, 75, 87);
  transition: border-color 0.1s ease-in-out;
}
.ticker-input:focus,
.ticker-input:hover {
  border-color: rgb(64, 75, 87, 0.5);
}
.ticker-btn {
  padding: 0.4rem;
  font-size: 22px;
  width: 20vw;
  background-color: transparent;
  border: 2px solid rgba(255, 0, 255, 0.5);
  transition: background-color 0.3s, border-color 0.3s ease-in-out;
}
.ticker-btn:hover {
  background-color: rgb(255, 0, 255, 0.8);
  border-color: rgb(255, 0, 255);
  color: white;
}

.crypto-block {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  justify-content: flex-start;
}
.crypto-block > div {
  flex: 0 0 31%;
  padding: 1rem 0;
  margin: .4rem;
  border: 1px solid #000;
}
.crypto-block:first-child {
  flex-grow: 1;
}

.currency-name {
  padding: .4rem 0;
  text-transform: uppercase;
  color: gray;
  font-size: 15px;
}
.currency-value {
  font-size: 25px;
  font-weight: 600;
  padding: .3rem 0;
}
.crypto-delete {
  font-size: 16px;
  color: grey;
  background-color: transparent;
  border-color: transparent;
  transition: text-decoration 0.3s ease-in-out;
}
.crypto-delete:hover {
  text-decoration: underline;
}
</style>
