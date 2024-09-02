<template>
  <header>
    <h1>Jesper Laurell</h1>
    <p class="tagline">
      RootPi Co-founder | M.Sc. Electrical Engineering, LTH | UX, Flutter & Game
      Design Enthusiast
    </p>
  </header>

  <main class="main-content">
    <section class="hero">
      <div class="hero-content">
        <h2>Hello, I'm Jesper</h2>
        <p>
          I love creating easy to use applications that solve real world
          problems.
        </p>
        <a href="#contact" class="cta-button" @click.prevent="scrollToContact"
          >Get in touch</a
        >
      </div>
      <img
        src="@/assets/profile-image.jpg"
        alt="Jesper Laurell"
        class="profile-image"
      />
    </section>

    <section class="skills">
      <h2>Skills</h2>
      <div class="skill-tags">
        <span v-for="skill in skills" :key="skill" class="skill-tag">{{
          skill
        }}</span>
      </div>
    </section>

    <section class="featured-projects">
      <h2>What I do</h2>
      <div class="project-grid">
        <div
          v-for="project in featuredProjects"
          :key="project.name"
          class="project-card"
        >
          <i :class="['project-icon', project.icon]"></i>
          <h4>{{ project.name }}</h4>
          <p>{{ project.description }}</p>
          <a :href="project.url" target="_blank" class="project-link"
            >View Project</a
          >
        </div>
      </div>
    </section>

    <section id="contact" class="contact">
      <h2>Get in Touch</h2>
      <div class="contact-info">
        <h3>Contact Information</h3>

        <p class="contact-item">
          <font-awesome-icon :icon="['fas', 'envelope']" class="contact-icon" />
          <span class="email">jesper.laurell1@gmail.com</span>
        </p>
        <p class="contact-item">
          <font-awesome-icon :icon="['fas', 'phone']" class="contact-icon" />
          <span>+46 708 88 95 36</span>
        </p>
        <p class="contact-item">
          <font-awesome-icon
            :icon="['fas', 'map-marker-alt']"
            class="contact-icon"
          />
          <span>Malmö, Sweden</span>
        </p>
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
    </section>
  </main>

  <footer>
    <p>
      &copy; {{ new Date().getFullYear() }} Jesper Laurell. All rights reserved.
    </p>
  </footer>
</template>

<script>
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { library } from "@fortawesome/fontawesome-svg-core";
import {
  faLinkedin,
  faGithub,
  faTwitter,
} from "@fortawesome/free-brands-svg-icons";
import {
  faRobot,
  faBrain,
  faListCheck,
  faArrowRight,
} from "@fortawesome/free-solid-svg-icons";
import "./App.css"; // Import the CSS file
import {
  faEnvelope,
  faPhone,
  faMapMarkerAlt,
} from "@fortawesome/free-solid-svg-icons";

library.add(
  faLinkedin,
  faGithub,
  faTwitter,
  faRobot,
  faBrain,
  faListCheck,
  faArrowRight,
  faEnvelope,
  faPhone,
  faMapMarkerAlt
);

export default {
  name: "App",
  components: {
    FontAwesomeIcon,
  },
  data() {
    return {
      skills: [
        "JavaScript",
        "Vue",
        "Python",
        "Laravel",
        "PHP",
        "OpenAI",
        "CSS",
        "HTML",
        "UI/UX Design",
        "GitHub",
        "Node.js",
        "Java",
        "IaaS",
        "Responsive Web Design",
        "Flutter",
        "Dart",
        "iOS/Android App Dev",
        "Scrum",
        "Machine Learning",
        "Reinforcement Learning",
        "React",
        "SQL",
        "Unity",
        "Game Dev",
        "C#",
        "Blender",
        "VR Development",
        "Meta Quest Dev",
      ],
      featuredProjects: [
        {
          name: "RootPi",
          description:
            "Co-founded RootPi, specializing in innovative AI solutions and software development.",
          icon: "fa-solid fa-robot",
          url: "https://rootpi.xyz/",
        },
        {
          name: "currencydebasement2",
          description:
            "An interactive visualization of global currency debasement.",
          icon: "fa-solid fa-brain",
          url: "https://github.com/snoboll/c",
        },
      ],
    };
  },
  methods: {
    scrollToContact() {
      const contactSection = document.getElementById("contact");
      contactSection.scrollIntoView({ behavior: "smooth" });
    },
  },
};
</script>
