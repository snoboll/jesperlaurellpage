<template>
  <section class="talk-to-me-about">
    <h2>Talk to Me About</h2>
    <ul class="topic-list">
      <li class="topic-item">
        <span class="topic-icon">💪</span>
        <span class="topic-text">Murph</span>
        <MurphGraph />
      </li>
      <li class="topic-item">
        <span class="topic-icon">₿</span>
        <span class="topic-text">Bitcoin</span>
        <BitcoinChart />
      </li>
      <li class="topic-item">
        <span class="topic-icon">📈</span>
        <span class="topic-text">Self Improvement</span>
      </li>
    </ul>
  </section>
</template>

<script>
import MurphGraph from "./MurphGraph.vue";
import BitcoinChart from "./BitcoinChart.vue";

export default {
  name: "TalkToMeAbout",
  components: {
    MurphGraph,
    BitcoinChart,
  },
};
</script>

<style scoped>
.talk-to-me-about {
  margin-top: 4rem;
  text-align: center;
}

h2 {
  font-size: 2.5rem;
  color: #ff8c00;
  margin-bottom: 2rem;
}

.topic-list {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  list-style-type: none;
  padding: 0;
}

.topic-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #333333;
  border-radius: 10px;
  padding: 1.5rem;
  transition: transform 0.3s ease;
  width: 100%;
  max-width: 800px;
}

.topic-icon {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.topic-text {
  font-size: 1.2rem;
  color: #f0f0f0;
  margin-bottom: 1rem;
}

@media (max-width: 768px) {
  .topic-item {
    width: 90%;
    padding: 1rem;
  }

  h2 {
    font-size: 2rem;
  }

  .topic-icon {
    font-size: 2rem;
  }

  .topic-text {
    font-size: 1rem;
  }
}
</style>
