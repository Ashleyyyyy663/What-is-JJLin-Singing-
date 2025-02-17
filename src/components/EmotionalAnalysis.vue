<template>
  <div>
    <h1 class="title">歌词中最多的情感竟是？</h1>
    <div class="bar-chart-container">
      <svg id="bar-chart"></svg>
    </div>
    <div class="bubble-chart-container">
      <svg id="bubble-chart"></svg>
    </div>
    <div class="tooltip" id="tooltip"></div>

    <!-- Custom Lyrics Modal -->
    <div id="lyricsModal" class="lyrics-modal">
      <div class="lyrics-modal-content">
        <span @click="closeLyricsModal" class="close-button">&times;</span>
        <h2 id="songTitle" class="song-title">{{ currentSongName }} 的歌词</h2>
        <pre id="songLyrics" class="song-lyrics">{{ currentSongLyrics }}</pre>
      </div>
    </div>

    <!-- Audio player -->
    <!-- Replace v-show with v-if to ensure the element is in the DOM when needed -->
    <audio v-if="isAudioPlayerVisible" ref="audioPlayer" controls>
      Your browser does not support the audio tag.
    </audio>
  </div>
</template>

<script>
import { ref, onMounted, nextTick } from "vue";
import * as d3 from "d3";

export default {
  name: "EmotionalAnalysis",
  setup() {
    const currentSongName = ref("");
    const currentSongLyrics = ref("");
    const isAudioPlayerVisible = ref(false);
    const audioPlayer = ref(null);

    const closeLyricsModal = () => {
      document.getElementById("lyricsModal").style.display = "none";
    };

    const showLyrics = (songName, songLyrics) => {
      currentSongName.value = songName;
      currentSongLyrics.value = songLyrics;
      const lyricsModal = document.getElementById("lyricsModal");
      lyricsModal.style.display = "block";
    };

    const playMusic = (songName) => {
  console.log("playMusic function called with song:", songName);  // 调试用，检查函数是否被调用
  isAudioPlayerVisible.value = true;

  // 使用 nextTick 确保 DOM 渲染完成后再访问 audioPlayer
  nextTick(() => {
    // 通过 ref 直接获取元素
    let audioElement = audioPlayer.value;  // 将 const 改为 let

    if (!audioElement) {
      // 如果通过 ref 获取不到，使用 DOM 查询来确保元素存在
      console.error("Audio player not found, trying to find it in the DOM");
      const element = document.querySelector("audio");
      if (element) {
        console.log("Audio element found via DOM query.");
        audioElement = element;  // 更新 audioElement 为 DOM 查询的元素
      }
    }

    if (audioElement) {
      const audioPath = `/videos/${songName}.mp3`;
      console.log("Audio path:", audioPath);  // 检查路径是否正确
      audioElement.src = audioPath;

      // 等待音频加载完成
      audioElement.onloadeddata = () => {
        console.log("Audio loaded, ready to play.");
        audioElement.play()
          .then(() => {
            console.log("Audio is playing.");
          })
          .catch((error) => {
            console.error("Error playing audio: ", error);
          });
      };

      audioElement.load();  // 加载音频文件
    } else {
      console.error("Audio player not found.");
    }
  });
};


    // Data visualization and D3 logic
    onMounted(() => {
      d3.json("./processed_emotion_lyrics.json").then((data) => {
        const categories = ["正面", "中立", "负面"];
        const groupedData = {
          正面: data.filter((d) => d.情感类别 === "正面"),
          中立: data.filter((d) => d.情感类别 === "中立"),
          负面: data.filter((d) => d.情感类别 === "负面"),
        };

        const totalSongs = data.length;

        // Draw bar chart
        const barChartWidth = 1400;
        const barChartHeight = 30;
        const barSvg = d3
          .select("#bar-chart")
          .attr("width", barChartWidth)
          .attr("height", barChartHeight + 20);

        const colorScale = d3
          .scaleOrdinal()
          .domain(categories)
          .range(["#A290E9", "#D1D1D1", "#5CAFFF"]);

        let xOffset = 0;

        barSvg
          .selectAll(".bar")
          .data(categories)
          .enter()
          .append("rect")
          .attr("class", "bar")
          .attr("x", d => {
            const width = (groupedData[d].length / totalSongs) * barChartWidth;
            const currentX = xOffset;
            xOffset += width;
            return currentX;
          })
          .attr("y", 0)
          .attr("width", (d) => (groupedData[d].length / totalSongs) * barChartWidth)
          .attr("height", barChartHeight)
          .attr("fill", (d) => colorScale(d))
          .on("click", function (event, d) {
            updateBubbleChart(d);
            d3.selectAll(".bar")
              .classed("selected", false)
              .attr("opacity", 0.5);
            d3.select(this).classed("selected", true).attr("opacity", 1);
          });

        let xLabelOffset = 0;
        barSvg
          .selectAll(".label")
          .data(categories)
          .enter()
          .append("text")
          .attr("x", d => {
            const width = (groupedData[d].length / totalSongs) * barChartWidth;
            const currentX = xLabelOffset + width / 2;
            xLabelOffset += width;
            return currentX;
          })
          .attr("y", barChartHeight + 15)
          .attr("text-anchor", "middle")
          .attr("fill", "black")
          .attr("font-size", "14px")
          .text((d) => `${d} (${groupedData[d].length})`);

        d3.select(barSvg.selectAll(".bar").nodes()[0]).classed("selected", true);

        const bubbleChartWidth = 1300;
        const bubbleChartHeight = 560;
        const bubbleSvg = d3
          .select("#bubble-chart")
          .attr("width", bubbleChartWidth)
          .attr("height", bubbleChartHeight);

        const tooltip = d3.select("#tooltip");

        const highlightedSongs = [
          "曹操",
          "原来",
          "修炼爱情",
          "交换余生",
          "当你",
          "害怕",
          "零度的亲吻",
          "转动",
          "茉莉雨",
          "小酒窝",
        ];

        function updateBubbleChart(selectedCategory) {
          const selectedData = groupedData[selectedCategory];

          bubbleSvg.selectAll("*").remove();

          const bubbles = bubbleSvg
            .selectAll(".bubble")
            .data(selectedData)
            .enter()
            .append("g")
            .attr("class", "bubble-group")
            .attr("transform", function () {
              const xPos = Math.random() * (bubbleChartWidth - 60) + 30;
              const yPos = Math.random() * (bubbleChartHeight - 60) + 30;
              return `translate(${xPos}, ${yPos})`;
            })
            .call(
              d3
                .drag()
                .on("drag", function (event) {
                  const newX = event.x;
                  const newY = event.y;
                  d3.select(this).attr("transform", `translate(${newX}, ${newY})`);
                })
            );

          bubbles
            .append("circle")
            .attr("class", "bubble")
            .attr("r", 30)
            .attr("fill", colorScale(selectedCategory))
            .attr("opacity", 0.5)
            .classed("highlighted", (d) => highlightedSongs.includes(d.歌名))
            .on("mouseover", function (event, d) {
              tooltip.style("display", "block").html(`
                <strong>歌名:</strong> ${d.歌名}<br><strong>情感值:</strong> ${d.情感值}<br><strong>关键词:</strong> ${d.关键词}`);
            })
            .on("mousemove", function (event) {
              tooltip.style("top", event.pageY + 10 + "px").style("left", event.pageX + 10 + "px");
            })
            .on("mouseout", function () {
              tooltip.style("display", "none");
            })
            .on("click", function (event, d) {
              if (d.selected) {
                playMusic(d.歌名);
              } else {
                showLyrics(d.歌名, d.歌词);
              }
            });

          bubbles
            .filter((d) => d.selected)
            .append("circle")
            .attr("class", "highlighted-border")
            .attr("r", 35)
            .attr("fill", "none")
            .attr("stroke", "black")
            .attr("stroke-width", 3);

          bubbles
            .append("text")
            .attr("text-anchor", "middle")
            .attr("dy", ".3em")
            .attr("fill", "black")
            .attr("font-size", "12px")
            .text((d) => d.关键词.split(",")[0]);

          const radiusScale = d3.scaleSqrt()
            .domain([0, d3.max(selectedData, (d) => d.情感值)])
            .range([15, 30]);

          bubbles
            .select("circle")
            .transition()
            .duration(1000)
            .attr("r", (d) => radiusScale(d.情感值));
        }

        updateBubbleChart("正面");
      });
    });

    return {
      currentSongName,
      currentSongLyrics,
      isAudioPlayerVisible,
      closeLyricsModal,
      showLyrics,
    };
  },
};
</script>



