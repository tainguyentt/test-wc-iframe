<script setup lang="ts">
import { ref } from 'vue';

const logs = ref("");

const log = (msg: string) => {
  logs.value += `${msg}\n`;
};
function simpleStringify (object: any){
  // stringify an object, avoiding circular structures
  // https://stackoverflow.com/a/31557814
  var simpleObject = {} as any;
  for (var prop in object ){
      if (!object.hasOwnProperty(prop)){
          continue;
      }
      if (typeof(object[prop]) == 'object'){
          continue;
      }
      if (typeof(object[prop]) == 'function'){
          continue;
      }
      simpleObject[prop] = object[prop];
  }
  return JSON.stringify(simpleObject); // returns cleaned up JSON
};

const connect = async () => {
  log("loading...\n")
  const myProvider = (window as unknown as { ethereum: { request: (params: { method: string, params?: any }) => Promise<void> } }).ethereum;
  if (!myProvider) throw new Error("Metamask not found");
  log("Metamask found " + simpleStringify(myProvider));
  const accounts = await myProvider.request({ method: "eth_accounts" });
  log("accounts found " + JSON.stringify(accounts));
  await myProvider.request({ method: "eth_requestAccounts" });
  log("connected to metamask");
  const accounts2 = await myProvider.request({ method: "eth_accounts" });
  log("accounts2 found " + JSON.stringify(accounts2));
  const signedMessage = await myProvider.request({
    method: "personal_sign",
    params: ["Hello world", (accounts as unknown as string[])[0]],
  });
  log("signedMessage " + signedMessage);
};
</script>

<template>
  <button @click="connect">Connect</button>
  <div>LOGS:</div>
  <pre>{{ logs }}</pre>
</template>
