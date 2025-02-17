<template>
  <div id="container">
    <svg ref="chart" :width="width" :height="height"></svg>
  </div>
</template>

<script>
import * as d3 from 'd3';

export default {
  name: 'AlbumScore',
  data() {
    return {
      width: window.innerWidth-180, // 根据页面宽度调整
      height: window.innerHeight-50,//根据页面高度调整
      margin: { top: 60, right: 100, bottom: 90, left: 120 },
      data: [
        { date: '2003.4.10', album: '乐行者', evaluations: 16661, score: 8.3 },
        { date: '2004.6.4', album: '第二天堂', evaluations: 42047, score: 8.5 },
        { date: '2005.4.1', album: '编号89757', evaluations: 19627, score: 8.1 },
        { date: '2006.2.17', album: '曹操', evaluations: 19529, score: 7.8 },
        { date: '2007.6.29', album: '西界', evaluations: 10653, score: 7.5 },
        { date: '2008.10.18', album: 'JJ陆', evaluations: 17112, score: 7.8 },
        { date: '2009.12.18', album: '100天', evaluations: 9015, score: 7.6 },
        { date: '2010.12.8', album: '她说', evaluations: 37063, score: 8.3 },
        { date: '2011.12.30', album: '学不会', evaluations: 13178, score: 8.2 },
        { date: '2013.3.13', album: '因你而在', evaluations: 6521, score: 8.2 },
        { date: '2014.12.27', album: '新地球', evaluations: 8369, score: 8.4 },
        { date: '2015.12.25', album: '和自己对话', evaluations: 7049, score: 9.2 },
        { date: '2017.12.29', album: '伟大的渺小', evaluations: 7065, score: 8.9 },
        { date: '2020.10.20', album: '幸存者', evaluations: 6128, score: 8.1 },
        { date: '2023.4.21', album: '重拾_快乐', evaluations: 4893, score: 7.2 },
      ],
    };
  },
  mounted() {
  this.$nextTick(() => {
    this.createChart();
  });
},
  methods: {
    createChart() {
      console.log(this.data);

      const svg = d3.select(this.$refs.chart)
        .append('g')
        .attr('transform', `translate(${40},${ 10})`);

      const parseDate = d3.timeParse('%Y.%m.%d');
      this.data.sort((a, b) => d3.ascending(parseDate(a.date), parseDate(b.date)));
      /*const colorScale = d3.scaleOrdinal()
        .domain([d3.min(this.data, d => d.score), d3.max(this.data, d => d.score)])
        .range(['#FF5C00', '#96753C', '#A290E9', '#5CAFFF', '#02D098', '#F2C3FF']);*/
        const customPinkInterpolator = d3.interpolateRgbBasis(["#FFC0CB", "#FFB6C1", "#FF69B4", "#FF1493", "#DB7093"]);
        // 设置颜色比例尺函数，根据数据中的评分数据设置定义域和值域
        // 用于将评分数据映射到特定的颜色值，实现根据评分不同而颜色不同的可视化效果
        const colorScale = d3.scaleSequential(customPinkInterpolator)
            .domain([d3.min(this.data, d => d.score), d3.max(this.data, d => d.score)]);


      const validData = this.data.filter(d => parseDate(d.date)); // 确保所有日期都被正确解析
      const x = d3.scaleTime()
          .domain([
              new Date(d3.min(validData, d => parseDate(d.date)).getFullYear() - 1, 0, 1), // 最小日期 -1年
              new Date(d3.max(validData, d => parseDate(d.date)).getFullYear() + 1, 11, 31)  // 最大日期 +1年
          ])
          .range([0, this.width]);


      // Y轴比例尺
      const y = d3.scaleLinear()
        .domain([0, d3.max(this.data, d => d.evaluations)])
        .nice()
        .range([this.height-35, 0]);

      // 添加X轴
      svg.append('g')
        .attr('class', 'axis axis--x')
        .attr('transform', `translate(0,${this.height-35})`)
        .call(d3.axisBottom(x).tickFormat(d3.timeFormat('%Y.%m.%d')));

      // 添加Y轴
      svg.append('g')
        .attr('class', 'axis axis--y')
        .call(d3.axisLeft(y));

      // 创建Tooltip
      const tooltip = d3.select("body")
        .append('div')
        .attr('id', 'tooltip')
        .attr('class', 'tooltip')
        .style('opacity', 0);

      // 添加评价人数文本标签
      svg.selectAll('.text')
        .data(this.data)
        .enter()
        .append('text')
        .attr('class', 'text')
        .attr('x', d => x(parseDate(d.date)) )
        .attr('y', d => y(d.evaluations) - 25)
        .attr('text-anchor', 'middle')
        .text(d => d.evaluations)
        .style('opacity', 0) // 初始透明度设为0
            .transition()
            .delay((d, i) => {
                // 圆形元素的延迟和持续时间之和
                const circleDelay = (i * 600);
                const circleDuration = 500;
                return circleDelay + circleDuration; // 加一点额外的时间确保圆形动画完全结束
            })
            .duration(1300) // 文本标签的过渡持续时间
            .style('opacity', 1);

      // 添加专辑名称标签
      svg.selectAll('.album-label')
        .data(this.data)
        .enter()
        .append('text')
        .attr('class', 'album-label')
        .attr('x', d => x(parseDate(d.date)) )
        .attr('y', d => y(d.evaluations) - 40)
        .attr('text-anchor', 'middle')
        .text(d => d.album)
        .style('opacity', 0) // 初始透明度设为0
            .transition()
            .delay((d, i) => {
                // 圆形元素的延迟和持续时间之和
                const circleDelay = (i * 600);
                const circleDuration = 500;
                return circleDelay + circleDuration; // 加一点额外的时间确保圆形动画完全结束
            })
            .duration(1300) // 专辑名称标签的过渡持续时间
            .style('opacity', 1);

      // 创建线条
      svg.selectAll('.line')
        .data(this.data)
        .enter()
        .append('line')
        .attr('class', 'line')
        .attr('x1', d => x(parseDate(d.date)))
        .attr('x2', d => x(parseDate(d.date)))
        .attr('y1', d => y(d.evaluations))
        .attr('y2', () => y(0))
        .attr('stroke', 'green')
        .attr('stroke-width', 0)
        .attr("opacity", 0)
        .attr("stroke-opacity", 0)
        .on('mouseover', (event, d) => this.showTooltip(event, d))
        .on('mouseout', (event) => this.hideTooltip(event))
        .transition()
        .delay((d, i) => i * 600)
        .duration(1800)
        .attr("stroke-width", 2)
        .attr("opacity", 1)
        .attr("stroke-opacity", 1);

      // 创建圆形
      svg.selectAll('.circle')
        .data(this.data)
        .enter()
        .append('circle')
        .attr('class', 'circle')
        .attr('cx', d => x(parseDate(d.date)))
        .attr('cy', d => y(d.evaluations))
        .attr('r', 0)
        .attr('fill-opacity', 0)
        .attr('fill', d => colorScale(d.score))
        .attr("opacity", 0)
        .on('mouseover', (event, d) => this.showTooltip(event, d))
        .on('mouseout', (event) => this.hideTooltip(event))
        .transition()
        .delay((d, i) => i * 600)
        .duration(1800)
        .attr('r', d => d.score * 8 - 50)
        .attr('fill-opacity', 0.6)
        .attr("opacity", 1);

      // 定义显示提示框
      this.showTooltip = function(event, d) {
        tooltip.transition().duration(200).style('opacity', .9);
        tooltip.html(`专辑名称: ${d.album}<br>发行时间: ${d.date}<br>评分人数: ${d.evaluations}<br>评分: ${d.score.toFixed(1)}`)
          .style('left', (event.pageX + 10) + 'px')
          .style('top', (event.pageY - 60) + 'px');
      };

      // 隐藏提示框
      this.hideTooltip = function() {
        tooltip.transition().duration(100).style('opacity', 0);
      };

    },
  },
};
</script>

<style>
  body { 
    font-family: Arial, sans-serif;
    background-color: #fff6df;
  }
 
  .axis path, .axis line {
    fill: none;
    shape-rendering: crispEdges;
    stroke: #96753C;
    stroke-width: 2px;
  }

  .axis text {
    font: 10px sans-serif;
    fill: black;
  }

  .album-label, .text {
    font-size: 10px;
    fill: black;
  }

  .line {
    stroke: #02D098;
    stroke-width: 2;
  }

  .circle {
    fill-opacity: 0.6;
    stroke: none;
  }

  .tooltip {
    position: absolute;
    text-align: center;
    width: 150px;
    height: auto;
    padding: 5px;
    font: 12px sans-serif;
    background: rgba(0,0,0,0.6);
    border-radius: 5px;
    color: #fff;
    pointer-events: none;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  }
</style>
