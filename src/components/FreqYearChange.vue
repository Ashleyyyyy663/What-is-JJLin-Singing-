<template>
  <div class="bubble-chart">
    <div id="container" ref="chart">
    </div>
  </div>
</template>

<script>
import * as d3 from "d3";
import data3 from "@/assets/yearFreq.json";


export default {
  name: "FreqYearChange",
  data() {
    return {
      width: window.innerWidth-180,
      height: window.innerHeight-115,
      data: null,
      precomputedPositions: {},
      timePeriods: ["2001-2006", "2007-2012", "2013-2018", "2019-2023"],
      timePoints: [2001, 2006, 2012, 2018, 2023],
      intervalId: null,
      index: 0,
    };
  },
  mounted() {
    this.loadData();
  },
  updated() {
    console.log("FreqYearChange 组件已更新");
    this.loadData();
  },
  beforeUnmount() {
    console.log('组件即将卸载');
  },
  methods: {
    async loadData() {
      try {
        const jsonData = data3;
        this.data = jsonData;
        this.precomputePositions();
        this.initChart();
        this.autoUpdateChart();
      } catch (error) {
        console.error("Error loading data:", error);
      }
    },
    precomputePositions() {
      const columnRanges = [
        { start: 130, end: this.width * 0.2 },
        { start: this.width * 0.25, end: this.width * 0.45 },
        { start: this.width * 0.5, end: this.width * 0.75 },
        { start: this.width * 0.8, end: this.width - 130 },
      ];

      this.timePeriods.forEach((period, index) => {
        const chartData = this.data.map(d => ({
          id: d["词语"],
          word: d["词语"],
          freq: d[period],
        }));

        const maxFreq = d3.max(chartData, d => d.freq);
        const radiusScale = d3.scaleSqrt().domain([0, maxFreq]).range([0, 45]);

        const tempSimulation = d3.forceSimulation(chartData)
          .force("charge", d3.forceManyBody().strength(-4))
          .force("x", d3.forceX(() =>
            columnRanges[index].start + Math.random() * (columnRanges[index].end - columnRanges[index].start)))
          .force("y", d3.forceY(this.height / 2).strength(0.1))
          .force("collide", d3.forceCollide(d => radiusScale(d.freq) + 4))
          .stop();

        tempSimulation.tick(100);

        this.precomputedPositions[period] = chartData.map(d => ({
          id: d.id,
          word: d.word,
          x: d.x,
          y: d.y,
          freq: d.freq,
          radius: radiusScale(d.freq),
        }));
      });
    },
    initChart() {
      const svg = d3.select(this.$refs.chart).append("svg")
        .attr("width", this.width)
        .attr("height", this.height + 20)
        .style("width", "100%")  // 确保宽度自适应
        .style("height", "100%"); // 确保高度自适应;

      this.drawTimeline(svg);

      this.svg = svg;
    },
    drawTimeline(svg) {
      const x = d3.scaleTime()
        .domain([new Date(2001, 0, 1), new Date(2023, 0, 1)])
        .range([30, this.width - 30]);

      this.timePoints.forEach(year => {
        svg.append("line")
          .attr("x1", x(new Date(year, 0, 1)))
          .attr("y1", this.height - 50)
          .attr("x2", x(new Date(year, 0, 1)))
          .attr("y2", 50)
          .attr("class", "timeline-line");

        svg.append("text")
          .attr("x", x(new Date(year, 0, 1)))
          .attr("y", this.height -20)
          .attr("class", "timeline-text")
          .text(year);
      });
    },
    autoUpdateChart() {
      const updateNextChart = () => {
        const period = this.timePeriods[this.index];
        this.updateChart(period);
        this.index = (this.index + 1) % this.timePeriods.length;
      };

      updateNextChart();
      this.intervalId = setInterval(updateNextChart, 3500);
    },
    updateChart(timePeriod) {
      const chartData = this.precomputedPositions[timePeriod];
      const maxFreq = d3.max(chartData, d => d.freq);
      const radiusScale = d3.scaleSqrt().domain([0, maxFreq]).range([0, 45]);

      this.svg.selectAll(".bubble")
        .data(chartData, d => d.id)
        .join(
          enter => enter.append("circle")
            .attr("class", "bubble")
            .attr("r", d => radiusScale(d.freq))
            .attr("cx", d => d.x)
            .attr("cy", d => d.y)
            .attr("fill", "#b4a4f4"),
          update => update.transition().duration(1000)
            .attr("r", d => radiusScale(d.freq))
            .attr("cx", d => d.x)
            .attr("cy", d => d.y),
          exit => exit.transition().duration(1500)
            .style("fill-opacity", 0)
            .remove()
        );

        this.svg.selectAll(".bubble-text")
        .data(chartData, d => d.id)
        .join(
          enter => enter.append("text")
            .attr("class", "bubble-text")
            .attr("dy", "0.05em")          
            .style("font-size", d => `${Math.min(20, d.radius / 2)}px`)
            .style("fill", "white")       
            .text(d => d.word)
            .attr("x", d => d.x)
            .attr("y", d => d.y)
            .style("font-weight","weight")
            .style("text-anchor", "middle")          
            .style("dominant-baseline", "middle"),   
          update => update.transition().duration(1000)
            .attr("dy", "0.05em")        
            .attr("x", d => d.x)
            .style("font-weight","weight")
            .attr("y", d => d.y)
            .style("font-size", d => `${Math.min(20, d.radius / 2)}px`)
            .text(d => d.word)
            .style("text-anchor", "middle")          
            .style("dominant-baseline", "middle"),  
          exit => exit.transition().duration(1500)
            .style("fill-opacity", 0)
            .remove()
        );

    },
  },
};
</script>

<style scoped>
#container {
    display: flex;
    justify-content: center;
    align-items: center;  /* 使容器垂直居中 */
    height: 100%;  /* 让容器占满父元素高度 */
    /* padding-top:20px; */
  }
  
.bubble-chart {
  font-family: Arial, sans-serif;
  background-color: #fff6df;
  margin: 0;
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
}

.bubble {
  fill-opacity: 0.95;
  cursor: pointer;
}

</style>
