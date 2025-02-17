<!-- 没用的部分 -->
<template>
  <div class="page-container">
    <div class="buttons">
      <!-- 使用 Element Plus 的按钮组件 -->
      <div class="arrow-btn left" @click="prevPage">
        <div class="flagleft">&lt;</div>
      </div>
      <!-- 自定义右箭头按钮 -->
      <div class="arrow-btn right" @click="nextPage">
        <div class="flagright">&gt;</div>
      </div>
    </div>
    <transition name="slide" mode="out-in">
      <component :is="currentPage" key="currentPage" />
    </transition>
  </div>
</template>

<script>
import { ref } from 'vue';

import SongTitlePopup from './SongTitlePopup.vue';
import FreqYearChange from './FreqYearChange.vue';
import AlbumScore from './AlbumScore.vue';
import LyricsNum from './LyricsNum.vue';
import TestPage from './HomePage.vue';


export default {
  components: {
    SongTitlePopup,
    FreqYearChange,
    AlbumScore,
    LyricsNum,
    TestPage,
  },
  setup() {
    const currentPage = ref('TestPage'); // 用来存储当前页面组件
    const pages = ['TestPage','SongTitlePopup', 'AlbumScore','LyricsNum','FreqYearChange']; // 假设有三个页面
    let currentPageIndex = 0; // 当前页面的索引

    const prevPage = () => {
      currentPageIndex = (currentPageIndex - 1 + pages.length) % pages.length;
      currentPage.value = pages[currentPageIndex];
    };

    const nextPage = () => {
      currentPageIndex = (currentPageIndex + 1) % pages.length;
      currentPage.value = pages[currentPageIndex];
    };

    return { currentPage, prevPage, nextPage };
  }
};
</script>

<style scoped>
  .page-container {
    position: relative;
    background-color: #fff6df;
  }

  .slide-enter-active, .slide-leave-active {
    transition: transform 0.5s ease;
  }

  .slide-enter, .slide-leave-to {
    transform: translateX(100%);
  }

  .slide-enter-to, .slide-leave {
    transform: translateX(0);
  }


/* 半圆形箭头按钮样式 */
  .arrow-btn {
    position: fixed;
    top: 50%;
    transform: translateY(-50%);
    width: 95px;
    height: 95px;
    background-color: #bdb7a7;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    font-size: 30px;
    font-weight: bolder;
    cursor: pointer;
    z-index: 999;
    transition: background-color 0.3s;
  }

  .arrow-btn:hover ,
  .arrow-btn:active {
    background-color: #a290e9;
  }

  .left {
    left: -47.5px; /* 将按钮定位到左边 */
  }
  .left:hover,
  .left:active{
    left:-45px;
  }

  .right {
    right: -47.5px; /* 将按钮定位到右边 */
  }
  .right:hover,
  .right:active {
    right:-45px;
  }

  .flagleft{
    padding-left:35px;
  }
  .flagright{
    padding-right: 35px;
  }

</style>