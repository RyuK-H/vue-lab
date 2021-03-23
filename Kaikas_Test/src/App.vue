<template>
  <div>
    <img alt="Vue logo" src="./assets/logo.png">
    <button :onClick="connect">Connect KaiKas</button>
    <p>{{address}}</p>
    <button :onClick="sendKlay">Send Klay</button>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      kaikas: null,
      address: ""
    }
  },
  methods: {
    connect() {
      if (typeof window.klaytn !== 'undefined') {
        this.kaikas = window['klaytn'];

        if(this.kaikas) {
          (async () => {
            try {
              const accounts = await this.kaikas.enable();
              this.address = accounts[0];

              await this.kaikas.on('accountsChanged', (accounts) => {
                this.address = accounts;
                console.warn("주소 변경 알림", accounts);
              })
            } catch (error) {
              //error
            }
          })();
        } else {
          // 없어영 설치하러 가세엿!
        }
      }
    },
    sendKlay() {
      if(this.kaikas) {
        const transactionParameters = {
          gas: '0x2710',
          to: '0x0000000000000000000000000000000000000000',
          from: this.kaikas.selectedAddress,
          value: '0xff'
        }

        this.kaikas.sendAsync(
          {
            method: 'klay_sendTransaction',
            params: [transactionParameters],
            from: this.kaikas.selectedAddress
          },
          () => console.log("굳")
        )
      }
    },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
