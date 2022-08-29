<template>
  <div class="h-100" style="background-color: #000 !important">
    <client-only>
      <Flipbook
        class="flipbook"
        :pages="pages"
        v-slot="flipbook"
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
            Comic book Phase 1
          </h2>

          <p style="font-family: 'Champange'; font-size: 22px" class="pb-3">
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris id
            interdum urna, ut egestas ex. Curabitur est ex, vehicula a dolor
            vitae, varius mattis turpis. Aliquam gravida ac lorem vel semper.
            Mauris hendrerit tortor ut nisi eleifend, sed condimentum metus
            sollicitudin. In interdum eros bibendum lectus molestie dictum.
            Quisque blandit, urna vel ullamcorper posuere, nunc dolor fermentum
            urna, non consequat arcu erat ac arcu.
          </p>

          <b-btn
            v-if="!initialState.account"
            @click="connectWallet"
            style="font-family: 'Market'; background-color: black"
            >Connect wallet</b-btn
          >

          <b-btn
            v-else-if="initialState.amount_holding > 0"
            @click="onEnterComic"
            style="font-family: 'Market'; background-color: black"
          >
            Enter The Comicbook
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
        "https://images.pexels.com/photos/4380970/pexels-photo-4380970.jpeg?auto=compress&cs=tinysrgb&dpr=3&h=750&w=1260",
        "https://images.pexels.com/photos/2709385/pexels-photo-2709385.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
        "https://images.pexels.com/photos/2709385/pexels-photo-2709385.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
        "https://images.pexels.com/photos/3081752/pexels-photo-3081752.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
        "https://images.pexels.com/photos/3083250/pexels-photo-3083250.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260",
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
    };
  },
  mounted() {
    this.$bvModal.show("wallet-modal");

    this.ethereum = window.ethereum;
    this.metamaskIsInstalled = this.ethereum && this.ethereum.isMetaMask;
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

.flipbook {
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
}
</style>
