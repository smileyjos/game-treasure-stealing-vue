<template>
  <div class="connect-wallet-layout">
    <div class="container">
      <h1 class="align-center">Connect your wallet.</h1>
      <p class="mb-20 align-center">Connect with one of our available wallet providers or create a new one.</p>
      <div class="align-center">
        <!-- <el-button :loading="loading" @click="fakeConnect">Connect</el-button> -->
        <el-button class="connect-phantom-button" @click="connectPhantom">
          <img src="@/assets/icons/phantom.svg" height="20" />
        </el-button>
        <!-- <el-button @click="disconnectPhantom">Disconnect</el-button> -->
        <!-- <el-button @click="transferSOL">Transaction</el-button> -->
      </div>
    </div>
  </div>
</template>

<script>
import { mapActions, mapState } from 'vuex';
import MainMX from '@/mixins/MainMX';

export default {
  data(){
    return {
      loading: false,
      redirect: undefined,
      publicWalletAddress: ''
    }
  },

  mixins: [MainMX],

  computed: {
    isPhantom(){
      return solana?.isPhantom;
    },

    provider(){
      return this.getProvider();
    }
  },

  watch: {
    $route: {
      handler: function(route) {
        this.redirect = route.query && route.query.redirect
      },
      immediate: true
    }
  },

  mounted() {
    //this.getConnectInfo();
  },

  methods: {
    ...mapActions({
      login: 'users/login',
      connectWallet: 'users/connectWallet'
    }),

    async connectPhantom(){
      const provider = this.provider;
      try {
        const resp = await provider.connect();
        const wallet = resp.publicKey.toString();
        const signature = 'test123';
        this.connectWallet({wallet: wallet, signature: signature}).then( resp => {
          console.log(resp)
          this.$router.push({ path: this.redirect || '/' })
        }).catch(error => {
          console.log(error);
        })
      } catch (err) {

      }
    },

    async disconnectPhantom(){
      console.log(this.provider)
      this.provider.disconnect();
    },

    fakeConnect(){
      this.loading = true;
      this.login({email: '', password: ''}).then( res => {
        console.log(res)
        this.$router.push({ path: this.redirect || '/' })
        this.loading = false;
      }).catch( error => {
        console.log(error)
        this.loading = false;
      })
    }
  }
}
</script>
