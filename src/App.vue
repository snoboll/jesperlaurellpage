<template>
  <div id="app">
    <header>
      <h1>Jesper Laurell</h1>
      <p class="tagline">RootPi Co-founder | M.Sc. Electrical Engineering, LTH | UX, Flutter & Game Design Enthusiast</p>
    </header>

    <main class="main-content">
      <section class="hero">
        <div class="hero-content">
          <h2>Hello, I'm Jesper</h2>
          <p>My passion is to create innovative solutions to real-world problems that feel good to use</p>
          <a href="#contact" class="cta-button" @click.prevent="scrollToContact">Get in touch</a>
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

      <section id="contact" class="contact">
        <h2>Get in Touch</h2>
        <div class="contact-card">
          <form @submit.prevent="submitForm" class="contact-form">
            <div class="form-group">
              <label for="name">Name</label>
              <input type="text" id="name" v-model="contactForm.name" required>
            </div>
            <div class="form-group">
              <label for="email">Email</label>
              <input type="email" id="email" v-model="contactForm.email" required>
            </div>
            <div class="form-group">
              <label for="message">Message</label>
              <textarea id="message" v-model="contactForm.message" required></textarea>
            </div>
            <button type="submit" class="submit-button">Send Message</button>
          </form>
          <div class="contact-info">
            <h3>Contact Information</h3>
            <p><strong>Email:</strong> <span>hello@jesperlaurell.com</span></p>
            <p><strong>Phone:</strong> <span>+46 708 88 95 36</span></p>
            <p><strong>Location:</strong> <span>Malmö, Sweden</span></p>
            <div class="social-links">
              <a href="#" target="_blank" class="social-link" aria-label="LinkedIn">
                <font-awesome-icon :icon="['fab', 'linkedin']" />
              </a>
              <a href="#" target="_blank" class="social-link" aria-label="GitHub">
                <font-awesome-icon :icon="['fab', 'github']" />
              </a>
              <a href="#" target="_blank" class="social-link" aria-label="Twitter">
                <font-awesome-icon :icon="['fab', 'twitter']" />
              </a>
            </div>
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
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { library } from '@fortawesome/fontawesome-svg-core'
import { faLinkedin, faGithub, faTwitter } from '@fortawesome/free-brands-svg-icons'

library.add(faLinkedin, faGithub, faTwitter)

