<template>
  <div class="row text-center burn-div">
    <div class="col">
      <div class="row">
        <div class="col">
          <img class="burn-logo" :src="'/static/like-to-do/burn.png'" :alt="$t('token.name')"/>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="burn-tit">{{ $t("action.burn") }}</p>
          <p class="burn-txt">{{ $t("dashboard.burn.txt") }}</p>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <p class="burn-input-txt">{{ $t("dashboard.burn.confirm") }}</p>
          <input type="text" v-model="burnAmount" placeholder="0.00" class="burn-input">
        </div>
      </div>
      <div class="row">
        <div class="col-6 text-left notes-txt">
          {{ $t("dashboard.burn.unlock") }} {{willUnlock}} {{ $t("token.name") }}
        </div>
      </div>
      <div class="row">
        <div class="col">
          <input type="submit" class="btn btn-info btn-burn-now" value="Burn Now"
                 @click="burn"
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
// Import component
import Loading from 'vue-loading-overlay';
// Import stylesheet
import 'vue-loading-overlay/dist/vue-loading.css';
import Decimal from "decimal.js";

let opt = chainOpt.opt
export default {
  name: "Burn",
  props: ["hideBurn", "allBalance"],

  components: {
    Loading
  },

  data(){
    return {
      burnAmount: "0",
      willUnlock: "0",
      loading: false,
    }
  },

  watch: {
    burnAmount() {
      this.willUnlock = this.burnAmount
    }
  },

  methods: {
    async burn() {
      let tokenAmount = 0
      let validUnlock = false
      try {
        Decimal.set({ toExpPos: 256 })
        tokenAmount = new Decimal(this.burnAmount)
        validUnlock = tokenAmount.lte(new Decimal(this.allBalance.syn))
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



      this.loading = true
      if(!validUnlock) {
        this.loading = false
        this.$notification.open({
          message: 'Burn amount exceed!',
          description: validUnlock,
          duration: 0,
        });
        return
      }

      let unlockResult = await opt.burnSyn(tokenAmount)
      if(unlockResult !== null){
        console.log("mint transaction send success, tx hash is: ", unlockResult)
      } else {
        //user cancel or other unknown things happened, do nothing
      }

      this.loading = false
      if(typeof this.hideBurn === "function") {
        this.hideBurn()
      }
    }
  },
}
</script>


<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../../styles/colors.scss";
@import "../../../styles/fonts.scss";

.burn-div {
  flex: 1 1 0;
  padding: 24px 0;

  .burn-logo {
    width: 180px;
    height: 180px;
  }

  .burn-tit {
    font-size: 32px;
    color: $fontColor;
    letter-spacing: 2px;
    margin: 15px 0 15px;
    font-family: $fontTit;
  }
  .burn-txt,
  .burn-input-txt {
    font-size: 16px;
    color: $txtColor;
    font-weight: 400;
    font-family: $fontTxt;
  }
  .burn-input-txt {
    margin-top: 30px;
  }
  .burn-input {
    width: 100%;
    height: 54px;
    background-color: $bg;
    font-size: 24px;
    color: $fontColor;
    padding: 16px;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 20px;
    outline: none;
    margin-bottom: 12px;
    font-family: $fontTxt;
  }

  .notes-txt {
    color: $notesColor;
    font-family: $fontTxt;
    font-size: 10px;
    line-height: 18px;
    letter-spacing: 0.5px;
    font-weight: 400;
  }

  .btn-burn-now {
    color: $btnFontColor;
    background-color: $btnBg;
    font-family: $fontTit;
    width: 100%;
    height: 72px;
    margin-top: 30px;
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
