<template>
  <button @click="exportHtml">Export</button>
  <Unlayer ref="unlayerRef" style="height: calc(100vh - 16px)" :user="1111" />
</template>

<script setup>
import { ref } from 'vue'
import Unlayer from '@/components/Unlayer.vue'

const unlayerRef = ref(null)

function exportHtml() {
  const data = unlayerRef.value.exportHtml()
  if (Object.keys(data ?? {}).length) console.log(data)
}
</script>
