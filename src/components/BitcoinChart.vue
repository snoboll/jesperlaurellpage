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
      new Chart(ctx, {
        type: "line",
        data: {
          labels: data.map((d) => new Date(d[0])),
          datasets: [
            {
              label: "USD/BTC",
              data: data.map((d) => 1 / d[1]),
              borderColor: "rgb(255, 99, 132)",
              backgroundColor: "rgba(255, 99, 132, 0.5)",
              fill: true,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
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
            },
            y: {
              title: {
                display: true,
                text: "USD/BTC",
              },
              ticks: {
                callback: function (value) {
                  return value.toFixed(8);
                },
              },
            },
          },
          plugins: {
            tooltip: {
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
