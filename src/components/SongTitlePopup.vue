<template>
  <div>
    <div>
      <canvas id="canvas"></canvas>
    </div>
  </div>
</template>

<script>
import { onMounted, onUnmounted } from 'vue'; // 导入 Vue 的生命周期钩子 onMounted

export default {
  name: 'SongTitlePopup',

  setup() {
    let p5Instance = null; // 保存 p5 实例
    let words = []; // 用于存储生成的单词
    let usedWords = []; // 用于存储已使用的单词
    let startTime; // 动画开始时间
    let duration = 4500; // 整个效果持续的时间
    let wordCount = 0; // 已生成单词计数
    const margin = 15; // 最小屏幕边距


    const setup = () => {
      const p5 = require('p5');

      p5Instance = new p5((p) => {
        // p5.js 初始化设置
        p.setup = function () {
          p.createCanvas(window.innerWidth-40, window.innerHeight-100); // 创建画布
          p.background(255, 246, 223); // 设置背景色
          p.noFill(); // 不填充图形
          p.strokeWeight(3); // 设置描边宽度
          p.textSize(30); // 设置文字大小
          p.frameRate(20); // 设置帧率
          startTime = p.millis(); // 获取当前时间作为动画开始时间
        };

        // p5.js 绘制循环
        p.draw = function () {
          let currentTime = p.millis(); // 获取当前时间

          // 如果动画时间未结束
          if (currentTime - startTime < duration) {
            let newWord = generateWord(p); // 生成新的单词
            if (newWord) {
              words.push(newWord); // 将新单词添加到单词列表
            }
          }

          // 绘制每个单词
          words.forEach((word) => {
            drawWord(p, word); // 绘制单词
          });
        };

        const generateWord = (p) => {
          const availableWords = [
          'The Show','愿与愁','逆光白','孤独娱乐','梦不凌乱','自画像','谢幕','如果我还剩一件事情可以做','黑色泡沫','你都在','一时的选择',
          'Castle in the air','7千3百多天','In The Joy','Intro','Too Bad','身后','想见你想见你想见你','恨幸福来过','慢慢喜欢你',
          'You Are The Reason','咖啡+交换余生','圣所','生生','我还年轻 我还年轻','你啊你啊','那女孩对我说','落在生命里的光','是你','达尔文',
          '无拘','From The Ashes','At Least I Had You',"You're My Superhero",'Positive Procrastination','Help!','Better With You',
          '你還不知道?','A Day At A Time','一定会','After The Rain','裹着心的光','While I Can','Like You Do','Not Tonight',
          'All In Your Mind','Head On','Steady My Love','过','最向往的地方','交换余生','幸存者','离开的那一些','最好是','暂时的记号',
          '背对背拥抱','As I Believe','Wonderland','将故事写成我们','对的时间点','我们很好','进阶','飞云之下','Despacito 缓缓','伟大的渺小',
          '穿越','四点四十四','我继续','剪云者','黑夜问白天','丹宁执着','身为风帆','小瓶子','Until the Day','Until The Day',
          '2infinity And Beyond','超越无限','调音','不为谁而作的歌','中场休息','关键词','弹唱','有梦不难','Welcome to the Livehouse',
          '12年前','现在的我和她','序曲：海边 初','Lier and Accuser','独舞','序曲：海边 终','你，有没有过','只要有你的地方','Dreams',
          '全面开战','Our Singapore','I Pray For You','回','新地球','水仙','浪漫血液','黑键','手心的蔷薇','可惜没如果','I Am Alive',
          '爱的鼓励','茉莉雨','Lamando','Checkmate','因你而在','零度的亲吻','黑暗骑士','修炼爱情','飞机','巴洛克先生','One Shot',
          '裂缝中的阳光','友人说','十秒的冲动','以后要做的事','一千年后，记得我','翅膀','不懂','江南','害怕','一千年以后','简简单单',
          '原来','西界','不流泪的机场','小酒窝','爱与希望','第几个100天', '她说','爱笑的眼睛','就是我','会读书','第二天堂','子弹列车',
          '编号89757','木乃伊','曹操','波间带','杀手','不潮不用花钱','街道','期待爱','X','加油！','跟屁虫','独奏','学不会','故事细腻',
          '那些你很冒险的梦','白羊梦','灵魂的共鸣','We Together','Cinderella','白兰花','陌生老朋友','不存在的情人','Love U U','只对你有感觉',
          '当你','一眼万年','保护色','握不住的他','心墙','我很想爱他','一生的爱','记得','完美新世界','I AM','真材实料的我','Cheer Singapore',
          '感动每一刻','梦想的翅膀','曙光','无法克制','一个又一个','爱不会绝迹','转动','妈妈的娜鲁娃','Still Moving Under Gunfire','表达爱',
          '微笑上海','SIXOLOGY','黑武士','醉赤壁','由你选择','Always Online','主角','我还想她','点一把火炬','Cries in a Distance','听见幸福',
          'Hurray一起摇','独白','杀手@续','无聊','K-O','大男人·小女孩','单挑','L-O-V-E','发现爱','Baby Baby','自由不变','忘记','사랑해요只对你说',
          '熟能生巧','不死之身','爱情Yogurt','进化论',"Now That She's Gone",'你要的不是我','流行主教','一千年以前','莎士比亚的天份','突然累了',
          '明天','无尽的思念','盗','听不懂 没关系','来不及了','一开始','起床了','豆浆油条','天使心','森林浴','精灵','相信无限','美人鱼',
          '距离','未完成','Endless Road','星球','冻结','压力','女儿家','星空下的吻','让我心动的人','会有那么一天'
        ];

          const unusedWords = availableWords.filter((word) => !usedWords.includes(word));
          if (unusedWords.length === 0) return null;

          let myText = p.random(unusedWords);
          usedWords.push(myText);

          let textWidth = p.textWidth(myText);
          let w = textWidth + 40;
          let h = 50;
          let x, y;
          let maxAttempts = 100;
          let attempts = 0;

          do {
            x = p.random(margin, p.width - w - margin);
            y = p.random(margin, p.height - h - margin);
            attempts++;
          } while (attempts < maxAttempts && !isLowOverlap(x, y, w, h));

          if (attempts >= maxAttempts) return null;

          let gradient = createFixedGradient(p);
          let style = wordCount === 0 ? "no-border" : p.random() < 0.5 ? "no-border" : "border";
          wordCount++;

          return { text: myText, x: x, y: y, w: w, h: h, style: style, gradient: gradient };
        };

        const isLowOverlap = (x, y, w, h) => {
          return words.every((word) => {
            let overlapX = Math.max(0, Math.min(x + w, word.x + word.w) - Math.max(x, word.x));
            let overlapY = Math.max(0, Math.min(y + h, word.y + word.h) - Math.max(y, word.y));
            let overlapArea = overlapX * overlapY;
            let thisArea = w * h;
            let wordArea = word.w * word.h;
            return overlapArea / thisArea < 0.17 && overlapArea / wordArea < 0.17;  // Overlap threshold
          });
        };

        const createFixedGradient = (p) => {
          let gradient = p.drawingContext.createLinearGradient(
            p.width / 2 - 500,
            p.height / 2 - 500,
            p.width / 2 + 500,
            p.height / 2 + 500
          );
          gradient.addColorStop(0, p.color(242, 195, 255));
          gradient.addColorStop(0.5, p.color(209, 209, 209));
          gradient.addColorStop(1, p.color(92, 175, 255));
          return gradient;
        };

        const drawWord = (p, word) => {
          let gradient = word.gradient;

          if (word.style === "border") {
            p.strokeWeight(3);
            p.stroke(0);
            p.drawingContext.strokeStyle = gradient;
            p.drawingContext.shadowBlur = 5;
            p.drawingContext.shadowColor = p.color(207, 7, 99);
            p.rect(word.x, word.y, word.w, word.h, 20);
            p.textSize(30);
            p.noStroke();
            p.drawingContext.fillStyle = gradient;
            p.text(word.text, word.x + 20, word.y + 35);
          } else {
            p.noStroke();
            p.drawingContext.fillStyle = gradient;
            p.drawingContext.shadowBlur = 5;
            p.drawingContext.shadowColor = p.color(207, 7, 99);
            p.rect(word.x, word.y, word.w, word.h, 20);
            p.textSize(30);
            p.fill(255);
            p.text(word.text, word.x + 20, word.y + 35);
          }
        };
      });
    };

    onMounted(() => {
      setup();
    });

    onUnmounted(() => {
      // const canvas = document.getElementById('canvas');
      // const p5Instance = canvas.p5Instance; // 假设你把 p5 实例存储到 canvas 上
      if (p5Instance) {
        console.log('清除');
        p5Instance.remove(); // 销毁 p5 实例
      }
    });

    return {};
  },
};
</script>

<style scoped>
body {
  margin: 0;
  overflow: hidden;
  font-family: 'Roboto', sans-serif;
  position: relative;
}

#canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 90%;
  height: 90%;
}
</style>
