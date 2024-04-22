<script setup>
import HelloWorld from './components/HelloWorld.vue'
import { onMounted } from 'vue';

function setCrossOriginHeaders() {
  // 创建 meta 元素
  const coopMeta = document.createElement('meta');
  coopMeta.setAttribute('http-equiv', 'Cross-Origin-Opener-Policy');
  coopMeta.setAttribute('content', 'same-origin');
  document.head.appendChild(coopMeta);

  const coepMeta = document.createElement('meta');
  coepMeta.setAttribute('http-equiv', 'Cross-Origin-Embedder-Policy');
  coepMeta.setAttribute('content', 'require-corp');
  document.head.appendChild(coepMeta);
}
onMounted(() => {
});
</script>

<template>
  <n-message-provider>
    <HelloWorld msg="Vite + Vue" />
  </n-message-provider>
</template>

<style scoped></style>
