<template>
  <div class="row text-center mint-div">
    <div class="col">
      <div class="row">
        <div class="col">
          <img class="mint-logo" :src="'/static/like-to-do/mint.png'" :alt="$t('token.name')"/>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="mint-tit">{{ $t("action.mint") }}</p>
          <p class="mint-txt">{{ $t("dashboard.mint.txt") }}</p>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="mint-input-tit">{{ $t("dashboard.mint.confirm") }}</p>
          <input type="text" v-model="synAmount" placeholder="0.00" class="mint-input">
        </div>
      </div>
      <div class="row">
        <div class="col-6 text-left notes-txt">
          {{ $t("dashboard.mint.staking") }} {{willLock}} {{ $t("token.name") }}
        </div>
      </div>
      <div class="row">
        <div class="col">
          <input v-show="enabled" type="submit" class="btn btn-info btn-mint-now" value="Mint Now"
                 @click="mint"
          >
          <input v-show="!enabled" type="submit" class="btn btn-info btn-mint-now" :value="$t('action.enable')"
                 @click="enableMint"
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
import * as constant from "@/utils/web3/constant"

// Import component
import Loading from 'vue-loading-overlay';
// Import stylesheet
import 'vue-loading-overlay/dist/vue-loading.css';

let opt = chainOpt.opt
export default {
  name: "mint",
  components:{
    Loading
  },

  props: ["hideMint", "allBalance"],
  data() {
    return {
      synAmount: "0",
      enabled: false,
      willLock: "0",
      loading: false,
    }
  },

  watch: {
    enabled() {
      if(this.enabled) {
        $(this.$el).find(".mint-input, .mint-input-tit, .notes-txt").show()
        $(this.$el).find(".mint-input, .mint-input-tit, .notes-txt").removeAttr("disabled")
      } else {
        $(this.$el).find(".mint-input, .mint-input-tit, .notes-txt").hide()
        $(this.$el).find(".mint-input, .mint-input-tit, .notes-txt").attr("disabled", "disabled")
      }
    },

    synAmount() {
      this.willLock = this.synAmount
    }
  },

  mounted() {
    setTimeout(async _=>{
      this.enabled = await opt.isSynEnabled().catch(e=>{return false})
      if(!this.enabled) {
        this.$nextTick(_=>{
          $(this.$el).find(".mint-input, .mint-input-tit, .notes-txt").hide()
          $(this.$el).find(".mint-input, .mint-input-tit, .notes-txt").attr("disabled", "disabled")
        })
      }
    }, 500)
  },

  methods: {
    async enableMint() {
      this.loading = true
      let enabled = await opt.isSynEnabled()
      if(enabled) {
        this.enabled = true
        this.loading = false
        return
      }

      let _ = await opt.enableSyn()
      this.enabled = false
      this.loading = false
    },

    async mint() {
      let enabled = await opt.isSynEnabled()
      if(!enabled) {
        this.enabled = false
        return
      }

      this.loading = true
      let tokenAmount = 0
      let validMint = false
      try {
        Decimal.set({ toExpPos: 256 })
        tokenAmount = new Decimal(this.synAmount)
        validMint = tokenAmount.lte(new Decimal(this.allBalance.base))
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

      if(!validMint) {
        setTimeout(_=>{
          this.$notification.open({
            message: 'Invalid Mint!',
            description: this.$i18n.t("token.name") + " insufficient",
            duration: 0,
          });
          this.loading = false
        }, 400)

        return
      }

      let mintResult = await opt.mintSyn(tokenAmount.toString())

      if(mintResult !== null){
        console.log("mint transaction send success, tx hash is: ", mintResult)
      } else {
        //user cancel or other unknown things happened, do nothing
      }

      this.loading = false
      if(typeof this.hideMint === "function") {
        this.hideMint()
      }
    },
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../../styles/colors.scss";
@import "../../../styles/fonts.scss";

.mint-div {
  flex: 1 1 0;
  padding: 24px 0;

  .mint-logo {
    width: 180px;
    height: 180px;
  }

  .mint-tit {
    font-family: $fontTit;
    font-size: 32px;
    letter-spacing: 2px;
    margin: 42px 0 12px;
  }
  .mint-txt,
  .mint-input-tit {
    font-family: $fontTxt;
    font-size: 16px;
    color: $txtColor;
    font-weight: 400;
  }
  .mint-input-tit {
    font-family: $fontTit;
    margin-top: 48px;
  }
  .mint-input {
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

  .btn-mint-now {
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
