<script setup>
import { ref } from 'vue'
import AudioIndex from '@/components/AudioIndex.vue'
import LrcIndex from '@/components/LrcIndex.vue'
import { lrc } from '@/assets/data.js'
const lrcRef = ref(null)
const getCurrentTime = currentTime => {
  lrcRef.value.setOffset(currentTime)
}
</script>

<template>
  <AudioIndex src="/mp3/music.mp3" @getCurrentTime="getCurrentTime" />
  <LrcIndex ref="lrcRef" :lrc="lrc" />
</template>
<style lang="less" scoped></style>
