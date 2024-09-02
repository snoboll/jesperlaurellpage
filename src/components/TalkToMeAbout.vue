<template>
  <section class="talk-to-me-about">
    <h2>Talk to Me About</h2>
    <ul class="topic-list">
      <li class="topic-item">
        <span class="topic-icon">💪</span>
        <span class="topic-text">Murph</span>
      </li>
      <li class="topic-item">
        <span class="topic-icon">₿</span>
        <span class="topic-text">Bitcoin</span>
      </li>
      <li class="topic-item">
        <span class="topic-icon">📈</span>
        <span class="topic-text">Self Improvement</span>
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  name: "TalkToMeAbout",
};
</script>

<style scoped>
.talk-to-me-about {
  text-align: center;
}

h2 {
  font-size: 1.5rem;
  color: #ff8c00;
  margin-bottom: 2rem;
}

.topic-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
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
  width: calc(33.333% - 2rem); /* Set width to one-third minus gap */
  max-width: 200px; /* Maximum width for larger screens */
  min-width: 150px; /* Minimum width for smaller screens */
}

.topic-item:hover {
  transform: translateY(-5px);
}

.topic-icon {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.topic-text {
  font-size: 1.2rem;
  color: #f0f0f0;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .topic-item {
    width: calc(50% - 2rem); /* Two columns on medium screens */
  }
}

@media (max-width: 480px) {
  .topic-item {
    width: 100%; /* Full width on small screens */
  }
}
</style>
