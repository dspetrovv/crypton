<template>
  <div class="input-block">
    <div>
      <label for="ticker-name">Ticker: {{ ticker }}</label>
      <div style="margin= .5rem; width: 20vw;">
        <input
          v-model="ticker"
          @keydown.enter="add()"
          @input="findSimilar()"
          type="text"
          id="ticker-name"
          class="ticker-input"
          maxlength="8"
        />
      </div>
      <template v-if="similarCoins.length">
        <div class="input-helper">
          <div
            v-for="coinSymbol of similarCoins"
            :key="coinSymbol.Symbol"
          >
            <button
              class="button-helper"
              :title="coinSymbol.FullName"
              @click="add()"
            >
              {{ coinSymbol.Symbol }}
            </button>
          </div>
        </div>
      </template>
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
      tickers: [],
      cryptoNames: [[],[],[],[],[],[],[],[],[]],
      similarCoins: []
    };
  },

  created: async function() {
    const crypts = await fetch('https://min-api.cryptocompare.com/data/all/coinlist?summary=true')
    const json = await crypts.json()
    const cryptoData = []
    for (let cryptoInfo in json.Data) {
      cryptoData.push(json.Data[cryptoInfo])
      const idx = this.checkRegExp(json.Data[cryptoInfo].Symbol[0])
      this.cryptoNames[idx].push(json.Data[cryptoInfo])
    }
    this.cryptoCoinList = cryptoData
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
    },

    checkRegExp(str) {
      if (/[A-D]+/gi.test(str)) {
        return 0
      } else if (/[E-H]+/gi.test(str)) {
        return 1
      } else if (/[I-L]+/gi.test(str)) {
        return 2
      } else if (/[M-P]+/gi.test(str)) {
        return 3
      } else if (/[Q-T]+/gi.test(str)) {
        return 4
      } else if (/[U-X]+/gi.test(str)) {
        return 5
      } else if (/[Y-Z]+/gi.test(str)) {
        return 6
      } else if (/[0-4]+/gi.test(str)) {
        return 7
      } else if (/[5-9$]+/gi.test(str)) {
        return 8
      }
    },
    
    findSimilar() {
      this.similarCoins = []
      let currentInput = this.ticker.trim();
      if (currentInput.length > 0) {
        const idx = this.checkRegExp(currentInput[0])
        for (let coin of this.cryptoNames[idx]) {
          if (coin.FullName.toUpperCase().includes(this.ticker.toUpperCase())) {
            this.similarCoins.push(coin)
          }
          if (this.similarCoins.length == 4)
            break
        }
      }
      console.log('similar: ',this.similarCoins)
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
  width: 30vw;
  text-align: start;
  margin: 0.4rem;
}
.ticker-input {
  padding: 0.3rem;
  font-size: 22px;
  display: block;
  border-width: 0 0 1px 0;
  border-color: rgb(64, 75, 87);
  transition: border-color 0.1s ease-in-out;
}
.ticker-input:focus,
.ticker-input:hover {
  border-color: rgba(64, 75, 87, 0.5);
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
  background-color: rgba(255, 0, 255, 0.8);
  border-color: rgb(255, 0, 255);
  color: white;
}

.input-helper {
  width: 100%;
  font-size: 15px;
  padding: .3rem;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: flex-start;
}
.input-helper > div {
  flex: 0 0 15%;
  text-align: center;
  margin-left: .2rem;
  border: 1px solid #000;
  border-radius: 10%;
}
.button-helper {
  width: 100%;
  color: white;
  background-color: rgb(64, 75, 87);
  border: 1px solid rgba(64, 75, 87, .8);
}
.button-helper:hover {
  background-color: rgba(64, 75, 87, 0.9);
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
