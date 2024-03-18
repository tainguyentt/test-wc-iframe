<script setup lang="ts">

import { ref, onMounted } from 'vue';

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

onMounted(() => {
  log("begining, print window, " + simpleStringify(window as Object));
  log("begining, print window.deficonnect, " +  simpleStringify((window as any).deficonnect as Object));
});

</script>

<template>
  <h1>Embedded Wallet</h1>
  <div>Logs (outside iframe):</div>
  <pre>{{ logs }}</pre>
  <iframe src="https://test-wallet-1.vercel.app" width="100%" height="500px"></iframe>
</template>
