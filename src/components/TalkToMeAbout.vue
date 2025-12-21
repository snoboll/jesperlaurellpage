<template>
  <section class="talk-to-me-about">
    <h2>Talk to Me About</h2>
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
  margin-top: 2rem;
  text-align: center;
  opacity: 0;
  animation: fadeInUp 0.8s cubic-bezier(0.16, 1, 0.3, 1) 1.5s forwards;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(40px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

h2 {
  font-size: 2rem;
  font-weight: 600;
  color: #ff8c00;
  margin-bottom: 2rem;
  font-family: "Outfit", sans-serif;
  letter-spacing: -0.02em;
}

.topic-list {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.topic-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: linear-gradient(145deg, #2d2d2d 0%, #252525 100%);
  border-radius: 16px;
  padding: 2rem;
  width: 100%;
  max-width: 800px;
  border: 1px solid rgba(255, 140, 0, 0.1);
  position: relative;
  overflow: hidden;
  transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}

.topic-item::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 2px;
  background: linear-gradient(90deg, 
    transparent 0%, 
    rgba(255, 140, 0, 0.5) 50%, 
    transparent 100%);
}

.topic-item:hover {
  transform: translateY(-5px);
  border-color: rgba(255, 140, 0, 0.25);
  box-shadow: 
    0 15px 35px rgba(0, 0, 0, 0.25),
    0 0 25px rgba(255, 140, 0, 0.06);
}

.topic-header {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-bottom: 1rem;
}

.topic-icon {
  font-size: 1.75rem;
  color: #ff8c00;
  transition: transform 0.3s ease;
}

.topic-item:hover .topic-icon {
  transform: scale(1.15);
}

.topic-text {
  font-size: 1.25rem;
  font-weight: 500;
  color: #ff8c00;
  font-family: "Outfit", sans-serif;
}

@media (max-width: 768px) {
  .topic-item {
    width: 100%;
    padding: 1.5rem;
  }

  h2 {
    font-size: 1.6rem;
  }

  .topic-icon {
    font-size: 1.5rem;
  }

  .topic-text {
    font-size: 1.1rem;
  }
}
</style>
