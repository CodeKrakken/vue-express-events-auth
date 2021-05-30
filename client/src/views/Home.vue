<template>
<div id="app">
  <Wallet :wallet="data.wallet" :currentPrice="data.currentPriceObject.price" />
</div>
</template>

<script>

import Wallet from '@/views/Wallet.vue'
import EventService from '@/services/EventService.js'

export default {
  name: 'home',
  data() {
    return {
      timer: '',
      data: {},
      lastPrice: 0,
      firstRun: true      
    }
  },
  created() {
    this.timer = setInterval(this.getTick, 2000)
  },
  methods: {
    async getTick () {
      EventService.getTick()
      .then(response => this.refreshData(response))
    },
    refreshData(data) {
      if (!this.firstRun) {
        this.lastPrice = this.data.currentPriceObject.price
      }
      this.$set(this, "data", data);
      this.firstRun = false
      console.log(data)
    },
    trimOrders(orders, currentPrice) {
      let returnObject = {
        orders: []
      }
      let totalCurrentDollar = 0
      let totalProjectedDollar = 0
      orders.forEach(order => {
        returnObject.orders.push({
          'Side': order.side,
          'Time': order.timestamp,
          'Volume': order.amount,
          '@ Price': order.price,
          '= Current BUSD': this.n((order.amount * currentPrice), 2),
          '= Projected BUSD': this.n((order.amount * order.price), 2)
        })
        totalCurrentDollar += (order.amount * currentPrice)
        totalProjectedDollar += (order.amount * order.price)
      })
      returnObject['totals'] = {
        'totalCurrentDollar': totalCurrentDollar,
        'totalProjectedDollar': totalProjectedDollar
      }
      return returnObject
    },
    n(n, d) {
      return Number.parseFloat(n).toFixed(d);
    }
  },
  components: {
    Wallet
  }
}
</script>

<style>
  
</style>