<template>
  <div class="murph-wrapper" ref="wrapper">
    <div class="murph-stats" :class="{ visible: isVisible }">
      <div class="stat">
        <span class="stat-label">Best Time</span>
        <span class="stat-value">{{ bestTime }}<span class="stat-unit">min</span></span>
      </div>
      <div class="stat-divider"></div>
      <div class="stat">
        <span class="stat-label">Latest</span>
        <span class="stat-value">{{ latestTime }}<span class="stat-unit">min</span></span>
      </div>
      <div class="stat-divider"></div>
      <div class="stat">
        <span class="stat-label">Murphs</span>
        <span class="stat-value">{{ attempts }}</span>
      </div>
    </div>

    <div class="murph-graph" :class="{ visible: isVisible }">
      <div v-if="loading" class="skeleton"></div>
      <canvas v-else ref="murphChart"></canvas>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount, nextTick } from "vue";
import { Chart, registerables } from "chart.js";
import "chartjs-adapter-date-fns";
import { enUS } from "date-fns/locale";
Chart.register(...registerables);

// Each attempt: { date: "YYYY-MM-DD", time: minutes, type: "regular" | "weighted" | "team" }
// regular  = solo, bodyweight
// weighted = with vest (9kg, 10kg, 50%, etc.)
// team     = partner / kompismurph
const ATTEMPTS = [
  // 2020
  { date: "2020-10-20", time: 37.82, type: "regular" },
  { date: "2020-11-12", time: 45.40, type: "regular" },
  { date: "2020-12-02", time: 43.92, type: "regular" },
  // 2021
  { date: "2021-01-17", time: 64.12, type: "weighted" },
  { date: "2021-01-29", time: 44.57, type: "regular" },
  { date: "2021-02-09", time: 40.18, type: "regular" },
  { date: "2021-02-17", time: 47.07, type: "regular" },
  { date: "2021-02-25", time: 37.00, type: "regular" },
  { date: "2021-03-02", time: 50.48, type: "regular" },
  { date: "2021-03-09", time: 40.25, type: "regular" },
  { date: "2021-03-14", time: 41.50, type: "regular" },
  { date: "2021-06-01", time: 43.17, type: "regular" },
  { date: "2021-08-23", time: 36.98, type: "regular" },
  { date: "2021-09-29", time: 48.52, type: "regular" },
  { date: "2021-10-12", time: 59.70, type: "weighted" },
  { date: "2021-11-01", time: 54.15, type: "regular" },
  { date: "2021-12-03", time: 58.88, type: "weighted" },
  // 2022
  { date: "2022-01-10", time: 39.62, type: "regular" },
  { date: "2022-01-30", time: 43.90, type: "regular" },
  { date: "2022-05-06", time: 44.03, type: "regular" },
  { date: "2022-05-30", time: 39.98, type: "regular" },
  { date: "2022-07-11", time: 38.98, type: "regular" },
  { date: "2022-07-18", time: 60.13, type: "regular" },
  { date: "2022-07-31", time: 44.67, type: "regular" },
  { date: "2022-08-30", time: 55.97, type: "regular" },
  // 2023
  { date: "2023-02-02", time: 47.38, type: "regular" },
  { date: "2023-03-27", time: 59.43, type: "regular" },
  { date: "2023-10-09", time: 55.72, type: "weighted" },
  { date: "2023-10-16", time: 35.45, type: "regular" },
  { date: "2023-12-04", time: 48.22, type: "weighted" },
  // 2024
  { date: "2024-01-26", time: 40.37, type: "regular" },
  { date: "2024-02-28", time: 49.28, type: "weighted" },
  { date: "2024-03-20", time: 55.00, type: "team" },
  { date: "2024-05-27", time: 50.82, type: "weighted" },
  { date: "2024-07-14", time: 54.03, type: "team" },
  { date: "2024-08-31", time: 40.42, type: "regular" },
  { date: "2024-10-14", time: 43.17, type: "team" },
  { date: "2024-11-04", time: 41.43, type: "weighted" },
  // 2025
  { date: "2025-01-05", time: 59.33, type: "team" },
  { date: "2025-01-31", time: 38.15, type: "regular" },
  { date: "2025-05-26", time: 48.55, type: "weighted" },
  { date: "2025-07-23", time: 44.00, type: "team" },
  { date: "2025-08-18", time: 39.50, type: "regular" },
  { date: "2025-08-25", time: 40.08, type: "regular" },
  { date: "2025-09-01", time: 41.97, type: "weighted" },
  { date: "2025-09-08", time: 36.03, type: "regular" },
  { date: "2025-09-15", time: 39.53, type: "regular" },
  { date: "2025-09-22", time: 40.93, type: "regular" },
  { date: "2025-09-29", time: 35.97, type: "regular" },
  { date: "2025-10-06", time: 37.77, type: "regular" },
  { date: "2025-10-13", time: 36.18, type: "regular" },
  { date: "2025-10-20", time: 43.88, type: "weighted" },
  // 2026
  { date: "2026-02-14", time: 50.00, type: "team" },
];

