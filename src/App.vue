<template>
  <div id="app">
    <h1>
      Byteduino keys generator
    </h1>
    <div class="content">
      <button @click="generateRandomWallet">
        Generate new keys
      </button>
      <p>
        If you want to run the script locally you can download the files
        <a href="https://github.com/Papabyte/Byteduino-keys-generator/tree/master/docs" target="_blank">here</a>.
        This script will work even without internet.
      </p>
      <div v-show="seed">
        <b>Seed</b>
        <p>{{seed}}</p>
        <b>Config</b>
        <p>{{config}}</p>
        <b>Pairing code</b>
        <p>{{pairing_code}}</p>
      </div>
    </div>
  </div>
</template>

<script>
const Mnemonic = require('bitcore-mnemonic');
const Bitcore = require('bitcore-lib');
const ecdsa = require('secp256k1');

export default {
  name: 'app',
  data() {
    return {
      env: 'livenet',
      seed: null,
      config: null,
      pairing_code: null
    };
  },
  methods: {
    generateRandomWallet() {
      let mnemonic = new Mnemonic();
      while (!Mnemonic.isValid(mnemonic.toString())) {
        mnemonic = new Mnemonic();
      }
      this.seed = mnemonic.phrase;
      const xPrivKey = mnemonic.toHDPrivateKey();
      this.config = "setPrivateKeyM1(\"" + xPrivKey.derive("m/1'").privateKey.bn.toBuffer({size:32}).toString('base64')+ "\");" 
	+ "setExtPubKey(\""+ Bitcore.HDPublicKey(xPrivKey.derive("m/44'/0'/0'")).toString('base64')+ "\");"
	+ "setPrivateKeyM4400(\"" + xPrivKey.derive("m/44'/0'/0'/0/0").privateKey.bn.toBuffer({size:32}).toString('base64')+ "\");";
	this.pairing_code = ecdsa.publicKeyCreate(xPrivKey.derive("m/1'").privateKey.bn.toBuffer({size:32}), true).toString('base64')+"@byteball.org/bb#0000 for mainnet";
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  max-width: 800px;
  margin: 60px auto;
}

.content {
  text-align: left;
}

input, textarea, select, button {
  font-size: 14px;
  width: 100%;
  border: 1px solid #ccc;
  margin: 4px 0 16px;
  padding: 4px 8px;
  border-radius: 4px;
}

button {
  outline: 0;
  padding: 8px;
  cursor: pointer;
}

p {
  margin: 4px 0 16px;
}
</style>
