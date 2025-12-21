<template>
  <div class="cube-wrapper">
    <div ref="container" class="cube-container"></div>
    <p class="drag-hint">drag to rotate</p>
  </div>
</template>

<script>
import * as THREE from "three";
import { RoundedBoxGeometry } from "three/addons/geometries/RoundedBoxGeometry.js";

// Store Three.js objects completely outside Vue
let scene, camera, renderer, cubeGroup, animationId;
let isDragging = false;
let lastX = 0, lastY = 0;
let targetRotX = -0.5, targetRotY = 0.7;
let currentRotX = -0.5, currentRotY = 0.7;

export default {
  name: "RubiksCube",
  mounted() {
    this.init();
  },
  beforeUnmount() {
    this.dispose();
  },
  methods: {
    init() {
      const container = this.$refs.container;
      const width = container.clientWidth;
      const height = container.clientHeight;

      // Scene
      scene = new THREE.Scene();

      // Camera
      camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000);
      camera.position.z = 6;

      // Renderer
      renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      renderer.setSize(width, height);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      renderer.toneMapping = THREE.ACESFilmicToneMapping;
      renderer.toneMappingExposure = 1.5;
      container.appendChild(renderer.domElement);

      // Subtle ambient for dark mood
      const ambient = new THREE.AmbientLight(0x1a1a1a, 0.4);
      scene.add(ambient);

      // Key light - cooler to contrast with orange
      const keyLight = new THREE.DirectionalLight(0xcccccc, 0.6);
      keyLight.position.set(4, 4, 4);
      scene.add(keyLight);

      // Fill light (subtle)
      const fillLight = new THREE.DirectionalLight(0x888888, 0.3);
      fillLight.position.set(-4, 2, -2);
      scene.add(fillLight);

      // Strong orange accent light from below - gives edge glow
      const accentLight = new THREE.PointLight(0xff5500, 2.0, 12);
      accentLight.position.set(0, -4, 2);
      scene.add(accentLight);

      // Orange rim light for dramatic edge highlights
      const rimLight = new THREE.PointLight(0xff4400, 1.5, 10);
      rimLight.position.set(-3, 3, -3);
      scene.add(rimLight);

      // Front orange accent
      const frontLight = new THREE.PointLight(0xff6600, 1.2, 8);
      frontLight.position.set(2, -1, 5);
      scene.add(frontLight);
      
      // Top orange hint
      const topLight = new THREE.PointLight(0xff5500, 0.8, 10);
      topLight.position.set(0, 5, 0);
      scene.add(topLight);

      // Create cube
      this.createCube();

      // Events
      renderer.domElement.addEventListener("mousedown", this.onDown);
      renderer.domElement.addEventListener("mousemove", this.onMove);
      renderer.domElement.addEventListener("mouseup", this.onUp);
      renderer.domElement.addEventListener("mouseleave", this.onUp);
      renderer.domElement.addEventListener("touchstart", this.onTouchDown, { passive: true });
      renderer.domElement.addEventListener("touchmove", this.onTouchMove, { passive: true });
      renderer.domElement.addEventListener("touchend", this.onUp);
      window.addEventListener("resize", this.onResize);

      // Start animation
      this.animate();
    },

    createCube() {
      cubeGroup = new THREE.Group();
      
      const size = 0.8;
      const gap = 0.12;
      const offset = size + gap;
      const cornerRadius = 0.15;

      // Proper rounded box geometry
      const geometry = new RoundedBoxGeometry(size, size, size, 6, cornerRadius);

      // Dark solid material with subtle sheen
      const darkMat = new THREE.MeshPhysicalMaterial({
        color: 0x0a0a0a,
        metalness: 0.7,
        roughness: 0.25,
        transmission: 0,
        transparent: false,
        opacity: 1.0,
        reflectivity: 0.4,
        clearcoat: 0.8,
        clearcoatRoughness: 0.15,
        envMapIntensity: 0.5,
        emissive: 0x1a0800,
        emissiveIntensity: 0.08,
      });

      // Bright orange edge material
      const edgeMat = new THREE.LineBasicMaterial({
        color: 0xff6a00,
        transparent: true,
        opacity: 1.0,
        linewidth: 2,
      });

      // Create 3x3x3 cubes
      for (let x = -1; x <= 1; x++) {
        for (let y = -1; y <= 1; y++) {
          for (let z = -1; z <= 1; z++) {
            // Dark cube
            const cube = new THREE.Mesh(geometry, darkMat.clone());
            cube.position.set(x * offset, y * offset, z * offset);
            cubeGroup.add(cube);

            // Bright orange edge lines
            const edges = new THREE.EdgesGeometry(geometry, 12);
            const line = new THREE.LineSegments(edges, edgeMat.clone());
            line.position.copy(cube.position);
            cubeGroup.add(line);
            
            // Add subtle orange inner glow on corners
            const glowMat = new THREE.MeshBasicMaterial({
              color: 0xff6a00,
              transparent: true,
              opacity: 0.06,
            });
            const glowCube = new THREE.Mesh(geometry, glowMat);
            glowCube.position.copy(cube.position);
            glowCube.scale.setScalar(1.02);
            cubeGroup.add(glowCube);
          }
        }
      }

      // Outer glow box - brighter orange accent
      const outerGeom = new RoundedBoxGeometry(
        size * 3 + gap * 2.2,
        size * 3 + gap * 2.2,
        size * 3 + gap * 2.2,
        4,
        cornerRadius * 2
      );
      const outerEdges = new THREE.EdgesGeometry(outerGeom, 15);
      const outerMat = new THREE.LineBasicMaterial({
        color: 0xff6a00,
        transparent: true,
        opacity: 0.5,
      });
      const outerGlow = new THREE.LineSegments(outerEdges, outerMat);
      cubeGroup.add(outerGlow);

      scene.add(cubeGroup);
    },

    animate() {
      animationId = requestAnimationFrame(() => this.animate());

      // Smooth rotation
      currentRotX += (targetRotX - currentRotX) * 0.06;
      currentRotY += (targetRotY - currentRotY) * 0.06;

      // Auto-rotate when not dragging
      if (!isDragging) {
        targetRotY += 0.003;
      }

      if (cubeGroup) {
        cubeGroup.rotation.x = currentRotX;
        cubeGroup.rotation.y = currentRotY;
      }

      renderer.render(scene, camera);
    },

    onDown(e) {
      isDragging = true;
      lastX = e.clientX;
      lastY = e.clientY;
    },

    onMove(e) {
      if (!isDragging) return;
      targetRotY += (e.clientX - lastX) * 0.008;
      targetRotX += (e.clientY - lastY) * 0.008;
      targetRotX = Math.max(-1.2, Math.min(1.2, targetRotX));
      lastX = e.clientX;
      lastY = e.clientY;
    },

    onUp() {
      isDragging = false;
    },

    onTouchDown(e) {
      if (e.touches.length === 1) {
        isDragging = true;
        lastX = e.touches[0].clientX;
        lastY = e.touches[0].clientY;
      }
    },

    onTouchMove(e) {
      if (!isDragging || e.touches.length !== 1) return;
      targetRotY += (e.touches[0].clientX - lastX) * 0.008;
      targetRotX += (e.touches[0].clientY - lastY) * 0.008;
      targetRotX = Math.max(-1.2, Math.min(1.2, targetRotX));
      lastX = e.touches[0].clientX;
      lastY = e.touches[0].clientY;
    },

    onResize() {
      const container = this.$refs.container;
      if (!container) return;
      const w = container.clientWidth;
      const h = container.clientHeight;
      camera.aspect = w / h;
      camera.updateProjectionMatrix();
      renderer.setSize(w, h);
    },

    dispose() {
      cancelAnimationFrame(animationId);
      window.removeEventListener("resize", this.onResize);
      
      if (renderer) {
        renderer.dispose();
        renderer.domElement.remove();
      }
      
      if (scene) {
        scene.traverse((obj) => {
          if (obj.geometry) obj.geometry.dispose();
          if (obj.material) {
            if (Array.isArray(obj.material)) {
              obj.material.forEach(m => m.dispose());
            } else {
              obj.material.dispose();
            }
          }
        });
      }
      
      scene = camera = renderer = cubeGroup = null;
      animationId = null;
    },
  },
};
</script>

<style scoped>
.cube-wrapper {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1.5rem 0;
}

.cube-container {
  width: 300px;
  height: 300px;
  cursor: grab;
}

.cube-container:active {
  cursor: grabbing;
}

.drag-hint {
  margin-top: 0.5rem;
  font-size: 0.7rem;
  color: rgba(255, 140, 0, 0.35);
  font-family: "JetBrains Mono", monospace;
  letter-spacing: 0.1em;
  text-transform: lowercase;
}

@media (max-width: 768px) {
  .cube-container {
    width: 240px;
    height: 240px;
  }
}
</style>
