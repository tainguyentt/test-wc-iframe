<script setup lang="ts">
import Provider, { EthereumProvider } from '@walletconnect/ethereum-provider'
import { onMounted, ref } from 'vue';

const logs = ref("");

const log = (msg: unknown) => {
  logs.value += `${JSON.stringify(msg)}\n`;
};

var provider = ref<Provider>();
onMounted(async () => {
  provider.value = await EthereumProvider.init({
    projectId: 'e7a42e53358fcfe3456a9ea4d39ddcd0',
    metadata: {
      name: 'My Website',
      description: 'My Website Description',
      url: window.location.origin, // origin must match your domain & subdomain
      icons: ['https://avatars.githubusercontent.com/u/37784886']
    },
    showQrModal: true,
    optionalChains: [1, 137, 2020],
  })

  provider.value.on('display_uri', handleURI)
});

function handleURI(uri: string){
  log(uri);
}

const connect = async () => provider.value!.connect();
const request = async () => {
  const addresses = await provider.value!.request({ method: 'eth_requestAccounts' });
  log(addresses);
};
const sign = async () => {
  const addresses = await provider.value!.request({ method: 'eth_requestAccounts' });
  const rawMessageSig = await provider.value!.request({
    method: "personal_sign",
    params: ["Hello World", (addresses as string[])[0]],
  });
  log(rawMessageSig);
};
</script>

<template>
  <button @click="connect">Connect</button>
  <br />
  <br />
  <button @click="request">Request</button>
  <br />
  <br />
  <button @click="sign">Sign</button>
  <br />
  <br />
  <div>Logs:</div>
  <pre>{{ logs }}</pre>
</template>
