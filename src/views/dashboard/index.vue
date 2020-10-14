<template>
  <div class="dashboard-container">
    <div class="row">
      <div class="col">
        <wallet-details :all-balance="allBalance" :pool-info="poolInfo"></wallet-details>
      </div>
      <div class="col">
        <to-do-things :all-balance="allBalance" :pool-info="poolInfo"></to-do-things>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import WalletDetails from "./WalletDetails";
import ToDoThings from "./ToDoThings";
import chainOpt from "@/utils/web3/chainOperation";

import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

let querying = false
let opt = chainOpt.opt

export default {
  name: "dashboard",
  components: { WalletDetails, ToDoThings, Loading },

  data() {
    return {
      allBalance: null,
      poolInfo: null,
      loading: true,
    }
  },

  mounted() {
    this.loopGetInfo()
    setInterval(async _=> {
      await this.loopGetInfo()
    }, 5000)
  },

  methods: {
    async loopGetInfo() {
      if(querying) {
        return
      }

      querying = true

      this.allBalance = await opt.allBalanceInfo()
      this.poolInfo = await opt.poolInfo()

      this.loading = false
      querying = false
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.dashboard-container {
  width: 100%;
  height: 100%;
}
</style>
