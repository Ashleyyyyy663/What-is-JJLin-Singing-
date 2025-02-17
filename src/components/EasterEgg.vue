<template>
  <div @mousemove="dynamicDrawing" @resize="windowResized">
    <canvas ref="canvas"></canvas>
    <div class="instructions">试试在框内滑动鼠标</div>
  </div>
</template>

<script>
export default {
  name: "EasterEgg",
  data() {
    return {
      wordFreq: {
        '我们': 215, '自己': 124, '世界': 102, '没有': 100, '相信': 96, '知道': 82, '时间': 80, '感觉': 71, '不会': 64, '一切': 62,
        '如果': 60, '一起': 60, '因为': 60, '爱情': 57, '无法': 56, '一天': 55, '那么': 55, '一直': 53, '可以': 52, '还是': 52,
        '幸福': 52, '快乐': 51, '永远': 51, '所有': 51, '开始': 50, '未来': 49, '时候': 48, '明白': 47, '需要': 47, '寂寞': 47,
        '不能': 44, '想要': 44, '温柔': 44, '眼泪': 43, '原来': 42, '希望': 41, '真的': 40, '不要': 40, '沉默': 40, '也许': 39,
        '是否': 39, '等待': 39, '只要': 38, '一种': 36, '这样': 36, '身边': 36, '微笑': 36, '故事': 35, '回忆': 34,
        '最后': 34, '我会': 33, '选择': 33, '拥抱': 32, '慢慢': 32, '不到': 31, '黑暗': 31, '生命': 31, '地方': 31, '多少': 30,
        '一点': 30, '存在': 30, '音乐': 29, '心跳': 29, '完美': 29, '明天': 29, '离开': 29, '那些': 28, '这个': 28,
        '一样': 28, '以后': 28, '一定': 28, '一次': 28, '不了': 28, '不想': 28, '拥有': 27, '冻结': 27, '几个': 27,
        '孤单': 27, '勇敢': 27, '为什么': 27, '命运': 27, '记得': 27, '就算': 27, '感动': 26, '他们': 26, '不管': 25,
        '一秒': 25, '不再': 25, '不怕': 25, '变成': 25, '感受': 24, '执着': 24, '眼睛': 24,
        '失去': 24, '披上': 23, '回到': 23, '看见': 23, '生活': 23, '方向': 23, '努力': 23, '人生': 23,
        '还有': 23, '伤口': 23, '心中': 23, '喜欢': 23, '此刻': 23, '放弃': 23, '眼神': 22,
        '每个': 22, '最好': 22, '渴望': 22, '思念': 22, '不同': 22, '自由': 22, '天空': 22, '就让': 22, '地球': 21, '到底': 21,
        '心里': 21, '发现': 21, '现在': 21, '遗忘': 20, '情绪': 20, '曾经': 20,
        '迎面': 20, '直击': 20, '害怕': 20, '只有': 20, '呼吸': 20,
      },
      letters: '我们 自己 世界 没有 相信 知道 时间 感觉 不会 一切 如果 一起 因为 爱情 无法 一天 那么 一直 可以 还是 幸福 快乐 永远 所有 开始 未来 时候 明白 需要 寂寞 不能 想要 温柔 眼泪 原来 希望 真的 不要 沉默 也许 是否 等待 只要 一种 这样 身边 微笑 故事 回忆 最后 我会 选择 拥抱 慢慢 不到 黑暗 生命 地方 多少 一点 存在 音乐 心跳 完美 明天 离开 那些 这个 一样 以后 一定 一次 不了 不想 拥有 冻结 几个 孤单 勇敢 为什么 命运 记得 就算 感动 他们 不管 一秒 不再 不怕 变成 感受 执着 眼睛 失去 披上 回到 看见 生活 方向 努力 人生 还有 伤口 心中 喜欢 此刻 放弃 眼神 每个 最好 渴望 思念 不同 自由 天空 就让 地球 到底 心里 发现 现在 遗忘 情绪 曾经 迎面 直击 害怕 只有 呼吸',
      fontSizeMin: 12,
      fontSizeMax: 40,
      x: 0,
      y: 0,
      counter: 0,
      stepSize: 5.0,
      boxX: 0,
      boxY: 0,
      boxWidth: 0,
      boxHeight: 0,
    };
  },
  mounted() {
    this.setup();
    window.addEventListener('resize', this.windowResized);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.windowResized);
  },
  methods: {
    setup() {
      const canvas = this.$refs.canvas;
      canvas.width = window.innerWidth-30;
      canvas.height = window.innerHeight-30;
      this.boxX = canvas.width * 0.06;
      this.boxY = canvas.height * 0.15;
      this.boxWidth = canvas.width * 0.895;
      this.boxHeight = canvas.height * 0.80;
      this.x = canvas.width / 8;
      this.y = canvas.height / 5;
      this.drawBox();
      this.drawInstructions();
    },

    dynamicDrawing(event) {
      const canvas = this.$refs.canvas;
      const mouseX = event.clientX;
      const mouseY = event.clientY;

      if (mouseX > this.boxX && mouseX < this.boxX + this.boxWidth && mouseY > this.boxY && mouseY < this.boxY + this.boxHeight) {
        const d = Math.hypot(this.x - mouseX, this.y - mouseY);
        const fontSize = Math.min(Math.max(this.fontSizeMin + d / 4, this.fontSizeMin), this.fontSizeMax);
        const ctx = canvas.getContext('2d');
        ctx.font = `${fontSize}px Arial`;
        const newLetter = this.letters.charAt(this.counter);
        this.stepSize = ctx.measureText(newLetter).width;

        if (d > this.stepSize) {
          const angle = Math.atan2(mouseY - this.y, mouseX - this.x);
          ctx.save();
          ctx.translate(this.x, this.y);
          ctx.rotate(angle);
          ctx.fillStyle = `rgb(${Math.random() * 155 + 100}, ${Math.random() * 155 + 100}, ${Math.random() * 155 + 100})`;
          ctx.fillText(newLetter, 0, 0);
          ctx.restore();

          this.counter++;
          if (this.counter > this.letters.length - 1) this.counter = 0;

          this.x += Math.cos(angle) * this.stepSize;
          this.y += Math.sin(angle) * this.stepSize;
        }
      }
    },

    drawBox() {
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');
      ctx.strokeStyle = 'gray';
      ctx.lineWidth = 0.5; // 设置更细的线宽
      ctx.setLineDash([4, 4]); // 调整虚线的长度和间隔
      ctx.strokeRect(this.boxX, this.boxY, this.boxWidth, this.boxHeight);
      ctx.setLineDash([]);
    },

    drawInstructions() {
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');
      ctx.fillStyle = 'black';
      ctx.font = '16px Arial';
      ctx.textAlign = 'center';
      // ctx.fillText('试试在框内滑动鼠标', this.boxX + this.boxWidth / 2, this.boxY - 20);
    },

    windowResized() {
      this.setup();
    },
  },
};
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
  background-color: rgb(255, 246, 223);
}

canvas {
  display: block;
}

.instructions {
  position: absolute;
  top: 60px;
  left: 50%;
  transform: translateX(-50%);
  color: black;
  font-size: 24px;
  text-align: center;
}
</style>
