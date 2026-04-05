<template>
  <header>
    <p class="role">Engineer · Builder</p>
    <h1>Jesper Laurell</h1>
    <p class="tagline">
      RootPi Co-founder · M.Sc. Electrical Engineering, LTH · UX, Flutter &amp;
      Game Design
    </p>
  </header>

  <main class="main-content">
    <section class="hero">
      <div class="hero-content">
        <h2>Hello, I'm <em>Jesper</em>.</h2>
        <p>
          I build easy-to-use applications that solve real-world problems. I
          also care about lifting heavy, self improvement, and hard money.
        </p>
        <a href="#contact" class="cta-button" @click.prevent="scrollToContact"
          >Get in touch</a
        >
      </div>
      <img
        src="@/assets/profile-image.jpg"
        alt="Jesper Laurell"
        class="profile-image"
        width="280"
        height="280"
      />
    </section>

    <RubiksCube />

    <section class="featured-projects">
      <div class="section-head">
        <p class="eyebrow">01 / Work</p>
        <h2>What I <em>do</em></h2>
      </div>
      <div class="project-grid">
        <a
          v-for="project in featuredProjects"
          :key="project.name"
          :href="project.url"
          target="_blank"
          rel="noopener"
          class="project-card"
        >
          <i :class="['project-icon', project.icon]"></i>
          <h4>{{ project.name }}</h4>
          <p>{{ project.description }}</p>
          <span class="project-link">View project</span>
        </a>
      </div>
    </section>

    <TalkToMeAbout />

    <section id="contact" class="contact">
      <div class="section-head">
        <p class="eyebrow">03 / Contact</p>
        <h2>Get in <em>touch</em></h2>
      </div>
      <div class="contact-info">
        <p class="contact-item">
          <font-awesome-icon :icon="['fas', 'envelope']" class="contact-icon" />
          <a href="mailto:jesper.laurell1@gmail.com" class="email"
            >jesper.laurell1@gmail.com</a
          >
        </p>
        <p class="contact-item">
          <font-awesome-icon :icon="['fas', 'phone']" class="contact-icon" />
          <a href="tel:+46708889536">+46 708 88 95 36</a>
        </p>
        <p class="contact-item">
          <font-awesome-icon
            :icon="['fas', 'map-marker-alt']"
            class="contact-icon"
          />
          <span>Malmö, Sweden</span>
        </p>
        <p class="contact-item">
          <font-awesome-icon
            :icon="['fas', 'graduation-cap']"
            class="contact-icon"
          />
          <span>LTH, Lund · Austrian School of Economics</span>
        </p>
        <div class="social-links">
          <a
            href="https://www.linkedin.com/in/jesper-laurell-2b0213153/?originalSubdomain=se"
            target="_blank"
            class="social-link"
            aria-label="LinkedIn"
          >
            <font-awesome-icon :icon="['fab', 'linkedin']" />
          </a>
          <a
            href="https://github.com/snoboll"
            target="_blank"
            class="social-link"
            aria-label="GitHub"
          >
            <font-awesome-icon :icon="['fab', 'github']" />
          </a>
        </div>
      </div>
    </section>
  </main>

  <footer>
    <p>&copy; {{ new Date().getFullYear() }} — Jesper Laurell</p>
  </footer>
</template>

<script>
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { library } from "@fortawesome/fontawesome-svg-core";
import {
  faLinkedin,
  faGithub,
  faBitcoin,
} from "@fortawesome/free-brands-svg-icons";
import {
  faRobot,
  faDumbbell,
  faEnvelope,
  faPhone,
  faMapMarkerAlt,
  faGraduationCap,
  faGolfBallTee,
} from "@fortawesome/free-solid-svg-icons";
import "./App.css";

library.add(
  faLinkedin,
  faGithub,
  faBitcoin,
  faRobot,
  faDumbbell,
  faEnvelope,
  faPhone,
  faMapMarkerAlt,
  faGraduationCap,
  faGolfBallTee
);

import TalkToMeAbout from "./components/TalkToMeAbout.vue";
import RubiksCube from "./components/RubiksCube.vue";
export default {
  name: "App",
  components: {
    FontAwesomeIcon,
    TalkToMeAbout,
    RubiksCube,
  },
  data() {
    return {
      featuredProjects: [
        {
          name: "RootPi",
          description:
            "Co-founded RootPi, specializing in innovative AI solutions and software development.",
          icon: "fa-solid fa-robot",
          url: "https://rootpi.xyz/",
        },
        {
          name: "Pokemon Golf",
          description:
            "A playful mashup of Pokémon and golf — a side project exploring game design and web tech.",
          icon: "fa-solid fa-golf-ball-tee",
          url: "https://github.com/snoboll/pokemongolf",
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
