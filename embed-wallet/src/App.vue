<script setup lang="ts">

import { ref, onMounted } from 'vue';

const logs = ref("");

const log = (msg: string) => {
  logs.value += `${msg}\n`;
};

onMounted(() => {
  log("begining, print window, ", window);
  log("begining, print deficonnect, ", deficonnect);
  log("begining, print window.deficonnect, ",  window.deficonnect);
});

</script>

<template>
  <h1>Embedded Wallet</h1>
  <div>Logs (outside iframe):</div>
  <pre>{{ logs }}</pre>
  <iframe src="https://test-wallet-1.vercel.app" width="100%" height="500px"></iframe>
</template>
