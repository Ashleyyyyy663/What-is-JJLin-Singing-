<template>
  <div class="container">
    <div class="horizontal-line"></div>
    <div class="vertical-line"></div>

    <div v-for="(chart) in chartData" :key="chart.id" class="chart-container" :id="chart.id">
      <div class="chart-title">{{ chart.title }}</div>
      <svg :width="400" :height="chart.data.length * 50" style="overflow: visible"></svg>
    </div>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "WordFreq",
  data() {
    return {
      chartData: [
        {
          id: "chart1",
          title: "活在哪一天？",
          data: [
            { label: "昨天", value: 5 },
            { label: "今天", value: 10 },
            { label: "明天", value: 29 },
            { label: "每天", value: 8 }
          ],
          color: ["rgb(242, 195, 250)", "rgb(209, 209, 209)", "rgb(92, 175, 255)", "rgb(2, 208, 152)"]
        },
        {
          id: "chart2",
          title: "爱哪个季节？",
          data: [
            { label: "春", value: 6 },
            { label: "夏", value: 3 },
            { label: "秋", value: 5 },
            { label: "冬", value: 6 }
          ],
          color: ["rgb(242, 195, 250)", "rgb(209, 209, 209)", "rgb(92, 175, 255)", "rgb(2, 208, 152)"]
        },
        {
          id: "chart3",
          title: "黑白人生？",
          data: [
            { label: "白", value: 45 },
            { label: "黑", value: 97 },
            { label: "蓝", value: 12 },
            { label: "红", value: 26 }
          ],
          color: ["rgb(242, 195, 250)", "rgb(209, 209, 209)", "rgb(92, 175, 255)", "rgb(2, 208, 152)"]
        },
        {
          id: "chart4",
          title: "第一人称？",
          data: [
            { label: "你", value: 1541 },
            { label: "我", value: 2332 },
            { label: "他", value: 81 },
            { label: "她", value: 125 }
          ],
          color: ["rgb(242, 195, 250)", "rgb(209, 209, 209)", "rgb(92, 175, 255)", "rgb(2, 208, 152)"]
        }
      ]
    };
  },
  mounted() {
    this.chartData.forEach((chart, index) => {
      setTimeout(() => {
        this.$nextTick(() => {
          const container = document.getElementById(chart.id);
          if (container) {
            container.classList.add("visible");
            this.renderChart(chart.id, chart.data, chart.color);
          }
        });
      }, index * 4000);
    });
  },
  beforeUnmount() {
    console.log("清除柱状图");
  },
  methods: {
    renderChart(containerId, data, colors) {
      const container = d3.select(`#${containerId}`);
      const maxValue = d3.max(data, d => d.value);
      const scale = d3.scaleLinear().domain([0, maxValue]).range([0, 300]);

      const svg = container.select("svg");
      svg.attr("transform", "translate(40, 0)"); // 增加整体右移

      const barGroup = svg
        .selectAll("g")
        .data(data)
        .enter()
        .append("g")
        .attr("transform", (d, i) => `translate(15, ${i * 50})`); 

      const gridLines = [0.25, 0.5, 0.75, 1].map(fraction => fraction * maxValue);
      gridLines.forEach(value => {
        svg.append("line")
          .attr("x1", scale(value) + 15) 
          .attr("x2", scale(value) + 15) 
          .attr("y1", 0)
          .attr("y2", data.length * 50)
          .attr("stroke", "lightgray")
          .attr("stroke-width", 1)
          .attr("stroke-dasharray", "4 4");

        svg.append("text")
          .attr("x", scale(value) + 10) // 偏移文字，使其跟随网格线
          .attr("y", -5)
          .attr("text-anchor", "middle")
          .attr("fill", "gray")
          .attr("font-size", "12px")
          .text(value);
      });

      barGroup
        .append("text")
        .attr("x", -10)
        .attr("y", 20)
        .attr("text-anchor", "end")
        .attr("fill", "#333")
        .attr("font-size", "16px")
        .text(d => d.label);

      barGroup
        .append("rect")
        .attr("width", 0)
        .attr("height", 30)
        .attr("fill", (d, i) => colors[i])
        .transition()
        .delay((d, i) => i * 1000)
        .duration(1000)
        .attr("width", d => scale(d.value));
    }
  }
};
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  overflow: hidden; /* 禁止滚动条 */
}

.container {
  display: grid;
  grid-template-rows: 50% 50%;
  grid-template-columns: 50% 50%;
  gap: 0;
  margin: 0;
  height: 95vh; /* 填充全屏 */
  background-color: rgb(255, 246, 223);
}

.chart-container {
  display: flex;
  flex-direction: column;
  justify-content: center; /* 垂直居中 */
  align-items: center;
  padding-top: -30px;
  background: transparent;
  visibility: hidden;
  opacity: 0;
  transition: opacity 0.5s, visibility 0s linear 0.5s;
}

.chart-container.visible {
  visibility: visible;
  opacity: 1;
  transition: opacity 0.5s;
}

.chart-title {
  font-weight: bold;
  font-size: 20px;
  color: #333;
  margin-bottom: 55px; /* 调整与柱状图的距离 */
  text-align: center;
}

.horizontal-line,
.vertical-line {
  position: absolute;
  border-style: dashed;
  border-width: 1px;
  border-color: rgba(32, 24, 18, 0.3);
}

.horizontal-line {
  top: 50%;
  left: 0;
  width: 100%;
}

.vertical-line {
  left: 50%;
  top: 0;
  height: 100%;
}
</style>
