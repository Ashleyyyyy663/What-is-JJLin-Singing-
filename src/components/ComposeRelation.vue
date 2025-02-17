<template>
  <div class="chart-container" ref="chart"></div>
</template>

<script>
import * as echarts from 'echarts';

export default {
  name: 'BusySongwritersComponent',
  data() {
    return {
      chart: null,
      localData: {
       "nodes": [
          {
              "name": "易家扬",
              "value": 11,
              "representativeWorks": "《遇见》",
              "photoURL": "https://img1.baidu.com/it/u=3881189881,1892626237&fm=253&fmt=auto&app=120&f=JPEG?w=333&h=500",
              itemStyle: { color: '#fbb4ae' }
          },
          {
              "name": "林怡凤",
              "value": 25,
              "representativeWorks": "《背对背拥抱》",
              "photoURL": "https://pic.baike.soso.com/ugc/baikepic2/2710/cut-20210114175952-1563710535_jpg_221_295_9702.jpg/300",
              itemStyle: { color: '#b3cde3' }
          },
          {
              "name": "方文山",
              "value": 10,
              "representativeWorks": "《青花瓷》《七里香》",
              "photoURL": "https://img0.baidu.com/it/u=1421982636,3937219436&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=666",
              itemStyle: { color: '#bcc0da' }
          },
          {
              "name": "林俊杰（作词）",
              "value": 34,
              "representativeWorks": "《江南》",
              "photoURL": "https://img2.baidu.com/it/u=1322966601,236030098&fm=253&fmt=auto&app=120&f=JPEG?w=553&h=500",
              itemStyle: { color: '#dacfdd' }
          },
          {
              "name": "林秋离",
              "value": 30,
              "representativeWorks": "《哭砂》",
              "photoURL": "https://pic.rmb.bdstatic.com/bjh/events/9f3bdfdc7d7845babbdb88867557c1a05435.png@h_1280",
              itemStyle: { color: '#e9bfb7' }
          },
          {
              "name": "张思尔",
              "value": 22,
              "representativeWorks": "《我选的天空》",
              "photoURL": "https://img2.baidu.com/it/u=1645323154,25280626&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=520",
              itemStyle: { color: '#706998' }
          },
          {
              "name": "小寒",
              "value": 5,
              "representativeWorks": "《达尔文》",
              "photoURL": "https://img2.baidu.com/it/u=1645323154,25280626&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=520",
              itemStyle: { color: '#b794aa' }
          },

          {
              "name": "周信廷",
              "value": 2,
              itemStyle: { color: '#fbb4ae' }
          },
          {
              "name": "杨彤",
              "value": 2,
              itemStyle: { color: '#b3cde3' }
          },
          {
              "name": "蔡健雅",
              "value": 1,
              "representativeWorks": "《红色高跟鞋》",
              "photoURL": "https://img0.baidu.com/it/u=3896742793,1535977&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=500",
              itemStyle: { color: '#bcc0da' }
          },
          {
              "name": "张怀秋",
              "value": 5,
              "representativeWorks": "《无滤镜》",
              "photoURL": " https://img2.baidu.com/it/u=1829222908,3656402790&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=625",
              itemStyle: { color: '#dacfdd' }
          },
          {
              "name": "葛大为",
              "value": 2,
              "representativeWorks": "《无人知晓》《惧高症》",
              "photoURL": " https://5b0988e595225.cdn.sohucs.com/images/20180516/85e8438d899b4d94b0cc0312138bfa2d.jpeg",
              itemStyle: { color: '#e9bfb7' }
          },
          {
              "name": "姚若龙",
              "value": 7,
              "representativeWorks": "《心墙》《会呼吸的痛》",
              "photoURL": " https://b0.bdstatic.com/9f4b137ebdb2dc8f9a3b04490dd52610.jpeg@h_1280",
              itemStyle: { color: '#706998' }
          },
          {
              "name": "林夕",
              "value": 3,
              "representativeWorks": "《十年》",
              "photoURL": " https://img1.baidu.com/it/u=3869768938,3064727701&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=329",
              itemStyle: { color: '#b794aa' }
          },
          {
              "name": "王雅君",
              "value": 7,
              "representativeWorks": "《隐形的翅膀》",
              "photoURL": " https://img2.baidu.com/it/u=3809347198,3187359072&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=750",
              itemStyle: { color: '#fbb4ae' }
          },
          {
              "name": "阿信（陈信宏）",
              "value": 3,
              "representativeWorks": "《知足》",
              "photoURL": " https://img2.baidu.com/it/u=2210121337,1966937272&fm=253&fmt=auto&app=120&f=JPEG?w=500&h=750",
              itemStyle: { color: '#b3cde3' }
          },
          {
              "name": "吴青峰",
              "value": 4,
              "representativeWorks": "《小情歌》",
              "photoURL": " https://img0.baidu.com/it/u=886510225,410401330&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=750",
              itemStyle: { color: '#bcc0da' }
          },
          {
              "name": "简胜",
              "value": 2,
              itemStyle: { color: '#dacfdd' }
          },
          {
              "name": "Dr Moon",
              "value": 3,
              itemStyle: { color: '#e9bfb7' }
          },
          {
              "name": "Mr Mars",
              "value": 2,
              itemStyle: { color: '#706998' }
          },
          {
              "name": "许环良",
              "value": 7,
              "representativeWorks": "《江南》《一千年以后》",
              "photoURL": " https://img2.baidu.com/it/u=109315912,2864586013&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=677",
              itemStyle: { color: '#fbb4ae' }
          },
          {
              "name": "Venus",
              "value": 2,
              itemStyle: { color: '#b794aa' }
          },
          {
              "name": "陈少琪",
              "value": 1,
              "representativeWorks": "《北京欢迎你》",
              "photoURL": "https://p1.ssl.qhmsg.com/t010241bf68b34c427a.jpg",
              itemStyle: { color: '#b3cde3' }
          },
          {
            "name": "孙燕姿",
            "value": 1,
            "representativeWorks": "《遇见》",
            "photoURL": "https://img2.baidu.com/it/u=1504401281,1630508213&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=750",
            itemStyle: { color: '#bcc0da' }
        },
          {
              "name": "林宇中",
              "value": 5,
              "representativeWorks": "《旋律》",
            "photoURL": "https://img1.baidu.com/it/u=312652296,777274134&fm=253&fmt=auto&app=138&f=JPEG?w=355&h=500",
            itemStyle: { color: '#dacfdd' }
              
          },
          {
            "name": "其他作词人（每位有1-2首作品，不再进行单独分组）",
            "value": 41,
            itemStyle: { color: '#706998' }
          },
          {
              "name": "林俊杰（作曲）",
              "value": 138,
              "photoURL": "https://img0.baidu.com/it/u=3865269640,139525652&fm=253&fmt=auto&app=120&f=JPEG?w=667&h=500",
              
          },
          {
              "name": "其他作曲人",
              "value": 35,
              itemStyle: { color: '#e9bfb7' }
          },
  
        ],
        "links": [
          {
            "source": "易家扬",
            "target": "林俊杰（作曲）",
            "value": 10 // 表示从类别A到类别B的流量等数值
          },
          {
            "source": "易家扬",
            "target": "其他作曲人",
            "value": 1
          },
          {
              "source": "林怡凤",
              "target": "林俊杰（作曲）",
              "value": 25
          },
          {
              "source": "林怡凤",
              "target": "其他",
              "value": 2
          },
          {
            "source": "方文山",
            "target": "林俊杰（作曲）",
            "value": 10
          },
          
          {
            "source": "林秋离",
            "target": "林俊杰（作曲）",
            "value": 30
        },
        {
              "source": "张思尔",
              "target": "林俊杰（作曲）",
              "value": 22
          },
          {
            "source": "小寒",
            "target": "林俊杰（作曲）",
            "value": 5
        },
        {
              "source": "杨彤",
              "target": "林俊杰（作曲）",
              "value": 2
          },
          {
            "source": "蔡健雅",
            "target": "其他作曲人",
            "value": 1
          },
          {
            "source": "张怀秋",
            "target": "林俊杰（作曲）",
            "value": 5
        },
        {
          "source": "张怀秋",
          "target": "其他作曲人",
          "value": 4
        },
        {
          "source": "葛大为",
          "target": "林俊杰（作曲）",
          "value": 2
      },
          {
            "source": "姚若龙",
            "target": "林俊杰（作曲）",
            "value": 7
        },
        {
              "source": "林夕",
              "target": "林俊杰（作曲）",
              "value": 3
          },
          {
            "source": "王雅君",
            "target": "林俊杰（作曲）",
            "value": 7
        },
        {
          "source": "阿信（陈信宏）",
          "target": "林俊杰（作曲）",
          "value": 3
      },
      {
        "source": "吴青峰",
        "target": "林俊杰（作曲）",
        "value": 4
    },
    
    {
      "source": "简胜",
      "target": "林俊杰（作曲）",
      "value": 2
  },
  {
    "source": "周信廷",
    "target": "林俊杰（作曲）",
    "value": 2
  },
      {
        "source": "Dr Moon",
        "target": "林俊杰（作曲）",
        "value": 3
    },
    {
          "source": "Mr Mars",
          "target": "林俊杰（作曲）",
          "value": 2
      },
      {
        "source": "许环良",
        "target": "林俊杰（作曲）",
        "value": 7
    },
    {
      "source": "Venus",
      "target": "林俊杰（作曲）",
      "value": 2
  },
  {
    "source": "林宇中",
    "target": "林俊杰（作曲）",
    "value": 4
  },
  {
    "source": "孙燕姿",
    "target": "林俊杰（作曲）",
    "value": 1
  },
  {
    "source": "林宇中",
    "target": "其他作曲人",
    "value": 1
  }, {
    "source": "陈少琪",
    "target": "其他作曲人",
    "value": 1
  },
  {
    "source": "其他作词人（每位有1-2首作品，不再进行单独分组）",
    "target": "林俊杰（作曲）",
    "value": 43
  },
  {
  "source": "林俊杰（作词）",
  "target": "林俊杰（作曲）",
  "value": 33
  },
  {
  "source": "林俊杰（作词）",
  "target": "其他作曲人",
  "value": 1
  }, 
  {
  "source": "其他作词人（每位有1-2首作品，不再进行单独分组）",
  "target": "其他作曲人",
  "value": 27
  },
        ]
      }
    };
  },
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chartDom = this.$refs.chart;
      this.chart = echarts.init(chartDom);
      const option = {
        title: {
          text: '最\n忙\n碌\n的\n作\n词\n&\n曲\n人',
          //left: 'right' + ' -500px', 
          left : 1500,
          top:'center',
          textStyle: {
            fontSize: 50, // 设置字体大小
            lineHeight: 60, // 设置行距（字符之间的垂直间隔）
            // 其他样式属性，如字体颜色、字体权重等也可以在这里设置
            //color: '#333', // 设置字体颜色为深灰色
            //fontWeight: 'bold' // 设置字体为粗体
        }
        },
        tooltip: {
          trigger: 'item',
          triggerOn: 'mousemove',
          formatter: (params) => {
            if (params.dataType === 'node') {
              const name = params.data.name;
              const representativeWorks = params.data.representativeWorks || '还有很多作品等待你去发掘~';
              const photoURL = params.data.photoURL || 'https://t10.baidu.com/it/u=3719426350,228342414&fm=30&app=106&f=JPEG?w=640&h=640&s=ED863472433373245EF01DC70000E0A1';
              return `
                <div>
                  <strong>${name}</strong><br/>
                  代表作: ${representativeWorks}<br/>
                  作品数: ${params.data.value}<br/>
                  <img src="${photoURL}" alt="${name}的照片" style="max-width:100px;height:auto;margin-top:10px;">
                </div>
              `;
            } else {
              return `作词： ${params.data.source} 作曲： ${params.data.target}<br/>作品数: ${params.data.value}`;
            }
          }
        },
        series: [
          {
            type: 'sankey',
            data: this.localData.nodes,
            links: this.localData.links,
            emphasis: {
              focus: 'adjacency'
            },
            levels: [
              {
                depth: 0,
                itemStyle: {
                  color: '#fbb4ae'
                },
                lineStyle: {
                  color: 'source',
                  opacity: 0.6
                }
              },
              {
                depth: 1,
                itemStyle: {
                  color: '#b3cde3'
                },
                lineStyle: {
                  color: 'source',
                  opacity: 0.6
                }
              },
              {
                depth: 2,
                itemStyle: {
                  color: '#ccebc5'
                },
                lineStyle: {
                  color: 'source',
                  opacity: 0.6
                }
              },
              {
                depth: 3,
                itemStyle: {
                  color: '#decbe4'
                },
                lineStyle: {
                  color: 'source',
                  opacity: 0.6
                }
              }
            ],
            lineStyle: {
              curveness: 0.5
            }
          }
        ]
      };
      this.chart.setOption(option);
    }
  },
  beforeUnmount() {
    if (this.chart) {
      this.chart.dispose();
    }
  }
};
</script>

<style scoped>
.chart-container {
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center;    /* 垂直居中 */
  width: 100%;            /* 宽度自适应 */
  height: 100vh;          /* 高度自适应，视口高度 */
  margin: 0 auto ;         /* 在必要时用于水平居中，但flex已经处理 */
  overflow: hidden;       /* 如果内容超出，隐藏超出部分 */
  box-sizing: border-box; /* 包括边框和内边距在内计算元素的总宽度和高度 */
}
 
/* 如果需要更细粒度的控制，比如特定屏幕大小下的样式，可以添加媒体查询 */
@media (max-width: 768px) {
  .chart-container {
    max-width: 100%;      /* 在小屏幕上完全占满宽度 */
    padding: 100px;        /* 可选：增加一些内边距 */
  }
}
.echarts-tooltip {
  position: absolute;
  background: rgba(50, 50, 50, 0.7);
  color: #fff;
  padding: 100px;
  border-radius: 5px;
  pointer-events: none;
  z-index: 100;
  white-space: nowrap;
  font-size: 12px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  max-width: 300px;
}
.echarts-tooltip img {
  max-width: 100%;
  height: auto;
  display: block;
  margin-top: 200px;
}
</style>