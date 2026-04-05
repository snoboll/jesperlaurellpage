<template>
  <div class="bitcoin-wrapper" ref="wrapper">
    <div class="bitcoin-stats" :class="{ visible: isVisible }">
      <template v-if="loading">
        <div class="stat-skeleton" v-for="i in 3" :key="i"></div>
      </template>
      <template v-else>
        <div class="stat">
          <span class="stat-label">$1 today</span>
          <span class="stat-value">{{ formatSats(currentSats) }}</span>
        </div>
        <div class="stat-divider"></div>
        <div class="stat">
          <span class="stat-label">4Y Change</span>
          <span
            class="stat-value"
            :class="yearChange >= 0 ? 'positive' : 'negative'"
          >
            {{ yearChange >= 0 ? "+" : "" }}{{ yearChange }}%
          </span>
        </div>
        <div class="stat-divider"></div>
        <div class="stat">
          <span class="stat-label">USD Peak</span>
          <span class="stat-value">{{ formatSats(yearHigh) }}</span>
        </div>
      </template>
    </div>

    <div class="bitcoin-chart" :class="{ visible: isVisible }">
      <div v-if="loading || !chartReady" class="skeleton"></div>
      <canvas v-show="!loading && chartReady" ref="bitcoinChart"></canvas>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from "vue";
import { Chart, registerables } from "chart.js";
import "chartjs-adapter-date-fns";
import { enUS } from "date-fns/locale";

Chart.register(...registerables);

const wrapper = ref(null);
const bitcoinChart = ref(null);
const isVisible = ref(false);
const loading = ref(true);
const chartReady = ref(false);

const currentSats = ref(null);
const yearChange = ref(null);
const yearHigh = ref(null);

let chartInstance = null;
let observer = null;

const SATS_PER_BTC = 100_000_000;

function formatSats(val) {
  if (val == null) return "—";
  return new Intl.NumberFormat("en-US", { maximumFractionDigits: 0 }).format(val);
}

async function fetchAndRender() {
  const rawData = await fetchBitcoinData();
  loading.value = false;

  if (!rawData.length) return;

  // Invert: USD denominated in BTC (sats per USD)
  const satsSeries = rawData.map((d) => SATS_PER_BTC / d[1]);

  currentSats.value = Math.round(satsSeries[satsSeries.length - 1]);
  yearHigh.value = Math.round(Math.max(...satsSeries));
  const change =
    ((satsSeries[satsSeries.length - 1] - satsSeries[0]) / satsSeries[0]) * 100;
  yearChange.value = parseFloat(change.toFixed(1));

  await nextTick();
  buildChart(rawData, satsSeries);
}

