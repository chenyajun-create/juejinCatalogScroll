<script setup>
import { ref } from 'vue'
// 循环生成数据结构
const catalogTitle = ref([])
let i = 0
for (let index = 0; index < 12; index++) {
  if (i >= 6) {
    i = 0
  }
  catalogTitle.value.push({
    title: `文章题目${i + 1}`, //标题
    id: `${i + 1}`,
    level: `${i + 1}`, //层级
    left: (i + 1) * 15 + 'px', //左边距
  })
  i++
}

const canRun = ref(true) //节流 防止多次执行滚动事件
function handleScroll() {
  // 内容区滚动函数
  if (canRun.value) {
    canRun.value = false
    scrollElement() //滚动元素
    setTimeout(() => {
      canRun.value = true
    }, 100)
  }
}
const scrollContainer = ref(null) //内容区滚动容器元素
const catalogContent = ref(null) //标题加内容区目录元素
const currentCatalog = ref(0) //当前目录索引
const catalogBox = ref(null) //目录容器
function scrollElement() {
  const distance = Math.abs(scrollContainer.value.getBoundingClientRect().top) + 170 //增加一些距离目的为了快滚到时就激活对应目录
  catalogContent.value.forEach((el, index) => {
    // 如果容器的滚动距离 大于或等于标题距离ul顶部的距离，就说明当前标题已经触顶
    if (distance >= el.offsetTop) {
      currentCatalog.value = index
    }
  })
  setCatalogScroll()
}
function setCatalogScroll() {
  // 设置目录滚动距离
  if (currentCatalog.value >= 3) {
    catalogBox.value.scrollTo({
      top: (currentCatalog.value - 3) * 65,
    })
  } else {
    catalogBox.value.scrollTo({
      top: 0,
    })
  }
}
function jumpToCatalog(index) {
  // 点击跳转当前目录
  currentCatalog.value = index
  setCatalogScroll()
  catalogContent.value[index].scrollIntoView()
}
</script>
<template>
  <div class="outer" @scroll="handleScroll">
    <!-- 内容区 -->
    <div class="catalog-content" ref="scrollContainer">
      <div ref="catalogContent" v-for="(item, index) in catalogTitle" :key="item.id">
        <h1 v-if="item.level === '1'">{{ item.title }}</h1>
        <h2 v-if="item.level === '2'">{{ item.title }}</h2>
        <h3 v-if="item.level === '3'">{{ item.title }}</h3>
        <h4 v-if="item.level === '4'">{{ item.title }}</h4>
        <h5 v-if="item.level === '5'">{{ item.title }}</h5>
        <h6 v-if="item.level === '6'">{{ item.title }}</h6>
        <div class="catalog-message"></div>
      </div>
      <div style="height: 1000px"></div>
    </div>

    <!-- 目录区 -->
    <div class="catalog-name">
      <div class="catalog-name-title">目录</div>
      <div ref="catalogBox" class="catalog-box">
        <div
          v-for="(item, index) in catalogTitle"
          :data-index="index"
          :key="item.id"
          :style="{
            marginLeft: item.left,
            color: currentCatalog === index ? '#1e80ff' : '#000000',
            marginTop: index === 0 ? '10px' : '30px',
          }"
          class="catalog-title"
          @click="jumpToCatalog(index)"
        >
          {{ item.title }}
        </div>
      </div>
    </div>
  </div>
</template>
<style lang="scss" scoped>
html,
body {
  margin: 0;
  height: 100%;
}
$i: 7;
@while $i > 0 {
  h#{$i} {
    margin-left: 10px;
  }
  $i: $i - 1;
}
.outer {
  width: 100%;
  height: 100%;
  position: relative;
  background-color: #f2f3f5;
  display: flex;
  justify-content: center;
  overflow-y: scroll;
}
.catalog-content {
  display: inline-block;
  background-color: #ffffff;
  width: 800px;
  border-radius: 10px;
  margin-top: 20px;
  .catalog-message {
    height: 400px;
    background-color: #bfa;
  }
}
.catalog-name {
  position: fixed;
  right: 20px;
  top: 20px;
  width: 20%;
  min-width: 200px;
  border-radius: 10px;
  background-color: #ffffff;
  .catalog-box {
    max-height: 312px;
    overflow-y: auto;
    .catalog-title {
      margin-left: 15px;
      margin-bottom: 15px;
      &:hover {
        color: #1e80ff;
        cursor: pointer;
      }
    }
  }

  .catalog-name-title {
    font-weight: 500;
    margin-top: 15px;
    margin-left: 15px;
    margin-bottom: 35px;
    position: relative;
    &:after {
      content: '';
      width: 95%;
      position: absolute;
      height: 1px;
      top: 32px;
      left: 0;
      background-color: #e4e6eb;
    }
  }
}
.catalog-box::-webkit-scrollbar {
  display: none;
}
</style>
