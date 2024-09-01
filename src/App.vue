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
        <h2>What I do</h2>
        <div class="project-grid">
          <div v-for="project in featuredProjects" :key="project.name" class="project-card">
            <i :class="['project-icon', project.icon]"></i>
            <h4>{{ project.name }}</h4>
            <p>{{ project.description }}</p>
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
import { faRobot, faBrain, faListCheck, faArrowRight } from '@fortawesome/free-solid-svg-icons'

library.add(faLinkedin, faGithub, faTwitter, faRobot, faBrain, faListCheck, faArrowRight)

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
          name: 'RootPi',
          description: 'Co-founded RootPi, specializing in innovative AI solutions and software development.',
          icon: 'fa-solid fa-robot',
          url: 'https://rootpi.xyz/'
        },
        {
          name: 'currencydebasement2',
          description: 'An interactive visualization of global currency debasement.',
          icon: 'fa-solid fa-brain',
          url: 'https://github.com/snoboll/c'
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

/* Global Styles */
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

h1, h2, h3, .tagline {
  font-family: 'Poppins', sans-serif;
}

/* Header Styles */
header {
  text-align: center;
  margin-bottom: 4rem;
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

/* Main Content Styles */
.main-content {
  display: flex;
  flex-direction: column;
  gap: 4rem;
}

/* Hero Section Styles */
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

.profile-image {
  width: 300px;
  height: 300px;
  border-radius: 50%;
  object-fit: cover;
}

/* Skills and Projects Section Styles */
.skills, .featured-projects, .contact {
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
  min-height: 32px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.skill-tag:hover {
  transform: translateY(-2px);
  background-color: #ffa500;
}

/* Updated Project Card Styles */
.project-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 2rem;
}

.project-card {
  background-color: #333333;
  border-radius: 15px;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  position: relative;
  overflow: hidden;
}

.project-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 5px;
  background: linear-gradient(to right, #ff8c00, #ffa500);
}

.project-card h3 {
  color: #ff8c00;
  margin-top: 0;
  margin-bottom: 1rem;
  font-size: 1.5rem;
}

.project-card p {
  color: #f0f0f0;
  margin-bottom: 1.5rem;
  flex-grow: 1;
}

.project-icon {
  font-size: 2.5rem;
  color: #ffa500;
  margin-bottom: 1rem;
}

.project-link {
  color: #1a1a1a; /* Ensure text color contrasts with the background */
  border: 1px solid #ffa500;
  text-decoration: none;
  font-weight: 600;
  padding: 0.8rem 1.2rem;
  border-radius: 25px;
  display: inline-flex;
  align-items: center;
  margin-top: 1rem; /* Moved margin-top here */
}

.project-link:hover,
.project-link:focus {
  background-color: #ffa500;
  color: #1a1a1a;
  outline: none;
}


/* Contact Section Styles */
.contact {
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
  min-height: 44px;
}

.form-group textarea {
  height: 120px;
  resize: vertical;
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

/* Button and Link Styles */
.cta-button,
.submit-button,
.project-link,
.social-link {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.8rem 1.2rem;
  min-height: 44px;
  text-decoration: none;
  font-weight: 600;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.cta-button,
.submit-button {
  background-color: #ff8c00;
  color: #1a1a1a;
  border-radius: 25px;
  border: none;
  cursor: pointer;
}

.cta-button:hover,
.cta-button:focus {
  background-color: #ffa500;
  color: #1a1a1a;
  outline: none;
}

.project-link {
  color: #ff8c00;
  margin-top: 1rem;
}

.social-link {
  color: #ffa500;
  font-size: 1.5rem;
}

.social-link:hover {
  color: #ff8c00;
}

/* Footer Styles */
footer {
  text-align: center;
  margin-top: 4rem;
  color: #ffa500;
}

/* Responsive Styles */
@media (max-width: 1240px) {
  body {
    background-attachment: scroll;
  }
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

/* Ensure content doesn't overflow */
img, video, iframe {
  max-width: 100%;
  height: auto;
}
</style>
