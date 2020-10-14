<template>
  <div class="wallet-div">

    <div class="row">
      <div class="col text-left wallet-detail-tit">{{ $t("dashboard.wallet.details") }}</div>
    </div>

    <div class="row balance-list-div">
      <div class="col">
        <div class="row">
          <div class="col text-center token-price">{{ $t("dashboard.wallet.balanceList") }}</div>
        </div>
        <div class="row balance-item-1">
          <div class="col">
            <img class="balance-logo" :src="'/static/logo/icon.png'" alt=""/>
            {{ $t("token.name") }}
          </div>
          <div class="col text-right">{{tokenBalance}}</div>
        </div>
        <div class="row balance-item-2">
          <div class="col">
            <img class="balance-logo" :src="'/static/token/ETH.svg'" alt=""/>
            {{ $t("token.eth") }}
          </div>
          <div class="col text-right">{{ethBalance}}</div>
        </div>
        <div class="row balance-item-1">
          <div class="col">
            <img class="balance-logo" :src="'/static/token/synthAsset.png'" alt=""/>
            {{ $t("token.synthAsset") }}
          </div>
          <div class="col text-right">{{synBalance}}</div>
        </div>
        <div class="row balance-item-2">
          <div class="col">
            <img class="balance-logo" :src="'/static/logo/icon.png'" alt=""/>
            {{ $t("token.uniName") }}
          </div>
          <div class="col text-right">{{ uniBalance }}</div>
        </div>

        <div class="row balance-line"></div>

        <div class="row balance-item-1">
          <div class="col">
            <img class="balance-logo" :src="'/static/logo/icon.png'" alt=""/>
            {{ $t("token.rewardToken") }}
          </div>
          <div class="col text-right">{{lockedToken}}</div>
        </div>
        <div class="row balance-item-2">
          <div class="col">
            <img class="balance-logo" :src="'/static/token/synthAsset.png'" alt=""/>
            {{ $t("token.lockedSynthAsset") }}
          </div>
          <div class="col text-right">{{lockedSynBalance}}</div>
        </div>
        <div class="row balance-item-1">
          <div class="col">
            <img class="balance-logo" :src="'/static/logo/icon.png'" alt=""/>
            {{ $t("token.lockedUniName") }}
          </div>
          <div class="col text-right">{{ lockedUniBalance }}</div>
        </div>
      </div>
    </div>

    <div class="row balance-list-div">
      <div class="col">
        <div class="row">
          <div class="col text-center token-price">{{ $t("dashboard.stats.name") }}</div>
        </div>
        <div class="row balance-item-1">
          <div class="col">
            {{ $t("dashboard.stats.totalRewards") }}
          </div>
          <div class="col text-right"><b>{{totalRewards}}{{ $t("dashboard.stats.token") }}</b></div>
        </div>
        <div class="row balance-item-2">
          <div class="col">
            {{ $t("dashboard.stats.totalDeposits") }}
          </div>
          <div class="col text-right"><b>{{totalDeposits}} {{ $t("dashboard.stats.usd") }}</b></div>
        </div>

        <div class="row balance-item-1">
          <div class="col">
            {{ $t("dashboard.stats.endBlock") }}
          </div>
          <div class="col text-right"><b>{{endBlock}}</b></div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
  import chainOpt from "../../utils/web3/chainOperation";

  let opt = chainOpt.opt

  export default {
    name: "WalletDetails",
    props: ["allBalance", "poolInfo"],

    watch: {
      allBalance() {
        this.tokenBalance = this.allBalance.base
        this.ethBalance = this.allBalance.eth
        this.uniBalance = this.allBalance.uni
        this.synBalance = this.allBalance.syn
      },

      poolInfo() {
        if (!this.poolInfo.claimed) {
          this.lockedSynBalance = this.poolInfo.lockedSyn
          this.lockedUniBalance = this.poolInfo.lockedUni
          this.lockedToken = this.poolInfo.reward
        } else {
          this.lockedSynBalance = 0
          this.lockedUniBalance = 0
          this.lockedToken = 0
        }

        this.endBlock = this.poolInfo.endBlock
        this.totalDeposits = this.poolInfo.totalLockedUni
        this.totalRewards = this.poolInfo.totalReward
      }
    },

    data() {
      return {
        mintingRate: "0",
        ethBalance: "0",
        tokenBalance: "0",
        lockedToken: "0",
        synBalance: "0",
        lockedSynBalance: "0",
        uniBalance: "0",
        lockedUniBalance: "0",
        loading: true,

        totalRewards: 0,
        totalDeposits: 0,
        rewardsRate: 0,
        roundInfo: null,
        stakingInfo: null,
        endBlock: 0,
      }
    },

    methods: {}
  }
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.wallet-div {
  margin-top: 15px !important;
  border-width: 1px;
  border-style: solid;
  border-color: $divBorderColor;
  border-image: initial;
  border-radius: 5px;
  padding: 15px;

  .balance-list-div {
    padding: 15px 0;
  }

  .wallet-detail-tit {
    color: $fontColor;
    letter-spacing: 1px;
    font-family: $fontTit;
    font-weight: 500;
    display: flex;
    align-items: center;
    height: 40px;
  }

  .token-price {
    margin-left: 8px !important;
    font-size: 16px;
    font-family: $fontTit;
    font-weight: bold;
    -webkit-box-align: center;
    align-items: center;
    color: $txtColor;
  }

  .token-price {
    margin: 15px 0 !important;
  }

  .balance-item-1,
  .balance-item-2 {
    margin-top: 8px !important;
    font-family: $fontTxt;
    font-size: 14px;
    padding-top: 4px;
    padding-bottom: 4px;
  }

  .balance-item-1 {
    background-color: $tableSeparationColor;
  }

  .balance-logo {
    height: 14px;
    margin-right: 4px;
  }

  .balance-line {
    height: 1px;
    background-color: $divBorderColor;
    margin: 32px auto !important;
  }
}
</style>
