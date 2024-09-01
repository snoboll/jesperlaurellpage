<template>
  <div id="app">
    <header>
      <h1>Jesper Laurell</h1>
      <p class="tagline">Innovative Developer & Tech Enthusiast</p>
    </header>

    <main class="main-content">
      <section class="hero">
        <div class="hero-content">
          <h2>Hello, I'm Jesper</h2>
          <p>A passionate developer with a keen interest in creating innovative solutions.</p>
          <a href="#contact" class="cta-button">Get in touch</a>
        </div>
        <img src="@/assets/profile-image.jpg" alt="Jesper Laurell" class="profile-image" />
      </section>

      <section class="skills">
        <h2>Skills</h2>
        <div class="skill-tags">
          <span v-for="skill in skills" :key="skill" class="skill-tag">{{ skill }}</span>
        </div>
      </section>

      <section class="featured-projects">
        <h2>Featured Projects</h2>
        <div class="project-grid">
          <div v-for="project in featuredProjects" :key="project.name" class="project-card">
            <h3>{{ project.name }}</h3>
            <p>{{ project.description }}</p>
            <p>Language: {{ project.language }}</p>
            <a :href="project.url" target="_blank" class="project-link">View Project</a>
          </div>
        </div>
      </section>
    </main>

    <footer>
      <p>&copy; {{ new Date().getFullYear() }} Jesper Laurell. All rights reserved.</p>
    </footer>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      skills: [
        "JavaScript", "Vue.js", "React", "Node.js",
        "Python", "Django",
        "Java", "Spring Boot",
        "TypeScript", "GraphQL", "REST API",
        "HTML5", "CSS3", "Sass",
        "Git", "GitHub",
        "Docker", "Kubernetes",
        "AWS", "Azure",
        "MongoDB", "PostgreSQL",
        "Machine Learning", "TensorFlow",
        "Data Analysis", "Pandas",
        "UI/UX Design",
        "Agile Methodologies",
        "Test-Driven Development",
        "CI/CD",
        "Microservices Architecture",
        "Responsive Web Design",
        "Performance Optimization"
      ],
      featuredProjects: [
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
          name: 'TodoDjango',
          description: 'A Todo application built with Django',
          language: 'Python',
          url: 'https://github.com/snoboll/TodoDjango'
        }
      ],
      experience: [
        {
          title: "Senior Developer at TechCorp",
          period: "2018 - Present",
          achievements: [
            "Led development of cutting-edge web applications",
            "Mentored junior developers and improved team productivity",
            "Implemented CI/CD pipelines, reducing deployment time by 50%",
          ],
        },
        // Add more job experiences as needed
      ],
    };
  },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&family=Open+Sans:wght@400;600&display=swap');

body {
  margin: 0;
  padding: 0;
  background: #1a1a1a;
  color: #f0f0f0;
  font-family: 'Open Sans', sans-serif;
}

#app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
}

header {
  text-align: center;
  margin-bottom: 4rem;
}

h1, h2, h3, .tagline {
  font-family: 'Poppins', sans-serif;
}

h1 {
  font-size: 3.5rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
  color: #ff8c00;
}

.tagline {
  font-size: 1.3rem;
  color: #ffa500;
}

.main-content {
  display: flex;
  flex-direction: column;
  gap: 4rem;
}

.hero {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 2rem;
}

.hero-content {
  flex: 1;
}

.hero h2 {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  color: #ff8c00;
}

.cta-button {
  display: inline-block;
  background-color: #ff8c00;
  color: #1a1a1a;
  padding: 0.8rem 1.5rem;
  border-radius: 25px;
  text-decoration: none;
  font-weight: 600;
  margin-top: 1rem;
}

.profile-image {
  width: 300px;
  height: 300px;
  border-radius: 50%;
  object-fit: cover;
}

.skills, .featured-projects {
  background-color: #2a2a2a;
  border-radius: 10px;
  padding: 2rem;
}

.skill-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.8rem;
}

.skill-tag {
  background-color: #ff8c00;
  color: #1a1a1a;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.9rem;
  font-weight: 600;
}

.project-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
}

.project-card {
  background-color: #333333;
  border-radius: 10px;
  padding: 1.5rem;
  transition: transform 0.3s ease;
}

.project-card:hover {
  transform: translateY(-5px);
}

.project-card h3 {
  color: #ff8c00;
  margin-top: 0;
}

.project-link {
  display: inline-block;
  margin-top: 1rem;
  color: #ff8c00;
  text-decoration: none;
  font-weight: 600;
}

footer {
  text-align: center;
  margin-top: 4rem;
  color: #ffa500;
}
</style>