<style scoped>
body {
  font-family: Arial, sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0;
  padding: 0;
  background-color: #fff6df;
}

.title {
  text-align: center;
  margin-top: 2%; /* 使顶部间距相对于屏幕高度自适应 */
  font-size: 2vw; /* 根据视口宽度调整字体大小 */
  color: #d87b6b;
}

.bar-chart-container {
  margin-top: 2%; /* 顶部间距调整为5% */
  margin-bottom: 2%; /* 底部间距调整为5% */
}

.bubble-chart-container {
  margin-top: 2%; /* 顶部间距调整为2% */
  margin-bottom: 2%; /* 底部间距调整为2% */
}

.tooltip {
  position: absolute;
  display: none;
  background-color: #f7f7f7;
  border: 1px solid #ccc;
  padding: 1.5%; /* 边距使用百分比，使其相对自适应 */
  font-size: 1vw; /* 使字体大小根据视口宽度变化 */
  color: #333;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.1);
}

.lyrics-modal {
  display: none;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 80%; /* 宽度100%，占满屏幕 */
  height: 80%; /* 高度100%，占满屏幕 background-color: rgba(0, 0, 0, 0.6);*/
}

.lyrics-modal-content {
  background-color: #fefefe;
  padding: 2%; /* 边距使用百分比，使其自适应 */
  border: 1px solid #888;
  width: 60%; /* 内容宽度为80% */
  margin: 5% auto; /* 垂直居中，顶部和底部间距10% */
}

.close-button {
  color: #aaa;
  float: right;
  font-size: 2vw; /* 字体大小使用视口宽度单位，适应屏幕大小 */
  font-weight: bold;
}

.close-button:hover,
.close-button:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

.song-title {
  font-size: 1.8vw; /* 字体大小根据视口宽度调整 */
  color: #333;
}

.song-lyrics {
  font-size: 1.2vw; /* 字体大小根据视口宽度调整 */
  color: #555;
  white-space: pre-wrap;
}

#audio-player {
  display: none;
  margin-top: 5%; /* 顶部间距使用百分比 */
}

</style>
