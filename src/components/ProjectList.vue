<template>
  <div class="project-list">
    <h2>My Projects</h2>
    <ul>
      <li v-for="project in projects" :key="project.name">
        <h3>{{ project.name }}</h3>
        <p>{{ project.description }}</p>
        <p>Language: {{ project.language }}</p>
        <a :href="project.url" target="_blank">View on GitHub</a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'ProjectList',
  // ... rest of the component code remains the same
}
</script>

<style scoped>
.project-list {
  /* Add your styles here */
}
</style>