<template>
  <div class="bitcoin-chart">
    <canvas ref="bitcoinChart"></canvas>
  </div>
</template>

<script>
import { Chart, registerables } from "chart.js";
import "chartjs-adapter-date-fns";
import { enUS } from "date-fns/locale";

Chart.register(...registerables);

export default {
  name: "BitcoinChart",
  mounted() {
    this.createChart();
  },
  methods: {
    async createChart() {
      const data = await this.fetchBitcoinData();
      const ctx = this.$refs.bitcoinChart.getContext("2d");
      
      // Create gradient
      const gradient = ctx.createLinearGradient(0, 0, 0, 300);
      gradient.addColorStop(0, "rgba(255, 140, 0, 0.3)");
      gradient.addColorStop(1, "rgba(255, 140, 0, 0)");
      
      new Chart(ctx, {
        type: "line",
        data: {
          labels: data.map((d) => new Date(d[0])),
          datasets: [
            {
              label: "USD/BTC",
              data: data.map((d) => 1 / d[1]),
              borderColor: "#ff8c00",
              backgroundColor: gradient,
              borderWidth: 2,
              fill: true,
              tension: 0.4,
              pointRadius: 0,
              pointHoverRadius: 6,
              pointHoverBackgroundColor: "#ff8c00",
              pointHoverBorderColor: "#1a1a1a",
              pointHoverBorderWidth: 2,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          interaction: {
            intersect: false,
            mode: "index",
          },
          plugins: {
            legend: {
              labels: {
                color: "rgba(240, 240, 240, 0.8)",
                font: {
                  family: "'Outfit', sans-serif",
                  size: 12,
                },
                padding: 20,
                usePointStyle: true,
                pointStyle: "circle",
              },
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
                  let label = context.dataset.label || "";
                  if (label) {
                    label += ": ";
                  }
                  if (context.parsed.y !== null) {
                    label += (1 / context.parsed.y).toFixed(2) + " BTC/USD";
                  }
                  return label;
                },
              },
            },
          },
          scales: {
            x: {
              type: "time",
              time: {
                unit: "month",
              },
              adapters: {
                date: {
                  locale: enUS,
                },
              },
              ticks: {
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
                text: "USD/BTC",
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
                callback: function (value) {
                  return value.toFixed(8);
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
    async fetchBitcoinData() {
      const response = await fetch(
        "https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=usd&days=365&interval=daily"
      );
      const data = await response.json();
      return data.prices;
    },
  },
};
</script>

<style scoped>
.bitcoin-chart {
  width: 100%;
  height: 300px;
  max-width: 800px;
  margin: 0 auto;
}
</style>
