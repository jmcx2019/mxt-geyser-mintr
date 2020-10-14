<template>
  <div class="row text-center claim-div">
    <div class="col">
      <div class="row">
        <div class="col">
          <img class="claim-logo" :src="'/static/like-to-do/withdraw.png'" :alt="$t('token.name')"/>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="claim-tit">{{ $t("action.withdraw") }}</p>
          <p class="claim-txt">
            {{ $t("dashboard.withdraw.amount") }}
            <b>{{ stakingAmount }} {{$t("token.uniName") }}</b>
            ,
            <b>{{ stakingSynthAsset }} {{$t("token.mxtCake") }}</b>
          </p>
          <p class="claim-txt">{{ $t("dashboard.withdraw.claim") }}<b>{{ mxtRewards }} {{$t("token.name") }}</b></p>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <input type="submit" class="btn btn-info btn-claim-now" :value="$t('action.withdraw')"
                 @click="claim"
          >
        </div>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import chainOpt from "../../../utils/web3/chainOperation";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

let opt = chainOpt.opt
export default {
  name: "Withdraw",
  props: ["hideWithdraw", "poolInfo"],

  components: {
    Loading
  },

  data(){
    return {
      willClaim: "0",
      loading: false,
    }
  },

  computed: {
    stakingAmount() {
      // return new Decimal(this.stakingInfo[0]).div(1e18).toDP(6, Decimal.ROUND_FLOOR).toString()
      return this.poolInfo ? this.poolInfo.lockedUni : 0
    },

    stakingSynthAsset() {
      return this.poolInfo ? this.poolInfo.lockedSyn : 0
    },

    mxtRewards() {
      return this.poolInfo ? this.poolInfo.reward : 0
    }
  },

  methods: {
    async claim() {
      this.loading = false
      this.loading = true
      await opt.claimReward()
      this.loading = false

      if(typeof this.hideWithdraw === "function") {
        this.hideWithdraw()
      }
    }
  },
}
</script>


<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../../styles/colors.scss";
@import "../../../styles/fonts.scss";

.claim-div {
  flex: 1 1 0;
  padding: 24px 0;

  .claim-logo {
    width: 180px;
    height: 180px;
  }

  .claim-tit {
    font-family: $fontTit;
    font-size: 32px;
    letter-spacing: 2px;
    margin: 12px 0 24px;
  }
  .claim-txt {
    font-family: $fontTxt;
    font-size: 16px;
    font-weight: 400;
  }
  .claim-input {
    color: $fontColor;
    background-color: $bg;
    font-family: $fontTxt;
    width: 100%;
    height: 54px;
    font-size: 24px;
    padding: 16px;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 20px;
    outline: none;
    margin-bottom: 12px;
  }

  .btn-claim-now {
    color: $btnFontColor;
    background-color: $btnBg;
    font-family: $fontTit;
    width: 100%;
    height: 72px;
    text-transform: uppercase;
    cursor: pointer;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 5px;
    transition: all 0.1s ease-in 0s;
  }
}
</style>
