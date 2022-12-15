<template>
  <div style="background-color: #000 !important; height: 100vh">
     <client-only>
        <Flipbook
           class="flipbook"
           :zooms="zooms"
           :pages="pages"
           ref="flipbook"
           >
        </Flipbook>
     </client-only>
     <b-modal
        id="wallet-modal"
        centered
        hide-header
        hide-header-close
        hide-footer
        no-close-on-backdrop
        no-close-on-esc
        size="lg"
        >
        <b-container>
           <div class="text-center">
              <h2 class="py-3" style="font-family: 'Market'; font-size: 42px">
                 Comic book Vol.1
              </h2>
              <p style="font-family: 'Champange'; font-size: 22px" class="pb-1">
                "This is a story about coping with what life throws at you. It is my outlet, 
                allowing me to channel my emotions through the trauma and keep my mental health in check throughout uncertainty.
              </p>
              <p style="font-family: 'Champange'; font-size: 22px" class="pb-1">
                It is what inspired the images for the project ‘The Heist’.
              </p>
              <p style="font-family: 'Champange'; font-size: 22px" class="pb-1">
                What was the trigger? Well, welcome to Volume 1.
              </p>

              <p style="font-family: 'Champange'; font-size: 22px" class="pb-1">
                The many masks we all wear, and conversations we have at different times of our lives based on what we are going through, yet… it is always ‘us’ on the inside.” - God
              </p>

              <p style="font-family: 'Market'; font-size: 22px" class="pb-1">
                A WOLF IN SHEEP’S CLOTHING.
              </p>

              <p style="font-family: 'Champange'; font-size: 12px" class="pb-3">
                *Please note: This digital Comic Book is property and copyright of MGMS. As a holder of The Heist NFT you have access to view this Comic Book, and will be subject to special benefits derived and related to it. Owning a Heist NFT does not give the right to reproduce, screenshot, or share on social media or otherwise.
              </p>
              <b-btn
                 v-if="!initialState.account"
                 @click="connectWallet"
                 style="font-family: 'Market'; background-color: black; font-size:26;"
                 >Connect wallet</b-btn
                 >
              <b-btn
                 v-else-if="initialState.amount_holding > 0"
                 @click="onEnterComic"
                 style="font-family: 'Market'; background-color: black; font-size:26;"
                 >
                 Enter
              </b-btn>
              <h2
                 v-else-if="initialState.amount_holding == 0"
                 style="font-family: 'Market'"
                 >
                 Must be a holder to enter.
              </h2>
              <div v-else>
                 <b-spinner label="Spinning"></b-spinner>
              </div>
           </div>
        </b-container>
     </b-modal>
     
  </div>
</template>

<script>
import abi from "~/static/abi.json";
import config from "~/static/config.json";
import Web3EthContract from "web3-eth-contract";
import Web3 from "web3";
export default {
  name: "IndexPage",
  data() {
    return {
      pages: [
        null,
        "/images/0.png",
        "/images/1.png",
        "/images/2.png",
        "/images/3.png",
        "/images/4.png",
        "/images/5.png",
        "/images/6.png",
        "/images/7.png",
        "/images/8.png",
        "/images/9.png",
        "/images/10.png",
        "/images/11.png",
        "/images/12.png",
        "/images/13.png",
        "/images/14.png",
        "/images/15.png",
        "/images/16.png",
        "/images/17.png",
        "/images/18.png",
        "/images/19.png",
        "/images/20.png",
        "/images/21.png",
        "/images/22.png",
        "/images/23.png",
        "/images/24.png",
        "/images/25.png",
        "/images/26.png",
        "/images/27.png",
        "/images/28.png",
        "/images/29.png",
        "/images/30.png",
        "/images/31.png",
        null
      ],
      ethereum: null,
      metamaskIsInstalled: false,
      initialState: {
        loading: false,
        account: null,
        amount_holding: null,
        truncated_account: null,
        smartContract: null,
        web3: null,
        errorMsg: "",
      },
      zooms: [1]
    };
  },
  mounted() {
    this.$bvModal.show("wallet-modal");

    this.ethereum = window.ethereum;
    this.metamaskIsInstalled = this.ethereum && this.ethereum.isMetaMask;

    document.addEventListener('contextmenu', event => event.preventDefault());
  },
  methods: {
    async connectWallet() {
      if (this.metamaskIsInstalled) {
        Web3EthContract.setProvider(ethereum);
        this.initialState.web3 = new Web3(ethereum);

        try {
          const accounts = await this.ethereum.request({
            method: "eth_requestAccounts",
          });
          const networkId = await this.ethereum.request({
            method: "net_version",
          });

          this.initialState.account = accounts[0];

          this.initialState.truncated_account =
            this.initialState.account.substring(38);

          if (networkId == config.NETWORK.ID) {
            const SmartContractObj = new Web3EthContract(
              abi,
              config.CONTRACT_ADDRESS
            );

            this.initialState.smartContract = SmartContractObj;

            await this.getBalance().then((amount) => {
              setTimeout(() => {
                this.initialState.amount_holding = amount;
              }, 1000);
            });
          } else {
            alert("Change to the correct network.");
          }
        } catch (e) {
          console.error(e);
        }
      } else {
        alert("metamask is not installed.");
      }
    },

    async getBalance() {
      const response = await this.initialState.smartContract.methods.balanceOf(
        this.initialState.account
      );

      let call = await response.call();

      return parseInt(call);
    },

    onEnterComic() {
      this.$bvModal.hide("wallet-modal");
    },
  },
};
</script>
<style>
@font-face {
  font-family: "Champange";
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url("~assets/fonts/Champagne.ttf") format("truetype");
}

@font-face {
  font-family: "Market";
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url("~assets/fonts/Market.ttf") format("truetype");
}

@font-face {
  font-family: "WordOfGod";
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url("~assets/fonts/WordOfGod.ttf") format("truetype");
}


.flipbook {
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
}
</style>
