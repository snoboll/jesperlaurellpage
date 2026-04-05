<template>
  <section class="talk-to-me-about">
    <div class="section-head">
      <p class="eyebrow">02 / Interests</p>
      <h2>Talk to me about</h2>
    </div>
    <ul class="topic-list">
      <li class="topic-item">
        <div class="topic-header">
          <font-awesome-icon :icon="['fas', 'dumbbell']" class="topic-icon" />
          <span class="topic-text">Murph</span>
        </div>
        <MurphGraph />
      </li>
      <li class="topic-item">
        <div class="topic-header">
          <font-awesome-icon :icon="['fab', 'bitcoin']" class="topic-icon" />
          <span class="topic-text">Bitcoin</span>
        </div>
        <BitcoinChart />
      </li>
    </ul>
  </section>
</template>

<script>
import MurphGraph from "./MurphGraph.vue";
import BitcoinChart from "./BitcoinChart.vue";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

export default {
  name: "TalkToMeAbout",
  components: {
    MurphGraph,
    BitcoinChart,
    FontAwesomeIcon,
  },
};
</script>

<style scoped>
.talk-to-me-about {
  opacity: 0;
  animation: reveal 0.9s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

@keyframes reveal {
  from { opacity: 0; transform: translateY(16px); }
  to   { opacity: 1; transform: translateY(0); }
}

.section-head {
  margin-bottom: 2rem;
}

.eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 0.6rem;
  font-family: "IBM Plex Mono", monospace;
  font-size: 0.72rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.18em;
  color: #ff8c00;
  margin: 0 0 1rem;
}

.eyebrow::before {
  content: "";
  width: 28px;
  height: 1px;
  background: #ff8c00;
  opacity: 0.6;
}

.section-head h2 {
  font-family: "IBM Plex Sans", sans-serif;
  font-size: clamp(1.7rem, 3vw, 2.1rem);
  font-weight: 600;
  letter-spacing: -0.03em;
  color: #f0ece4;
  margin: 0;
  line-height: 1.1;
}

.topic-list {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.topic-item {
  display: flex;
  flex-direction: column;
  background: rgba(255, 255, 255, 0.02);
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 14px;
  padding: 2rem;
  box-sizing: border-box;
  transition: border-color 0.4s cubic-bezier(0.16, 1, 0.3, 1),
              transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}

@media (hover: hover) {
  .topic-item:hover {
    border-color: rgba(255, 140, 0, 0.25);
  }
}

.topic-header {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-bottom: 1.25rem;
}

.topic-icon {
  font-size: 1.25rem;
  color: #ff8c00;
}

.topic-text {
  font-family: "IBM Plex Sans", sans-serif;
  font-size: 1.1rem;
  font-weight: 500;
  color: #f0ece4;
  letter-spacing: -0.01em;
}

@media (max-width: 640px) {
  .topic-item {
    padding: 1.5rem;
  }
}
</style>
