<script setup>
import { ref } from 'vue'
defineProps({
  src: {
    type: String,
    required: true
  }
})
const emit = defineEmits(['getCurrentTime'])
const audio = ref(null)
const timeupdate = () => {
  emit('getCurrentTime', audio.value.currentTime)
}
</script>
<template>
  <audio ref="audio" :src="src" controls @timeupdate="timeupdate"></audio>
</template>

<style lang="less" scoped></style>
