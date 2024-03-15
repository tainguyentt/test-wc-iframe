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
  if (!myProvider) {
    log("Metamask not found");
    return;
  };
  log("Metamask found " + simpleStringify(myProvider));
  log("calling eth_accounts...");
  const accounts = await myProvider.request({ method: "eth_accounts" });
  log("accounts found " + JSON.stringify(accounts));
  log("calling eth_requestAccounts...");
  await myProvider.request({ method: "eth_requestAccounts" });
  log("connected to metamask");
  log("calling eth_accounts...");
  const accounts2 = await myProvider.request({ method: "eth_accounts" });
  log("accounts2 found " + JSON.stringify(accounts2));
};

const sign = async () => {
  log("loading...\n")
  const myProvider = (window as unknown as { ethereum: { request: (params: { method: string, params?: any }) => Promise<void> } }).ethereum;
  if (!myProvider) throw new Error("Metamask not found");
  const accounts2 = await myProvider.request({ method: "eth_accounts" });
  if (!(accounts2 as unknown as string[])[0]) {
    log("no account found");
    return;
  }
  
  log("calling personal_sign...");
  const signedMessage = await myProvider.request({
    method: "personal_sign",
    params: ["Hello world", (accounts2 as unknown as string[])[0]],
  });
  log("signedMessage " + signedMessage);
};
</script>

<template>
  <button @click="connect">Connect</button>
  <br />
  <br />
  <button @click="sign">Sign</button>
  <br />
  <br />
  <div>Logs:</div>
  <pre>{{ logs }}</pre>
</template>
