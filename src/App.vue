<template>
  <div id="app">
    <template v-if="address">
      <h2> {{address}}</h2>
      <h3> {{balance}} CFX</h3>
      <button @click="sendTransaction"> Send Transaction</button>
    </template>
    <template v-else>
      <button @click="connectWallet"> Connect Wallet</button>
    </template>
  </div>
</template>

<script>

import { Conflux, Drip} from "js-conflux-sdk";
var confluxJS = new Conflux({ url: "https://portal-main.confluxrpc.org"})

export default {
  name: 'App',
  data() {
    return {
      address: "",
      balance: "0"
    }
  },
  methods: {
    connectWallet(){
      if(window.conflux){
        window.conflux.enable().then( accounts => {
          if (accounts && accounts.length > 0) {
            this.address = accounts[0]
            this.getBalance()
          }
        })
      }else{
        alert("Please mathwallet frist!")
      }
    },
    getBalance(){
      confluxJS.getBalance(this.address).then(balance => {
        this.balance = new Drip(balance).toCFX()
      });
    },
    sendTransaction() {
      confluxJS.provider = window.conflux;
      // conflux.s
      confluxJS
        .sendTransaction({
          from: this.address,
          to: "cfx:aak8af9s87471w8sc4wc9uz6a7ve0v576u4s4j6vuu",
          value: Drip.fromCFX("0.1")
        }).then(response => {
          console.log(response);
        }).catch(e => {
          console.log(e);
        });
    }

  },
}
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
</style>