function buildSeries(type) {
  return ATTEMPTS
    .filter((a) => a.type === type)
    .map((a) => ({ x: new Date(a.date), y: a.time }));
}

const REGULAR = buildSeries("regular");
const WEIGHTED = buildSeries("weighted");
const TEAM = buildSeries("team");

const wrapper = ref(null);
const murphChart = ref(null);
const isVisible = ref(false);
const loading = ref(false);
let chartInstance = null;
let observer = null;

const regularTimes = REGULAR.map((p) => p.y);
const attempts = computed(() => ATTEMPTS.length);
const bestTime = computed(() => Math.min(...regularTimes).toFixed(1));
const latestTime = computed(() =>
  regularTimes.length ? regularTimes[regularTimes.length - 1].toFixed(1) : "—"
);

function createChart() {
  const ctx = murphChart.value.getContext("2d");
  const bestVal = parseFloat(bestTime.value);
  const reducedMotion = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

  const gradient = ctx.createLinearGradient(0, 0, 0, 260);
  gradient.addColorStop(0, "rgba(255, 140, 0, 0.22)");
  gradient.addColorStop(1, "rgba(255, 140, 0, 0)");

  // Highlight PB on regular series (whichever attempt matches the min)
  const regularPointColors = REGULAR.map((p) =>
    p.y === bestVal ? "#fff" : "#ff8c00"
  );
  const regularPointBorders = REGULAR.map((p) =>
    p.y === bestVal ? "#ff8c00" : "#0b0b0b"
  );
  const regularPointRadii = REGULAR.map((p) => (p.y === bestVal ? 6 : 3));

  chartInstance = new Chart(ctx, {
    type: "line",
    data: {
      datasets: [
        {
          label: "Regular Cindy",
          data: REGULAR,
          borderColor: "#ff8c00",
          backgroundColor: gradient,
          borderWidth: 2,
          tension: 0,
          fill: true,
          pointBackgroundColor: regularPointColors,
          pointBorderColor: regularPointBorders,
          pointBorderWidth: 2,
          pointRadius: regularPointRadii,
          pointHoverRadius: 6,
          pointHoverBackgroundColor: "#ffb347",
          pointHoverBorderColor: "#0b0b0b",
          pointHoverBorderWidth: 2,
        },
        {
          label: "Weighted Cindy",
          data: WEIGHTED,
          borderColor: "#ffb347",
          backgroundColor: "rgba(255, 179, 71, 0)",
          borderWidth: 1.8,
          borderDash: [5, 4],
          tension: 0,
          fill: false,
          pointBackgroundColor: "#ffb347",
          pointBorderColor: "#0b0b0b",
          pointBorderWidth: 2,
          pointRadius: 3,
          pointHoverRadius: 5,
        },
        {
          label: "Team Murph",
          data: TEAM,
          borderColor: "#f0ece4",
          backgroundColor: "rgba(240, 236, 228, 0)",
          borderWidth: 1.8,
          borderDash: [2, 4],
          tension: 0,
          fill: false,
          pointBackgroundColor: "#f0ece4",
          pointBorderColor: "#0b0b0b",
          pointBorderWidth: 2,
          pointRadius: 3,
          pointHoverRadius: 5,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      animation: { duration: reducedMotion ? 0 : 800, easing: "easeInOutQuart" },
      interaction: { intersect: false, mode: "nearest", axis: "x" },
      plugins: {
        legend: {
          display: true,
          position: "top",
          align: "end",
          labels: {
            color: "rgba(240, 236, 228, 0.65)",
            font: { family: "'IBM Plex Mono', monospace", size: 10, weight: "500" },
            usePointStyle: true,
            pointStyle: "line",
            boxWidth: 24,
            boxHeight: 2,
            padding: 14,
          },
        },
        tooltip: {
          backgroundColor: "rgba(10, 10, 10, 0.95)",
          titleColor: "#ff8c00",
          bodyColor: "rgba(240, 236, 228, 0.9)",
          borderColor: "rgba(255, 140, 0, 0.2)",
          borderWidth: 1,
          padding: { x: 14, y: 10 },
          cornerRadius: 10,
          titleFont: { family: "'IBM Plex Sans', sans-serif", weight: "600", size: 12 },
          bodyFont: { family: "'IBM Plex Mono', monospace", size: 12, weight: "500" },
          callbacks: {
            title: (items) => {
              const d = new Date(items[0].parsed.x);
              return d.toLocaleDateString("en-US", {
                month: "short",
                day: "numeric",
                year: "numeric",
              });
            },
            label: (ctx) => {
              const isPR =
                ctx.dataset.label === "Regular Cindy" &&
                ctx.parsed.y === parseFloat(bestTime.value);
              return ` ${ctx.dataset.label}: ${ctx.parsed.y.toFixed(1)} min${isPR ? "  ★ PB" : ""}`;
            },
          },
        },
      },
      scales: {
        x: {
          type: "time",
          time: { unit: "year", displayFormats: { year: "yyyy" } },
          adapters: { date: { locale: enUS } },
          ticks: {
            color: "rgba(240, 236, 228, 0.3)",
            font: { family: "'IBM Plex Mono', monospace", size: 10 },
            maxRotation: 0,
          },
          grid: { color: "rgba(255, 255, 255, 0.03)", drawBorder: false },
          border: { display: false },
        },
        y: {
          min: 30,
          ticks: {
            color: "rgba(240, 236, 228, 0.35)",
            font: { family: "'IBM Plex Mono', monospace", size: 11, weight: "500" },
            callback: (v) => v + "m",
          },
          grid: { color: "rgba(255, 255, 255, 0.03)", drawBorder: false },
          border: { display: false },
        },
      },
    },
  });
}

onMounted(() => {
  observer = new IntersectionObserver(
    ([entry]) => {
      if (entry.isIntersecting) {
        isVisible.value = true;
        nextTick(createChart);
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
.murph-wrapper {
  width: 100%;
}

.murph-stats {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1.25rem;
  background: rgba(255, 255, 255, 0.02);
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 10px;
  padding: 0.75rem 1.5rem;
  opacity: 0;
  transform: translateY(12px);
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.murph-stats.visible {
  opacity: 1;
  transform: translateY(0);
}

@media (prefers-reduced-motion: reduce) {
  .murph-stats,
  .murph-graph {
    transition: none;
    opacity: 1;
    transform: none;
  }
}

.stat {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.15rem;
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

.stat-unit {
  font-size: 0.8rem;
  font-weight: 400;
  color: rgba(255, 165, 0, 0.45);
  margin-left: 2px;
}

.stat-divider {
  width: 1px;
  height: 34px;
  background: rgba(255, 255, 255, 0.07);
  flex-shrink: 0;
}

.murph-graph {
  width: 100%;
  height: 260px;
  opacity: 0;
  transform: translateY(16px);
  transition: opacity 0.6s ease 0.15s, transform 0.6s ease 0.15s;
}

.murph-graph.visible {
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
  .skeleton { animation: none; background: rgba(255, 140, 0, 0.06); }
}

@media (max-width: 480px) {
  .murph-graph { height: 210px; }
  .stat { padding: 0 0.85rem; }
  .stat-value { font-size: 1.3rem; }
  .stat-label { font-size: 0.6rem; }
}
</style>
