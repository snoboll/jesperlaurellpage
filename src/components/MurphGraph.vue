<template>
  <div class="murph-graph">
    <canvas ref="murphChart"></canvas>
  </div>
</template>

<script>
import { Chart, registerables } from "chart.js";
Chart.register(...registerables);

export default {
  name: "MurphGraph",
  mounted() {
    this.createChart();
  },
  methods: {
    createChart() {
      const ctx = this.$refs.murphChart.getContext("2d");
      
      // Create gradient
      const gradient = ctx.createLinearGradient(0, 0, 0, 300);
      gradient.addColorStop(0, "rgba(255, 140, 0, 0.25)");
      gradient.addColorStop(1, "rgba(255, 140, 0, 0)");
      
      new Chart(ctx, {
        type: "line",
        data: {
          labels: this.getLabels(),
          datasets: [
            {
              label: "Time (min)",
              data: this.getRegularMurphData(),
              borderColor: "#ff8c00",
              backgroundColor: gradient,
              borderWidth: 2,
              tension: 0.3,
              fill: true,
              spanGaps: true,
              pointBackgroundColor: "#ff8c00",
              pointBorderColor: "#1a1a1a",
              pointBorderWidth: 2,
              pointRadius: 4,
              pointHoverRadius: 6,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              display: false,
            },
            tooltip: {
              backgroundColor: "rgba(26, 26, 26, 0.95)",
              titleColor: "#ff8c00",
              bodyColor: "rgba(240, 240, 240, 0.9)",
              borderColor: "rgba(255, 140, 0, 0.3)",
              borderWidth: 1,
              padding: 12,
              cornerRadius: 8,
              titleFont: {
                family: "'Outfit', sans-serif",
                weight: "600",
              },
              bodyFont: {
                family: "'JetBrains Mono', monospace",
              },
              callbacks: {
                label: function (context) {
                  return context.parsed.y.toFixed(1) + " min";
                },
              },
            },
          },
          scales: {
            x: {
              type: "category",
              ticks: {
                maxRotation: 45,
                minRotation: 45,
                color: "rgba(240, 240, 240, 0.5)",
                font: {
                  family: "'JetBrains Mono', monospace",
                  size: 10,
                },
              },
              grid: {
                color: "rgba(255, 140, 0, 0.08)",
                drawBorder: false,
              },
            },
            y: {
              title: {
                display: true,
                text: "Time (minutes)",
                color: "rgba(240, 240, 240, 0.7)",
                font: {
                  family: "'Outfit', sans-serif",
                  size: 12,
                },
              },
              ticks: {
                color: "rgba(240, 240, 240, 0.5)",
                font: {
                  family: "'JetBrains Mono', monospace",
                  size: 11,
                },
              },
              grid: {
                color: "rgba(255, 140, 0, 0.08)",
                drawBorder: false,
              },
            },
          },
        },
      });
    },
    getLabels() {
      return [
        "2020-10",
        "2020-11",
        "2020-12",
        "2021-01",
        "2021-02",
        "2021-03",
        "2021-04",
        "2021-05",
        "2021-06",
        "2021-07",
        "2021-08",
        "2021-09",
        "2021-10",
        "2021-11",
        "2021-12",
        "2022-01",
        "2022-02",
        "2022-03",
        "2022-04",
        "2022-05",
        "2022-06",
        "2022-07",
        "2022-08",
        "2022-09",
        "2022-10",
        "2022-11",
        "2022-12",
        "2023-01",
        "2023-02",
        "2023-03",
        "2023-04",
        "2023-05",
        "2023-06",
        "2023-07",
        "2023-08",
        "2023-09",
        "2023-10",
        "2023-11",
        "2023-12",
        "2024-01",
        "2024-02",
        "2024-03",
      ];
    },
    getRegularMurphData() {
      return [
        37.82,
        45.4,
        43.92,
        null,
        44.57,
        40.18,
        47.07,
        37.0,
        50.48,
        40.25,
        41.5,
        null,
        null,
        43.17,
        null,
        36.98,
        48.52,
        null,
        54.15,
        null,
        39.62,
        43.9,
        null,
        null,
        44.03,
        39.98,
        38.98,
        44.67,
        55.97,
        null,
        47.38,
        59.43,
        null,
        null,
        null,
        null,
        35.45,
        null,
        null,
        40.37,
        null,
        null,
      ];
    },
  },
};
</script>

<style scoped>
.murph-graph {
  width: 100%;
  height: 300px;
  max-width: 800px;
  margin: 0 auto;
}
</style>
