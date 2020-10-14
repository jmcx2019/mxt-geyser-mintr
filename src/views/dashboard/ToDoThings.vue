<template>
  <div class="wallet-div">

    <div class="row">
      <div class="col wallet-detail-tit">{{ $t("dashboard.toDoThings.toDoName") }}</div>
    </div>

    <div class="row text-center" v-show="!toDoThingsViewDisabled">
      <div class="col like-to-do-div2" @click="viewMint">
        <div class="like-to-do-tit">一键梭哈{{ $t("token.name") }}</div>
      </div>
    </div>

    <div class="row text-center" v-show="!toDoThingsViewDisabled">
      <div class="col like-to-do-div" @click="viewMint">
        <div>
          <img class="like-to-do-logo" :src="'/static/like-to-do/mint.png'" :alt="$t('token.name')"/>
        </div>
        <div class="like-to-do-tit">{{ $t("action.mint") }}</div>
        <div class="like-to-do-txt">{{ $t("dashboard.toDoThings.mint") }}</div>
      </div>
      <div class="col like-to-do-div" @click="viewBurn">
        <div>
          <img class="like-to-do-logo" :src="'/static/like-to-do/burn.png'" :alt="$t('token.name')"/>
        </div>
        <div class="like-to-do-tit">{{ $t("action.burn") }}</div>
        <div class="like-to-do-txt">{{ $t("dashboard.toDoThings.burn") }}</div>
      </div>
    </div>

    <div class="row text-center" v-show="!toDoThingsViewDisabled">
      <div class="col like-to-do-div" @click="viewDeposit">
        <div>
          <img class="like-to-do-logo" :src="'/static/like-to-do/deposit.png'" :alt="$t('token.name')"/>
        </div>
        <div class="like-to-do-tit">{{ $t("action.deposit") }}</div>
        <div class="like-to-do-txt">{{ $t("dashboard.toDoThings.deposit") }}</div>
      </div>
      <div class="col like-to-do-div" @click="viewWithdraw">
        <div>
          <img class="like-to-do-logo" :src="'/static/like-to-do/withdraw.png'" :alt="$t('token.name')"/>
        </div>
        <div class="like-to-do-tit">{{ $t("action.withdraw") }}</div>
        <div class="like-to-do-txt">{{ $t("dashboard.toDoThings.withdraw") }}</div>
      </div>
    </div>

    <div class="row" v-show="toDoThingsViewDisabled">
      <div class="col">
        <input type="submit" class="btn btn-info btn-cancel" value="Cancel"
               @click="cancel"
        >
      </div>
    </div>

    <mint v-show="!mintDisabled" :hide-mint="cancel" :all-balance="allBalance"></mint>

    <burn v-show="!burnDisabled" :hide-burn="cancel" :all-balance="allBalance"></burn>

    <deposit v-show="!depositDisabled" :hide-burn="cancel" :all-balance="allBalance"></deposit>

    <withdraw v-show="!withdrawDisabled" :hide-burn="cancel" :pool-info="poolInfo"></withdraw>

  </div>
</template>

<script>
  import Mint from "./actions/Mint"
  import Burn from "./actions/Burn"
  import Deposit from "./actions/Deposit"
  import Withdraw from "./actions/Withdraw"

  export default {
    name: "ToDoThings",
    props: ["allBalance", "poolInfo"],
    data() {
      return {
        toDoThingsViewDisabled: false,
        mintDisabled: true,
        burnDisabled: true,
        depositDisabled: true,
        withdrawDisabled: true,
        visible: false,
      }
    },
    methods: {
      handleCancel(e) {
        this.visible = false;
      },
      viewMint() {
        this.toDoThingsViewDisabled = true
        this.mintDisabled = false
      },
      viewBurn() {
        this.toDoThingsViewDisabled = true
        this.burnDisabled = false
      },
      viewDeposit() {
        this.toDoThingsViewDisabled = true
        this.depositDisabled = false
      },
      viewWithdraw() {
        this.toDoThingsViewDisabled = true
        this.withdrawDisabled = false
      },
      cancel() {
        this.toDoThingsViewDisabled = false
        this.mintDisabled = true
        this.burnDisabled = true
        this.depositDisabled = true
        this.withdrawDisabled = true
      },
      comingSoon() {
        this.visible = true;
      }
    },
    components: { Mint, Burn, Deposit, Withdraw }
  }
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.wallet-div {
  border-width: 1px;
  border-style: solid;
  border-color: $divBorderColor;
  border-image: initial;
  border-radius: 5px;
  padding: 15px;
  margin-top: 15px !important;

  .wallet-detail-tit {
    color: $fontColor;
    letter-spacing: 1px;
    font-family: $fontTit;
    font-weight: 500;
    display: flex;
    align-items: center;
    height: 40px;
  }

  .like-to-do-div,
  .like-to-do-div2 {
    cursor: pointer;
    max-width: 336px;
    background-color: $btnBg;
    box-shadow: $btnBoxShadow 0 5px 10px 5px;
    flex: 1 1 0;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 5px;
    transition: transform 0.2s ease-in 0s;
    margin: 8px !important;
    padding: 24px;
  }
  .like-to-do-div2 {
    max-width: 100%;
  }

  .like-to-do-logo {
    width: 100px;
    height: 100px;
  }

  .like-to-do-tit {
    font-size: 24px;
    color: $fontColor;
    letter-spacing: 2px;
    font-family: $fontTit;
    margin: 15px 0 15px !important;
  }

  .like-to-do-txt {
    font-size: 14px;
    color: $fontColor;
    letter-spacing: 2px;
    margin: 15px 0 15px !important;
  }

  .btn-cancel {
    background-color: $btnBg;
    height: 40px;
    display: flex;
    -webkit-box-align: center;
    align-items: center;
    -webkit-box-pack: justify;
    justify-content: space-between;
    font-size: 14px;
    cursor: pointer;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    padding: 2px 20px 0;
    border-radius: 20px;
    transition: all 0.1s ease-in 0s;
    text-decoration: none;
    color: $btnFontColor;
    float: right;
    margin-bottom: 15px;
  }
}
</style>