export default {
  name: "App",
  components: {
    FontAwesomeIcon
  },
  data() {
    return {
      skills: [
        "JavaScript", "Vue", "Python", "Laravel", "PHP", "OpenAI", "CSS", "HTML",
        "UI/UX Design", "GitHub", "Node.js", "Java", "IaaS", "Responsive Web Design",
        "Flutter", "Dart", "iOS/Android App Dev", "Scrum", "Machine Learning",
        "Reinforcement Learning", "React", "SQL", "Unity", "Game Dev", "C#",
        "Blender", "VR Development", "Meta Quest Dev"
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
      contactForm: {
        name: '',
        email: '',
        message: ''
      }
    };
  },
  methods: {
    scrollToContact() {
      const contactSection = document.getElementById('contact');
      contactSection.scrollIntoView({ behavior: 'smooth' });
    },
    submitForm() {
      // Handle form submission here
      console.log('Form submitted:', this.contactForm);
      // Reset form after submission
      this.contactForm = { name: '', email: '', message: '' };
    }
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&family=Open+Sans:wght@400;600&display=swap');

body {
  margin: 0;
  padding: 0;
  background: linear-gradient(to right, #1a1a1a, #8b4513, #1a1a1a);
  color: #f0f0f0;
  font-family: 'Open Sans', sans-serif;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
  overflow-x: hidden;
}

#app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  box-sizing: border-box;
  width: 100%;
  background: rgba(26, 26, 26, 0.8);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
  border-radius: 10px;
  overflow-x: hidden;
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
  font-size: 1.2rem;
  color: #ffa500;
  max-width: 800px;
  margin: 0 auto;
  line-height: 1.4;
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
  justify-content: center;
}

.skill-tag {
  background-color: #ff8c00;
  color: #1a1a1a;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  transition: transform 0.3s ease, background-color 0.3s ease;
}

.skill-tag:hover {
  transform: translateY(-2px);
  background-color: #ffa500;
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

.contact {
  background-color: #2a2a2a;
  border-radius: 10px;
  padding: 2rem;
  margin-top: 4rem;
}

.contact h2 {
  text-align: center;
  margin-bottom: 2rem;
}

.contact-card {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  background-color: #333333;
  border-radius: 10px;
  padding: 2rem;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
}

.contact-form {
  flex: 1;
  width: 100%;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  color: #ffa500;
}

.form-group input,
.form-group textarea {
  width: 100%;
  padding: 0.8rem;
  border: 1px solid #444;
  border-radius: 5px;
  background-color: #2a2a2a;
  color: #f0f0f0;
  font-size: 16px;
  box-sizing: border-box;
}

.form-group textarea {
  height: 120px;
  resize: vertical;
}

.submit-button {
  background-color: #ff8c00;
  color: #1a1a1a;
  padding: 0.8rem 1.5rem;
  border: none;
  border-radius: 25px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s ease;
  width: 100%;
}

.contact-info {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

.contact-info h3 {
  color: #ff8c00;
  margin-bottom: 1rem;
}

.contact-info p {
  margin-bottom: 0.5rem;
  display: flex;
  align-items: center;
}

.contact-info strong {
  min-width: 80px;
  display: inline-block;
}

.social-links {
  margin-top: 1rem;
  display: flex;
  gap: 1rem;
}

.social-link {
  color: #ffa500;
  text-decoration: none;
  font-size: 1.5rem;
  transition: color 0.3s ease;
}

.social-link:hover {
  color: #ff8c00;
}

@media (max-width: 768px) {
  #app {
    padding: 1rem;
  }

  h1 {
    font-size: 2.2rem;
  }

  .tagline {
    font-size: 0.9rem;
  }

  .hero {
    flex-direction: column-reverse;
    text-align: center;
    gap: 1rem;
  }

  .hero-content {
    margin-top: 1rem;
  }

  .hero h2 {
    font-size: 1.8rem;
  }

  .profile-image {
    width: 180px;
    height: 180px;
  }

  .skills, .featured-projects, .contact {
    padding: 1.5rem;
  }

  .skill-tags {
    gap: 0.5rem;
  }

  .skill-tag {
    font-size: 0.75rem;
    padding: 0.3rem 0.6rem;
  }

  .project-grid {
    grid-template-columns: 1fr;
  }

  .contact-card {
    padding: 1.5rem;
  }

  .contact-info {
    margin-top: 1rem;
  }

  .social-links {
    flex-wrap: wrap;
  }

  .form-group input,
  .form-group textarea {
    font-size: 16px;
    padding: 0.6rem;
  }

  .main-content {
    gap: 2rem;
  }
}

@media (max-width: 480px) {
  h1 {
    font-size: 2rem;
  }

  .hero h2 {
    font-size: 1.6rem;
  }

  .profile-image {
    width: 150px;
    height: 150px;
  }

  .skill-tag {
    font-size: 0.7rem;
    padding: 0.2rem 0.5rem;
  }
}

/* Improve touch targets */
.cta-button,
.submit-button,
.project-link,
.social-link {
  padding: 0.8rem 1.2rem;
  min-height: 44px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.skill-tag {
  min-height: 32px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

/* Improve form usability on mobile */
.form-group input,
.form-group textarea {
  padding: 0.8rem;
  min-height: 44px;
}

/* Ensure content doesn't overflow */
img, video, iframe {
  max-width: 100%;
  height: auto;
}

/* Add this new media query */
@media (max-width: 1240px) {
  body {
    background-attachment: scroll;
  }
}
</style>
