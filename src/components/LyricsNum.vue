<template>
  <div ref="chartContainer" id="chart"></div>
</template>

<script>
import { ref, onMounted } from 'vue';
import * as d3 from "d3";
import sunburstData from '@/assets/LyricsNum.json'; // 引入数据

export default {
  name: 'LyricsNum',
  setup() {
    const chartContainer = ref(null);

    // 在组件挂载后生成太阳图
    onMounted(() => {
      generateSunburst(sunburstData);
    });

    function generateSunburst(data) {
      // 定义分区布局函数
      function partition(data) {
        return d3.partition().size([2 * Math.PI, radius * radius])(
          d3
            .hierarchy(data)
            .sum(d => d.value)
            .sort((a, b) => b.value - a.value)
        );
      }
     
      // 定义颜色比例尺
      const color = d3
        .scaleOrdinal()
        .domain(['home', 'product', 'search', 'account', 'other', 'end'])
        .range(['#FFB6C1', '#FFDAB9', '#FFE4E1', '#F0E68C', '#E6E6FA', '#D8BFD8']);

      // 获取窗口的内宽度和内高度（去除滚动条等占据的空间）
      const width = window.innerWidth;
      const height = window.innerHeight-85;
      const radius = Math.min(width, height) / 2.5;

      // 定义用于绘制弧形的函数
      const arc = d3
        .arc()
        .startAngle(d => d.x0)
        .endAngle(d => d.x1)
        .padAngle(1 / radius)
        .padRadius(radius)
        .innerRadius(d => Math.sqrt(d.y0))
        .outerRadius(d => Math.sqrt(d.y1) - 1);
      
      const mousearc = d3
        .arc()
        .startAngle(d => d.x0)
        .endAngle(d => d.x1)
        .innerRadius(d => Math.sqrt(d.y0))
        .outerRadius(radius);

      // 获取数据
      const root = partition(data);

      // 创建 SVG 元素
      const svg = d3.create('svg')
        .attr("width", width)
        .attr("height", height)
        .style("width", "100%")  // 确保宽度自适应
        .style("height", "100%"); // 确保高度自适应;

      svg
        .attr('viewBox', `${-radius + 220} ${-radius - 20} ${2 * radius - 20} ${2 * radius + 40}`)
        .style('max-width', '100vw')
        .style('max-height', '100vh')
        .style('font', '6px sans-serif');

      // 绘制太阳图的各个扇形区域
      const path = svg.append('g')
        .selectAll('path')
        .data(
          root.descendants().filter(d => {
            return d.depth && d.x1 - d.x0 > 0.001;
          })
        )
        .join('path')
        .attr('fill', d => color(d.data.name))
        .attr('d', arc);

      // 为每个扇形区域添加文本
      const text = svg
        .append('g')
        .selectAll('text')
        .data(
          root.descendants().filter(d => {
            return d.depth && d.x1 - d.x0 > 0.001;
          })
        )
        .join('text')
        .attr('text-anchor', 'middle')
        .attr('dy', '0.35em')
        .text(d => d.data.name)
        .style('font-size', d => (d.value < 2000 ? '3px' : '10px'));

      text.attr('transform', d => `translate(${arc.centroid(d)})`);
      
      const label = svg
          .append("text")
          .attr("text-anchor", "middle")
          .attr("fill", "#888")
          .style("visibility", "hidden");

      // 在创建的 'text' 元素内添加一个 'tspan' 子元素，用于显示主要的值部分
      // 设置其类名、坐标位置、字体大小等属性，并初始化为空文本
      label
          .append("tspan")
          .attr("class", "value")
          .attr("x", 0)
          .attr("y", 0)
          .attr("dy", "-0.1em")
          .attr("font-size", "3em")
          .text("");

      // 再添加一个 'tspan' 子元素，用于显示一些描述性的文本
      // 设置其坐标位置和font style
      label
          .append("tspan")
          .append("tspan")
          .attr("x", 0)
          .attr("y", 0)
          .attr("dy", "1.5em")
          .text("albums_songs_lyricNumber")
          .style("font", "15px sans-serif");
        
      const element = svg.node();
      element.value = { sequence: [], percentage: 0.0 };

      svg.append("g")
        .attr("fill", "none")
        .attr("pointer-events", "all")
        .on("mouseleave", () => {
            // 当鼠标离开时，恢复所有扇形区域的填充不透明度为1
            // 隐藏用于显示信息的文本标签，并更新视图的值为初始状态
            path.attr("fill-opacity", 1);
            label.style("visibility", "hidden");
            element.value = { sequence: [], percentage: 0.0 };
            element.dispatchEvent(new CustomEvent("input"));
        })
        .selectAll("path")
        .data(
            root.descendants().filter(d => {
                // 再次过滤数据节点，确保只处理需要绘制的节点
                return d.depth && d.x1 - d.x0 > 0.001;
            })
        )
        .join("path")
        .attr("d", mousearc)
        .on("mouseenter", (event, d) => {
            // 当鼠标悬停在某个扇形区域时
            // 获取当前扇形区域的祖先序列（除根节点外），以便后续根据这个序列来设置相关属性
            const sequence = d
                .ancestors()
                .reverse()
                .slice(1);

            // 根据祖先序列设置扇形区域的填充透明度，使得悬停的扇形及其祖先扇形更加突出显示
            path.attr("fill-opacity", node =>
                sequence.indexOf(node) >= 0 ? 1.0 : 0.3
            );

            // 获取当前节点的value值，用于后续显示相关信息
            const value = d.value;

            // 新增：获取根节点名字
            // 通过循环遍历当前节点的祖先节点，直到找到深度为1的节点（即根节点），获取其名字
            let rootNodeName = "林俊杰";
            if (d.depth > 1) {
                let currentNode = d;
                while (currentNode.depth > 1) {
                    currentNode = currentNode.parent;
                }
                rootNodeName = currentNode.data.name;
            }

            // 拼接显示信息，包括当前节点名字、所属根节点名字以及当前节点的值
            const displayText = `${d.data.name} (from ${rootNodeName}) - ${value}`;

            // 显示用于显示信息的文本标签，并设置其主要值部分的文本内容
            label
                .style("visibility", null)
                .select(".value")
                .text(displayText);

            // 更新视图的值，包含当前悬停的序列和百分比等信息
            element.value = { sequence, value };
            element.dispatchEvent(new CustomEvent("input"));
        });

      const rightTextBoxGroup = svg.append("g")
                .attr("transform", `translate(${radius * 1.3},-${radius*0.7 })`);

      rightTextBoxGroup.append("rect")
          .attr("width", 300)
          .attr("height", 400)
          .attr("fill", "#fff6df")
          .attr("rx", 5)
          .attr("ry", 5);

      const rightTextBoxText = rightTextBoxGroup.append("text")
          .attr("x", 10)
          .attr("y", 20)
          .attr("text-anchor", "start")
          .attr("fill", "#333")
          .style("font-size", "10px")
          .style("font-family", "sans-serif");

      // 使用 <tspan> 元素来添加可以换行的文本内容
        rightTextBoxText.append("tspan")
                .text("以旭日图展示林俊杰专辑、歌曲及歌词字数信息。")
                .attr("x", 10)
                .attr("y", 20);

            rightTextBoxText.append("tspan")
                .text("从专辑分支，细化至歌曲，呈现歌词字数占比，")
                .attr("x", 10)
                .attr("y", 40);

            rightTextBoxText.append("tspan")
                .text("直观展现其音乐作品歌词量分布，")
                .attr("x", 10)
                .attr("y", 60);

            rightTextBoxText.append("tspan")
                .text("助粉丝与研究者探究创作细节。")
                .attr("x", 10)
                .attr("y", 80);

            rightTextBoxText.append("tspan")
                .text("在林俊杰歌曲歌词字数的统计数据中，")
                .attr("x", 10)
                .attr("y", 120);

            rightTextBoxText.append("tspan")
                .text("歌词数量分布呈现出一定特征。")
                .attr("x", 10)
                .attr("y", 140);

            rightTextBoxText.append("tspan")
                .text("专辑 “和自己对话” 中歌曲歌词字数的总和相对较多，")
                .attr("x", 10)
                .attr("y", 160);

            rightTextBoxText.append("tspan")
                .text("这表明该专辑在歌词创作规模上较为突出。")
                .attr("x", 10)
                .attr("y", 180);

            rightTextBoxText.append("tspan")
                .text("其中，“序曲：12年前” 的歌词字数")
                .attr("x", 10)
                .attr("y", 220);

            rightTextBoxText.append("tspan")
                .text("在专辑内较为突出，说明此歌曲可能")
                .attr("x", 10)
                .attr("y", 240);

            rightTextBoxText.append("tspan")
                .text("在情感描绘或故事叙述方面有更深入的需求。")
                .attr("x", 10)
                .attr("y", 260);

            rightTextBoxText.append("tspan")
                .text("而像 “Sixology（口白）” 这类歌词字数较少的曲目和一些纯音乐，")
                .attr("x", 10)
                .attr("y", 300);

            rightTextBoxText.append("tspan")
                .text("可能更侧重于音乐节奏或其他元素的展现，")
                .attr("x", 10)
                .attr("y", 320);

            rightTextBoxText.append("tspan")
                .text("从整体来看，各专辑歌词字数的差异")
                .attr("x", 10)
                .attr("y", 360);

            rightTextBoxText.append("tspan")
                .text("也体现了其音乐风格与歌词创作的多元性与动态变化。")
                .attr("x", 10)
                .attr("y", 380);

      // 把生成的 SVG 添加到页面中
      chartContainer.value.appendChild(svg.node());
    }

    return {
      chartContainer
    };
  }
};
</script>

<style scoped>
body {
  background-color: #fff6df;
  margin: 0;
  padding: 0;
  overflow: hidden;
}

</style>
