<template>
  <div id="app">
    <header>
      <h1>Jesper Laurell</h1>
      <p class="tagline">Innovative Developer & Tech Enthusiast</p>
      <img
        src="@/assets/profile-image.jpg"
        alt="Jesper Laurell"
        class="profile-image"
      />
    </header>

    <main>
      <section class="about">
        <h2>About Me</h2>
        <p>
          Hello! I'm Jesper Laurell, a passionate developer with a keen interest
          in creating innovative solutions. With a strong background in [Your
          Specialties], I strive to push the boundaries of what's possible in
          tech.
        </p>
      </section>

      <section class="skills">
        <h2>Skills</h2>
        <div class="skill-tags">
          <span v-for="skill in skills" :key="skill" class="skill-tag">{{
            skill
          }}</span>
        </div>
      </section>

      <section class="projects">
        <h2>Featured Projects</h2>
        <div class="project-grid">
          <div
            v-for="project in projects"
            :key="project.title"
            class="project-card"
          >
            <h3>{{ project.title }}</h3>
            <p>{{ project.description }}</p>
          </div>
        </div>
      </section>

      <section class="experience">
        <h2>Experience</h2>
        <div v-for="job in experience" :key="job.title" class="job">
          <h3>{{ job.title }}</h3>
          <p>{{ job.period }}</p>
          <ul>
            <li v-for="(achievement, index) in job.achievements" :key="index">
              {{ achievement }}
            </li>
          </ul>
        </div>
      </section>
    </main>

    <footer>
      <p>
        &copy; {{ new Date().getFullYear() }} Jesper Laurell. All rights
        reserved.
      </p>
    </footer>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      skills: [
        "JavaScript",
        "Vue.js",
        "React",
        "Node.js",
        "Python",
        "Machine Learning",
        "UI/UX Design",
      ],
      projects: [
        {
          title: "Project 1",
          description:
            "A brief description of your first featured project and its impact.",
        },
        {
          title: "Project 2",
          description:
            "Details about another significant project you've worked on.",
        },
        {
          title: "Project 3",
          description:
            "Highlight a third project that showcases your diverse skills.",
        },
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
#app {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  line-height: 1.6;
  color: #333;
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
}

header {
  text-align: center;
  padding: 4rem 0;
}

h1 {
  font-size: 3.5rem;
  margin-bottom: 0.5rem;
  color: #2c3e50;
}

.tagline {
  font-size: 1.5rem;
  color: #34495e;
}

.profile-image {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  object-fit: cover;
  margin: 2rem 0;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
}

section {
  background-color: white;
  border-radius: 10px;
  padding: 2rem;
  margin-bottom: 2rem;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

h2 {
  color: #2980b9;
  border-bottom: 2px solid #3498db;
  padding-bottom: 0.5rem;
}

.skill-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.skill-tag {
  background-color: #3498db;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.9rem;
}

.project-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
}

.project-card {
  background-color: #f8f9fa;
  border-radius: 10px;
  padding: 1.5rem;
  transition: transform 0.3s ease;
}

.project-card:hover {
  transform: translateY(-5px);
}

footer {
  text-align: center;
  padding: 2rem 0;
  color: #7f8c8d;
}
</style>
