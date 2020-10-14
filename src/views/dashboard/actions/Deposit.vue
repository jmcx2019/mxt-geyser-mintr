<template>
  <div class="row text-center staking-div">
    <div class="col">
      <div class="row">
        <div class="col">
          <img class="staking-logo" :src="'/static/like-to-do/deposit.png'" :alt="$t('token.name')"/>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="staking-tit">{{ $t("action.deposit") }}</p>
          <p class="staking-txt">{{ $t("dashboard.deposit.txt") }}</p>
          <p class="staking-apy"><b>{{ $t("dashboard.deposit.apy") }}</b></p>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="staking-input-tit">
            {{ $t("dashboard.deposit.confirm1") }}
            <b>{{ $t("token.uniName") }}</b>
            {{ $t("dashboard.deposit.confirm2") }}
          </p>
          <input type="text" v-model="mxtUniAmount" placeholder="0" class="staking-input">
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="staking-input-tit">
            {{ $t("dashboard.deposit.mustBe") }}
            <b> {{mxtUniAmount * 49}} {{ $t("token.synthAsset") }} </b>
          </p>
        </div>
      </div>
      <div class="row">
        <div class="col text-left notes-txt">
          {{ $t("dashboard.deposit.tag") }}
        </div>
      </div>
      <div class="row">
        <div class="col">
          <input v-show="enabled" type="submit" class="btn btn-info btn-staking-now" :value="$t('action.deposit')"
                 @click="staking"
          >
          <input v-show="!enabled" type="submit" class="btn btn-info btn-staking-now" :value="$t('action.enable')"
                 @click="enable"
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
import $ from "jquery";
import Decimal from "decimal.js";

// Import component
import Loading from 'vue-loading-overlay';
// Import stylesheet
import 'vue-loading-overlay/dist/vue-loading.css';

let opt = chainOpt.opt
export default {
  name: "deposit",
  components:{
    Loading
  },
  props: ["hideStaking", "roundInfo"],
  data() {
    return {
      mxtUniAmount: "0",
      mxtCakeAmount: "0",
      enabled: false,
      willLock: "0",
      loading: false,
    }
  },

  watch: {
    enabled() {
      if(this.enabled) {
        $(this.$el).find(".staking-input, .staking-input-tit, .notes-txt").show()
        $(this.$el).find(".staking-input").removeAttr("disabled")
      } else {
        $(this.$el).find(".staking-input, .staking-input-tit, .notes-txt").hide()
        $(this.$el).find(".staking-input").attr("disabled", "disabled")
      }
    },
  },

  mounted() {
    setTimeout(async _=>{
      this.enabled = await opt.isDepositEnabled().catch(e=>{return false})
      if(!this.enabled) {
        this.$nextTick(_=>{
          $(this.$el).find(".staking-input, .staking-input-tit, .notes-txt").hide()
          $(this.$el).find(".staking-input").attr("disabled", "disabled")
        })
      }
    }, 500)
  },

  methods: {
    async loopCheckEnabled() {
      this.enabled = await opt.isDepositEnabled().catch(e=>{return false})

      if(this.enabled) {
        return
      }

      setTimeout(this.loopCheckEnabled, 15000)
    },

    async enable() {
      this.loading = true

      let enabled = await opt.isDepositEnabled()
      if(enabled) {
        this.enabled = true
        this.loading = false
        return
      }

      enabled = await opt.enableDeposit()

      this.loading = false
      if(enabled === null) {
        return
      }
      await this.loopCheckEnabled()
    },

    async staking() {
      try {
        let _ = new Decimal(this.mxtUniAmount)
      } catch (e) {
        setTimeout(_=>{
          this.$notification.open({
            message: 'Invalid Input!',
            description: "Amount must be a number",
            duration: 0,
          });
          this.loading = false
        }, 400)

        return
      }

      if(parseFloat(this.mxtUniAmount) < 2) {
        setTimeout(_=>{
          this.$notification.open({
            message: 'Minimum deposit of UNI-V2-LP is 2!',
            description: "",
            duration: 0,
          });
          this.loading = false
        }, 400)
        return
      }

      let enabled = await opt.isDepositEnabled()
      if(!enabled) {
        this.enabled = false
        return
      }

      this.loading = true
      let tx = await opt.deposit(this.mxtUniAmount)

      if(tx !== null){
        console.log("transaction send success, tx hash is: ", tx)
      } else {

      }

      this.loading = false
      if(typeof this.hideStaking === "function") {
        this.hideStaking()
      }
    },
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../../styles/colors.scss";
@import "../../../styles/fonts.scss";

.staking-div {
  flex: 1 1 0;
  padding: 24px 0;

  .staking-logo {
    width: 180px;
    height: 180px;
  }

  .staking-tit {
    font-family: $fontTit;
    font-size: 32px;
    letter-spacing: 2px;
    margin: 12px 0 24px;
  }
  .staking-txt,
  .staking-apy,
  .staking-input-tit {
    color: $txtColor;
    font-family: $fontTit;
    font-size: 16px;
    font-weight: 400;
  }
  .staking-txt,
  .staking-apy {
    font-family: $fontTxt;
  }
  .staking-apy {
    color: $fontColor;
    font-size: 20px;
  }
  .staking-input-tit {
    color: $fontColor;
    font-family: $fontTit;
    margin-top: 12px;
  }
  .staking-input {
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

  .notes-txt {
    color: $notesColor;
    font-family: $fontTxt;
    font-size: 10px;
    line-height: 18px;
    letter-spacing: 0.5px;
    font-weight: 400;
  }

  .btn-staking-now {
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
