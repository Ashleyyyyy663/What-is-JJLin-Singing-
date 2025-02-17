<template>
  <div class="container">
    <!-- <h2>林 俊 杰 在 唱 什 么 ？</h2> -->
    <transition
      :name="transitionName"
      mode="out-in"
    >
      <component :is="currentPage" :key="currentPage" />
    </transition>

    <!-- 左右切换按钮 -->
    <div class="arrow-btn left" @click="goLeft">
      <div class="flagleft">&lt;</div>
    </div>
    <div class="arrow-btn right" @click="goRight">
      <div class="flagright">&gt;</div>
    </div>
  </div>
</template>



<script>

import SongTitlePopup from './SongTitlePopup.vue';
import FreqYearChange from './FreqYearChange.vue';
import AlbumScore from './AlbumScore.vue';
import LyricsNum from './LyricsNum.vue';
import EasterEgg from './EasterEgg.vue';
import HomePage from './HomePage.vue';
import EmotionalAnalysis from './EmotionalAnalysis.vue';
import WordFreq from './WordFreq.vue';
import ComposeRelation from './ComposeRelation.vue';

export default {
  components: {
    SongTitlePopup,
    FreqYearChange,
    AlbumScore,
    LyricsNum,
    EasterEgg,
    EmotionalAnalysis,
    WordFreq,
    HomePage,
    ComposeRelation,
  },
  data() {
    return {
      pages: ['HomePage','SongTitlePopup', 'AlbumScore','LyricsNum','FreqYearChange','WordFreq','ComposeRelation','EmotionalAnalysis','EasterEgg'],  // 示例页面组件
      currentIndex: 0,  // 当前页面的索引
      direction: '',  // 控制动画方向的变量
    };
  },
  computed: {
    currentPage() {
      return this.pages[this.currentIndex];  // 根据索引获取当前页面组件
    },
    transitionName() {
      return this.direction === 'left' ? 'slide-left' : 'slide-right';  // 根据方向动态决定过渡效果
    }
  },
  methods: {
    goLeft() {
      // 切换到上一页，并循环
      this.direction = 'left';  // 设置为左滑的方向
      this.currentIndex = (this.currentIndex - 1 + this.pages.length) % this.pages.length;
    },
    goRight() {
      // 切换到下一页，并循环
      this.direction = 'right';  // 设置为右滑的方向
      this.currentIndex = (this.currentIndex + 1) % this.pages.length;
    }
  }
};
</script>

<style scoped>
  .container {
    position: relative;
    background-color: #fff6df;
    margin-top:20px;
  }

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

  .arrow-btn:hover {
    background-color: #a290e9;
  }
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
  /* 向左滑动的动画 */
  .slide-left-enter-active, .slide-left-leave-active {
    transition: transform 0.5s ease;
  }

  .slide-left-enter, .slide-left-leave-to {
    transform: translateX(100%);  /* 离开时向右滑出，进入时从右滑入 */
  }

  .slide-left-enter-to, .slide-left-leave {
    transform: translateX(0);  /* 结束位置：0，代表完全显示 */
  }

  /* 向右滑动的动画 */
  .slide-right-enter-active, .slide-right-leave-active {
    transition: transform 0.5s ease;
  }

  .slide-right-enter, .slide-right-leave-to {
    transform: translateX(-100%);  /* 离开时向左滑出，进入时从左滑入 */
  }

  .slide-right-enter-to, .slide-right-leave {
    transform: translateX(0);  /* 结束位置：0，代表完全显示 */
  }
</style>
