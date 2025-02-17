<template>
  <div class="container">
    <!-- 背景容器，包含21张专辑图片 -->
    <div class="background-container">
      <div v-for="i in 21" :key="i" class="album" :style="getBackgroundStyle(i)"></div>
    </div>

    <!-- 中间文字 -->
    <div class="text">林俊杰<br>在<span class="spe">唱什么?</span></div>
  </div>
</template>

<script>
export default {
  name: "HomePage",
  methods: {
    // 动态生成专辑背景图片的样式
    preloadImages() {
    for (let i = 1; i <= 21; i++) {
      const img = new Image();
      img.src = `/images/${i}.jpg`;  // 使用 public 目录下的图片路径
    }
    },
    getBackgroundStyle(index) {
    const imageIndex = index <= 21 ? index : index % 21;
    return {
      backgroundImage: `url('/images/${imageIndex}.jpg')`  // 使用 public 目录下的图片路径
    };
  }
  }
};
</script>

<style scoped>
/* 设置背景为专辑图片的排列 */
.container {
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  /* margin-top:10px; */
  height: 95vh;
  background-color: #fff6df;
  font-family: '方正兰亭大黑_GBK';
  overflow: hidden;
}

.background-container {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  grid-template-rows: repeat(3, 1fr);
  gap: 10px;
  width: 100vw;
  height: 100vh;
}

/* 设置文字样式 */
.text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 7em;
  font-weight: bold;
  color: #a290e9;
  text-align: center;
  letter-spacing: 0.1em;
  text-shadow: 5px 2px 2px rgba(255, 255, 255);
  /* text-shadow:0 2px white, 2px 0 white, -2px 0 white, 0 -2px white; */
  /* mix-blend-mode:multiply; */

}

.album {
  width: 100%;
  height: 100%;
  background-size: cover;
  background-position: center;
  filter: grayscale(80%);
  opacity: 0.3; /*降低透明度，使颜色不那么鲜艳，整体更和谐*/
}
/* .spe{
    animation: fadeEffect 5s forwards;
    -webkit-text-stroke: 2px black;
    text-shadow: none; 
    background: linear-gradient(to right, #a290e9, transparent); 
    background-clip: text;
    -webkit-background-clip: text; 
    color: transparent; 
}
@keyframes fadeEffect {
    0% {
      color: #a290e9;
    }

    100% {
      color: transparent;
    }
} */
</style>
