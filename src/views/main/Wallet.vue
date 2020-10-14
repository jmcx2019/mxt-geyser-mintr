<template>
  <div class="wallet-container">
    <div class="row">
      <div class="col text-center welcome-tit">
        <div class="welcome-div">{{ $t("common.welcome") }}</div>
      </div>
    </div>

    <div class="row">
      <div class="col">
        <div class="wallet-connect-div">
          <div class="row">
            <div class="col text-center wallet-connect-tit">
              {{ $t("wallet.connect") }}
            </div>
          </div>

          <div class="row">
            <div class="col d-flex justify-content-around">
              <button type="submit" class="wallet-btn" @click="openMetamask">
                <img class="wallet-btn-icon text-right" :src="'/static/logo/logo.png'" :alt="$t('wallet.metamask')"/>
                <h2 class="wallet-btn-name text-left">{{ $t("wallet.name") }}</h2>
              </button>
            </div>
          </div>

          <div class="row" v-show="walletConnectError">
            <div class="col text-center wallet-error-txt">
              {{ $t("wallet.error") }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import chainOpt from "../../utils/web3/chainOperation";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  name: "Wallet",
  components: { Loading },
  data() {
    return {
      ConnectWalletModalText: 'Waiting for connection',
      loading: false,
      walletConnectError: false,
    };
  },
  methods: {
    async openMetamask() {
      //wallet connect
      this.loading = true

      if(!chainOpt.wallet.isConnected()) {
        chainOpt.wallet.connect()
          .then(r=>{
            if(r === null) {
              //not connect, do nothing
              return
            }
            this.loading = false
            this.$router.push({name: "Dashboard"})
          })
          .catch(()=>{
            //unknown error, do nothing
            this.loading = false
            this.walletConnectError = true
          })
      } else {
        //already connected
        this.loading = false
        await this.$router.push({name: "Dashboard"})
      }
    }
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.wallet-container {
  max-width: 800px;
  margin: auto;
  height: 100%;
  padding-top: 120px;
  padding-bottom: 120px;

  .welcome-tit {
    font-size: 27px;
    color: $fontColor;
    letter-spacing: 2px;
    font-family: $fontTit;
  }

  .welcome-div,
  .wallet-connect-div {
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 5px;
    padding: 30px 15px;
    margin-bottom: 24px !important;
  }

  .wallet-connect-tit {
    font-size: 16px;
    color: $txtColor;
    line-height: 28px;
    font-family: $fontTxt;
    margin-bottom: 20px;
  }
  .wallet-error-txt {
    font-size: 16px;
    color: $errColor;
    line-height: 28px;
    font-family: $fontTxt;
    margin-top: 15px;
  }

  .wallet-btn {
    height: 80px;
    max-width: 480px;
    display: flex;
    justify-content: left;
    -webkit-box-align: center;
    align-items: center;
    background-color: $btnBg;
    box-shadow: $btnBoxShadow 0 5px 10px 5px;
    opacity: 1;
    cursor: pointer;
    border-radius: 2px;
    padding: 16px 10%;
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    transition: all 0.1s ease 0s;
  }
  .wallet-btn-name {
    margin: 0 0 0 16px;
    color: $btnFontColor;
    letter-spacing: 2px;
    font-family: $fontTit;
    text-transform: capitalize;
    font-size: 18px;
  }
  .wallet-btn-icon {
    height: 40px;
    width: 40px;
  }
}
</style>
