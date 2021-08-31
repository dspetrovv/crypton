<template>
  <div class="input-block">
    <div>
      <label for="ticker-name">Ticker</label>
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
              @click="add(coinSymbol.FullName)"
            >
              {{ coinSymbol.Symbol }}
            </button>
          </div>
        </div>
      </template>
    </div>
    <div
      class="input-error-block"
      v-if="inputError"
    >{{ inputError }}</div>
    <div>
      <button class="ticker-btn" @click="add()"><div>Add ticker</div></button>
    </div>
  </div>
  <template v-if="cryptoCoins.length">
    <hr />
      <div class="crypto-block">
        <div v-for="crypto of cryptoCoins" :key="crypto.name">
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
      cryptoCoins: [],
      cryptoCoinList: [[],[],[],[],[],[],[],[],[]],
      similarCoins: [],
      inputError: ''
    };
  },

  created: async function() {
    const crypts = await fetch('https://min-api.cryptocompare.com/data/all/coinlist?summary=true')
    const json = await crypts.json()
    for (let cryptoInfo in json.Data) {
      const idx = this.checkRegExp(json.Data[cryptoInfo].Symbol[0])
      this.cryptoCoinList[idx].push(json.Data[cryptoInfo])
    }
  },

  methods: {
    add(coin = '') {
      let currentInput = this.ticker.trim()
      if (currentInput && this.similarCoins.length > 0) {
        coin = this.checkIfCoinExists(coin, currentInput)
        if (!coin) {
          this.inputError = "Error! The coin '" + this.ticker + "' doesn't exist"
          return;
        }
        for (let choosenCoin of this.cryptoCoins){
          if (choosenCoin.name == coin) {
            this.inputError = "Error! You already chose '" + coin + "' coin"
            return;
          }
        }
        const newCoin = {
          name: coin,
          value: "-"
        }
        this.cryptoCoins.push(newCoin)
        this.ticker = ""
        this.similarCoins = []
        this.inputError = ''
      } else {
        this.inputError = currentInput.length > 0 ? 
        "Error! The coin '" + this.ticker + "' doesn't exist"
        : "Error! Field 'Coin' cannot be empty"
      }
    },

    deleteTicker(value) {
      this.cryptoCoins = this.cryptoCoins.filter((crypto) => crypto != value)
    },

    checkIfCoinExists(coin, currentInput) {
      if (coin == '') {
        const idx = this.checkRegExp(currentInput[0])
        for (let cryptoCoin of this.cryptoCoinList[idx]) {
          if (
            cryptoCoin.FullName.toUpperCase() == currentInput.toUpperCase()
            || cryptoCoin.Symbol.toUpperCase() == currentInput.toUpperCase()
            ) {
            coin = cryptoCoin.FullName
            break
          }
        }
      }
      return coin
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
      this.inputError = ''
      let currentInput = this.ticker.trim()
      console.log('currentInput',currentInput)
      if (currentInput.length > 0) {
        const idx = this.checkRegExp(currentInput[0])
        for (let coin of this.cryptoCoinList[idx]) {
          if (coin.FullName.toUpperCase().includes(currentInput.toUpperCase())) {
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
  transition: background-color 0.1s ease-in-out;
}
.button-helper:hover {
  background-color: rgba(64, 75, 87, 0.9);
}

.input-error-block {
  color: red;
  font-size: 18px;
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
  height: 30%;
  padding: .4rem 0;
  text-transform: uppercase;
  color: gray;
  font-size: 15px;
}
.currency-value {
  height: 30%;
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
