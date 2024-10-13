<script setup lang="ts">
/**
 * @Author Rika
 * @Description 虚拟列表demo
 * @CreateData 2024/08/25
 */

// 模拟数据 十万条
const arrayData = Array.from({ length: 100000 }, (_, index) => index)

// 可视区信息
const visibleInfo = reactive({
  start: 0, // 起始index
  end: 0, // 结束index
  height: 0, // 可视区高度
})

// 元素高度
const itemHeight = ref(60)

// 虚拟列表高度
const phantomHeight = ref(0)

// 可视区数据
const visibleData = computed(() => {
  return arrayData.slice(visibleInfo.start, visibleInfo.end)
})

// 可视区渲染个数
const visibleCount = computed(() => {
  return Math.ceil(visibleInfo.height / itemHeight.value)
})

// 偏移量
const startOffset = ref(0)
// 偏移量对应的style：滚动后偏移多少子项元素 需要增补回来
const getTransform = computed(() => `translate3d(0,${startOffset.value}px,0)`)

// 容器实例
const containerRef = useTemplateRef<HTMLDivElement | null>('container')

function hdlScroll(event: Event) {
  const scrollTop = (event.target! as HTMLDivElement).scrollTop
  visibleInfo.start = Math.floor(scrollTop / itemHeight.value)
  visibleInfo.end = visibleInfo.start + visibleCount.value
  startOffset.value = visibleInfo.start * itemHeight.value
}

function initial() {
  // 初始化
  visibleInfo.height = containerRef.value!.clientHeight
  phantomHeight.value = arrayData.length * itemHeight.value
  visibleInfo.start = 0
  visibleInfo.end = visibleCount.value
}

onMounted(() => {
  initial()
})
</script>

<template>
  <div>列表容量： {{ arrayData.length }}</div>
  <div>列表高度： {{ phantomHeight }}</div>
  <div>Start {{ visibleInfo.start }} End {{ visibleInfo.end }}</div>
  <div>可视区渲染个数: {{ visibleCount }}</div>
  <div ref="container" class="w-[600px] h-[600px] scroll-smooth overflow-auto relative bg-cyan-400" @scroll="hdlScroll">
    <div :style="{ height: `${phantomHeight}px` }">
      <div class="absolute top-0 left-0 w-full bg-cyan-300" :style="{ transform: getTransform }">
        <div
          v-for="(item, index) in visibleData" :key="index"
          class="text-center text-gray-500 box-border border border-solid border-gray-300 bg-cyan-500"
          :style="{ height: `${itemHeight}px`, lineHeight: `${itemHeight}px` }"
        >
          {{ item }}
        </div>
      </div>
    </div>
  </div>
</template>
