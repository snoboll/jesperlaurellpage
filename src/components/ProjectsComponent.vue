<template>
  <div class="projects-component">
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
  name: 'ProjectsComponent',
  data() {
    return {
      projects: [
        {
          name: 'Spacecar',
          description: 'A forked Python project',
          language: 'Python',
          url: 'https://github.com/snoboll/Spacecar'
        },
        {
          name: 'Image-Analysis',
          description: 'Solutions for assignments and home exam in Image analysis course FMAN20',
          language: 'MATLAB',
          url: 'https://github.com/snoboll/Image-Analysis'
        },
        {
          name: 'Artificial-Intelligence',
          description: 'Assignments for course in AI, EDAP01',
          language: 'Python',
          url: 'https://github.com/snoboll/Artificial-Intelligence'
        },
        {
          name: 'Kattis',
          description: 'Solutions for competitive programming problems',
          language: 'Python',
          url: 'https://github.com/snoboll/Kattis'
        },
        {
          name: 'TodoDjango',
          description: 'A Todo application built with Django',
          language: 'Python',
          url: 'https://github.com/snoboll/TodoDjango'
        },
        {
          name: 'Arduino',
          description: 'Arduino programming projects',
          language: 'C++',
          url: 'https://github.com/snoboll/Arduino'
        }
      ]
    }
  }
}
</script>

<style scoped>
.projects-component {
  /* Add your styles here */
}
</style>