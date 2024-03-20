<script setup lang="ts">
import Provider, { EthereumProvider } from '@walletconnect/ethereum-provider'
import { onMounted, ref } from 'vue';

const logs = ref("");

const log = (msg: unknown) => {
  logs.value += `${JSON.stringify(msg)}\n`;
};

var provider = ref<Provider>();
var session = ref<boolean>(false);

onMounted(async () => {
  provider.value = await EthereumProvider.init({
    projectId: 'e7a42e53358fcfe3456a9ea4d39ddcd0',
    showQrModal: true,
    chains: [1],
    methods: ['eth_sendTransaction', 'personal_sign'],
    events: ['connect', 'disconnect']
  })

  if (provider.value.session) {
    session.value = true;
  }

  provider.value.on('display_uri', handleURI)
});

function handleURI(uri: string){
  log(uri);
}

const connect = async () => {
  if (!provider.value) return;
  await provider.value!.connect();
  session.value = true;
};

const disconnect = async () => {
  if (!provider.value) return;
  await provider.value!.disconnect();
  session.value = false;
  logs.value = "";
};

const request = async () => {
  const addresses = await provider.value!.request({ method: 'eth_requestAccounts' });
  log(addresses);
};

const sign = async () => {
  const rawMessageSig = await provider.value!.request({
    method: "personal_sign",
    params: ["0x48656c6c6f20576562334d6f64616c", provider.value!.accounts[0]],
  });
  log(rawMessageSig);
};
</script>

<template>
  <h1>Wallet Connect</h1>
  <div v-if="session">
    <button @click="sign">Sign</button>
    <button @click="request">Request accounts</button>
    <button @click="disconnect">Disconnect</button>
  </div>
  <div v-else>
    <button @click="connect">Connect</button>
  </div>
  <br/>
  <div>Logs:</div>
  <pre>{{ logs }}</pre>
</template>