function buildChart(rawData, satsSeries) {
  const ctx = bitcoinChart.value.getContext("2d");
  const reducedMotion = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

  const gradient = ctx.createLinearGradient(0, 0, 0, 280);
  gradient.addColorStop(0, "rgba(255, 140, 0, 0.20)");
  gradient.addColorStop(0.65, "rgba(255, 140, 0, 0.05)");
  gradient.addColorStop(1, "rgba(255, 140, 0, 0)");

  chartInstance = new Chart(ctx, {
    type: "line",
    data: {
      labels: rawData.map((d) => new Date(d[0])),
      datasets: [
        {
          label: "USD Price (sats)",
          data: satsSeries,
          borderColor: "#ff8c00",
          backgroundColor: gradient,
          borderWidth: 2,
          fill: true,
          tension: 0.4,
          pointRadius: 0,
          pointHoverRadius: 5,
          pointHoverBackgroundColor: "#ffb347",
          pointHoverBorderColor: "#1a1a1a",
          pointHoverBorderWidth: 2,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      animation: { duration: reducedMotion ? 0 : 900, easing: "easeInOutQuart" },
      interaction: { intersect: false, mode: "index" },
      plugins: {
        legend: { display: false },
        tooltip: {
          backgroundColor: "rgba(10, 10, 10, 0.95)",
          titleColor: "rgba(240, 240, 240, 0.5)",
          bodyColor: "#ff8c00",
          borderColor: "rgba(255, 140, 0, 0.2)",
          borderWidth: 1,
          padding: { x: 14, y: 10 },
          cornerRadius: 10,
          titleFont: { family: "'IBM Plex Mono', monospace", size: 10 },
          bodyFont: { family: "'IBM Plex Mono', monospace", size: 13, weight: "500" },
          callbacks: {
            title: (items) => {
              const d = new Date(items[0].label);
              return d.toLocaleDateString("en-US", {
                month: "short",
                day: "numeric",
                year: "numeric",
              });
            },
            label: (ctx) =>
              " " +
              new Intl.NumberFormat("en-US").format(Math.round(ctx.parsed.y)) +
              " sats",
          },
        },
      },
      scales: {
        x: {
          type: "time",
          time: { unit: "month" },
          adapters: { date: { locale: enUS } },
          ticks: {
            color: "rgba(240, 240, 240, 0.3)",
            font: { family: "'IBM Plex Mono', monospace", size: 9 },
            maxTicksLimit: 10,
          },
          grid: { color: "rgba(255, 255, 255, 0.03)", drawBorder: false },
          border: { display: false },
        },
        y: {
          ticks: {
            color: "rgba(240, 240, 240, 0.35)",
            font: { family: "'IBM Plex Mono', monospace", size: 10 },
            callback: (v) =>
              new Intl.NumberFormat("en-US", { notation: "compact" }).format(v) +
              " sats",
          },
          grid: { color: "rgba(255, 255, 255, 0.03)", drawBorder: false },
          border: { display: false },
        },
      },
    },
  });

  chartReady.value = true;
}

// Returns array of [timestampMs, usdPrice]
async function fetchFromCoingecko() {
  const res = await fetch(
    "https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=usd&days=1460&interval=daily"
  );
  if (!res.ok) throw new Error(`coingecko ${res.status}`);
  const data = await res.json();
  if (!data.prices || !Array.isArray(data.prices) || !data.prices.length) {
    throw new Error("coingecko empty");
  }
  return data.prices;
}

async function fetchFromBlockchainInfo() {
  const res = await fetch(
    "https://api.blockchain.info/charts/market-price?timespan=4years&format=json&cors=true"
  );
  if (!res.ok) throw new Error(`blockchain.info ${res.status}`);
  const data = await res.json();
  if (!data.values || !Array.isArray(data.values) || !data.values.length) {
    throw new Error("blockchain.info empty");
  }
  return data.values.map((v) => [v.x * 1000, v.y]);
}

async function fetchFromBinance() {
  // 4y ≈ 1460 daily candles; Binance caps limit at 1000, so fetch twice.
  const now = Date.now();
  const fourYearsAgo = now - 1460 * 24 * 60 * 60 * 1000;
  const midpoint = now - 730 * 24 * 60 * 60 * 1000;
  const base = "https://api.binance.com/api/v3/klines?symbol=BTCUSDT&interval=1d&limit=1000";

  const [a, b] = await Promise.all([
    fetch(`${base}&startTime=${fourYearsAgo}&endTime=${midpoint}`).then((r) => r.json()),
    fetch(`${base}&startTime=${midpoint}&endTime=${now}`).then((r) => r.json()),
  ]);
  if (!Array.isArray(a) || !Array.isArray(b) || !a.length || !b.length) {
    throw new Error("binance empty");
  }
  // Kline: [openTime, open, high, low, close, ...]
  return [...a, ...b].map((k) => [k[0], parseFloat(k[4])]);
}

async function fetchBitcoinData() {
  const sources = [fetchFromCoingecko, fetchFromBlockchainInfo, fetchFromBinance];
  for (const source of sources) {
    try {
      const prices = await source();
      // Force chronological order — some sources may return newest-first.
      prices.sort((a, b) => a[0] - b[0]);
      // Downsample to one point per month (first occurrence of each month).
      const monthly = [];
      let lastMonth = -1,
        lastYear = -1;
      for (const price of prices) {
        const date = new Date(price[0]);
        const m = date.getMonth(),
          y = date.getFullYear();
        if (m !== lastMonth || y !== lastYear) {
          monthly.push(price);
          lastMonth = m;
          lastYear = y;
        }
      }
      if (monthly.length) return monthly;
    } catch (err) {
      console.warn("BTC source failed:", err.message);
    }
  }
  return [];
}

onMounted(() => {
  observer = new IntersectionObserver(
    ([entry]) => {
      if (entry.isIntersecting) {
        isVisible.value = true;
        fetchAndRender();
        observer.disconnect();
      }
    },
    { threshold: 0.2 }
  );
  if (wrapper.value) observer.observe(wrapper.value);
});

onBeforeUnmount(() => {
  observer?.disconnect();
  chartInstance?.destroy();
});
</script>

<style scoped>
.bitcoin-wrapper {
  width: 100%;
}

.bitcoin-stats {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1.25rem;
  background: rgba(255, 255, 255, 0.02);
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 10px;
  padding: 0.75rem 1.5rem;
  min-height: 68px;
  opacity: 0;
  transform: translateY(12px);
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.bitcoin-stats.visible {
  opacity: 1;
  transform: translateY(0);
}

.stat {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.25rem;
  padding: 0 1.5rem;
}

.stat-label {
  font-size: 0.65rem;
  font-family: "IBM Plex Mono", monospace;
  color: rgba(240, 236, 228, 0.45);
  text-transform: uppercase;
  letter-spacing: 0.14em;
}

.stat-value {
  font-size: 1.1rem;
  font-weight: 500;
  font-family: "IBM Plex Mono", monospace;
  color: #ff8c00;
  line-height: 1;
  letter-spacing: -0.01em;
}

.stat-value.positive { color: #4ade80; }
.stat-value.negative { color: #f87171; }

.stat-divider {
  width: 1px;
  height: 34px;
  background: rgba(255, 255, 255, 0.07);
  flex-shrink: 0;
}

.stat-skeleton {
  width: 80px;
  height: 44px;
  border-radius: 6px;
  background: rgba(255, 140, 0, 0.07);
  margin: 0 1.5rem;
}

.bitcoin-chart {
  width: 100%;
  height: 260px;
  opacity: 0;
  transform: translateY(16px);
  transition: opacity 0.6s ease 0.15s, transform 0.6s ease 0.15s;
}

.bitcoin-chart.visible {
  opacity: 1;
  transform: translateY(0);
}

.skeleton {
  width: 100%;
  height: 100%;
  border-radius: 8px;
  background: linear-gradient(
    90deg,
    rgba(255, 140, 0, 0.04) 25%,
    rgba(255, 140, 0, 0.09) 50%,
    rgba(255, 140, 0, 0.04) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s ease infinite;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

@media (prefers-reduced-motion: reduce) {
  .bitcoin-stats,
  .bitcoin-chart { transition: none; opacity: 1; transform: none; }
  .skeleton { animation: none; background: rgba(255, 140, 0, 0.06); }
}

@media (max-width: 480px) {
  .bitcoin-chart { height: 210px; }
  .stat { padding: 0 0.85rem; }
  .stat-value { font-size: 0.95rem; }
  .stat-label { font-size: 0.58rem; }
}
</style>
