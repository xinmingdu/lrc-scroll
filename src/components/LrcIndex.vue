<script setup>
import { ref, onMounted } from 'vue'
const props = defineProps({
  lrc: {
    type: String,
    required: true
  }
})
/**
 * 解析歌词字符串
 * 得到一个歌词对象的数组
 * 每个歌词对象：
 * {time:开始时间, words: 歌词内容}
 *
 */
const parseLrc = () => {
  const result = []
  const lines = props.lrc.split('\n')
  lines.forEach(line => {
    const parts = line.split(']')
    const timeStr = parts[0].slice(1)
    const obj = {
      time: parseTime(timeStr),
      words: parts[1]
    }
    result.push(obj)
  })
  return result
}
/**
 * @description 时间字符串转换成数字(秒)
 * @param {String} timeStr
 * @return {Number}
 */
const parseTime = timeStr => {
  const parts = timeStr.split(':')
  return +parts[0] * 60 + +parts[1]
}

const lrcData = parseLrc()
/**
 * 计算出，在当前播放器播放到第几秒的情况下
 * lrcData数组中，应该高亮显示的歌词下标
 * 如果没有任何一句歌词需要显示，则得到-1
 */
const findIndex = currentTime => {
  for (let i = 0, length = lrcData.length; i < length; i++) {
    if (currentTime < lrcData[i].time) {
      return i - 1
    }
  }
  return lrcData.length - 1
}
// 容器高度
const container = ref(null)
let containerHeight = 0
// 每个 li 的高度
const lrcList = ref(null)
let liHeight = 0
// 最大偏移量
let maxOffset = 0
onMounted(() => {
  containerHeight = container.value.clientHeight
  liHeight = lrcList.value.children[0].clientHeight
  maxOffset = lrcList.value.clientHeight - containerHeight
})
/**
 * 设置 ul 元素的偏移量
 */
const setOffset = currentTime => {
  const index = findIndex(currentTime)
  let offset = liHeight * index + liHeight / 2 - containerHeight / 2
  if (offset < 0) offset = 0
  if (offset > maxOffset) offset = maxOffset
  lrcList.value.style.transform = `translateY(-${offset}px)`
  let li = lrcList.value.querySelector('.active')
  if (li) li.classList.remove('active')
  li = lrcList.value.children[index]
  if (li) li.classList.add('active')
}
defineExpose({
  setOffset
})
</script>
<template>
  <div class="container" ref="container">
    <ul class="lrc-list" ref="lrcList">
      <li v-for="(item, index) in lrcData" :key="index">{{ item.words }}</li>
    </ul>
  </div>
</template>
<style lang="less" scoped></style>
