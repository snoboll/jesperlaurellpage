<template>
  <div class="cube-wrapper">
    <div
      ref="container"
      class="cube-container"
      role="img"
      aria-label="Interactive 3D Rubik's cube — drag to rotate, click to turn a face"
    ></div>
    <p class="drag-hint">drag to rotate · click to turn</p>
  </div>
</template>

<script>
import * as THREE from "three";
import { RoundedBoxGeometry } from "three/addons/geometries/RoundedBoxGeometry.js";

// Three.js state (outside Vue reactivity)
let scene, camera, renderer, cubeGroup, animationId;
let isDragging = false;
let dragMoved = false;
let isAnimating = false;
let lastX = 0, lastY = 0;
let downX = 0, downY = 0;
let targetRotX = -0.5, targetRotY = 0.7;
let currentRotX = -0.5, currentRotY = 0.7;

// Cube layout constants — referenced by layer rotation
const CUBIE_SIZE = 0.94;
const CUBIE_GAP = 0.04;
const OFFSET = CUBIE_SIZE + CUBIE_GAP;

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

      scene = new THREE.Scene();

      camera = new THREE.PerspectiveCamera(40, width / height, 0.1, 1000);
      camera.position.set(0, 0, 7);

      renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      renderer.setSize(width, height);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      renderer.toneMapping = THREE.ACESFilmicToneMapping;
      renderer.toneMappingExposure = 1.15;
      container.appendChild(renderer.domElement);

      // Soft ambient
      const ambient = new THREE.AmbientLight(0xffffff, 0.35);
      scene.add(ambient);

      // Key light — cool white from upper-right
      const keyLight = new THREE.DirectionalLight(0xffffff, 2.2);
      keyLight.position.set(5, 6, 5);
      scene.add(keyLight);

      // Fill — subtle cool from opposite side
      const fillLight = new THREE.DirectionalLight(0x9fb7d6, 0.5);
      fillLight.position.set(-5, -2, 3);
      scene.add(fillLight);

      // Warm rim from behind to catch the bevels
      const rimLight = new THREE.DirectionalLight(0xff8c00, 1.0);
      rimLight.position.set(-3, 2, -5);
      scene.add(rimLight);

      this.createCube();

      renderer.domElement.addEventListener("mousedown", this.onDown);
      renderer.domElement.addEventListener("mousemove", this.onMove);
      renderer.domElement.addEventListener("mouseup", this.onUp);
      renderer.domElement.addEventListener("mouseleave", this.onUp);
      renderer.domElement.addEventListener("touchstart", this.onTouchDown, { passive: true });
      renderer.domElement.addEventListener("touchmove", this.onTouchMove, { passive: true });
      renderer.domElement.addEventListener("touchend", this.onTouchUp);
      window.addEventListener("resize", this.onResize);

      this.animate();
    },

    createCube() {
      cubeGroup = new THREE.Group();

      const cornerRadius = 0.12;
      const cubieGeom = new RoundedBoxGeometry(CUBIE_SIZE, CUBIE_SIZE, CUBIE_SIZE, 5, cornerRadius);
      const cubieMat = new THREE.MeshPhysicalMaterial({
        color: 0x0a0a0a,
        metalness: 0.25,
        roughness: 0.55,
        clearcoat: 0.4,
        clearcoatRoughness: 0.3,
      });

      const stickerSize = CUBIE_SIZE * 0.78;
      const stickerRadius = 0.06;
      const stickerGeom = new RoundedBoxGeometry(
        stickerSize,
        stickerSize,
        0.04,
        4,
        stickerRadius
      );
      const stickerMat = new THREE.MeshStandardMaterial({
        color: 0xff8c00,
        metalness: 0.15,
        roughness: 0.35,
        emissive: 0xff6600,
        emissiveIntensity: 0.18,
      });

      const faces = [
        { axis: 0, dir:  1, rot: [0,  Math.PI / 2, 0] },
        { axis: 0, dir: -1, rot: [0, -Math.PI / 2, 0] },
        { axis: 1, dir:  1, rot: [-Math.PI / 2, 0, 0] },
        { axis: 1, dir: -1, rot: [ Math.PI / 2, 0, 0] },
        { axis: 2, dir:  1, rot: [0, 0, 0] },
        { axis: 2, dir: -1, rot: [0, Math.PI, 0] },
      ];
      const faceOffset = CUBIE_SIZE / 2 + 0.005;
      const axisNames = ["x", "y", "z"];

      // Each cubie gets its own container so layer rotation
      // can move the cubie + its stickers as a unit.
      for (let x = -1; x <= 1; x++) {
        for (let y = -1; y <= 1; y++) {
          for (let z = -1; z <= 1; z++) {
            const cubieGroup = new THREE.Group();
            cubieGroup.position.set(x * OFFSET, y * OFFSET, z * OFFSET);

            const cubie = new THREE.Mesh(cubieGeom, cubieMat);
            cubieGroup.add(cubie);

            const coord = [x, y, z];
            for (const f of faces) {
              if (coord[f.axis] !== f.dir) continue;
              const tile = new THREE.Mesh(stickerGeom, stickerMat);
              tile.position.set(0, 0, 0);
              tile.position[axisNames[f.axis]] = f.dir * faceOffset;
              tile.rotation.set(...f.rot);
              cubieGroup.add(tile);
            }

            cubeGroup.add(cubieGroup);
          }
        }
      }

      scene.add(cubeGroup);
    },

    animate() {
      animationId = requestAnimationFrame(() => this.animate());

      currentRotX += (targetRotX - currentRotX) * 0.06;
      currentRotY += (targetRotY - currentRotY) * 0.06;

      if (!isDragging && !isAnimating) {
        targetRotY += 0.003;
      }

      if (cubeGroup) {
        cubeGroup.rotation.x = currentRotX;
        cubeGroup.rotation.y = currentRotY;
      }

      renderer.render(scene, camera);
    },

    // --- input handlers ---

    onDown(e) {
      isDragging = true;
      dragMoved = false;
      lastX = e.clientX;
      lastY = e.clientY;
      downX = e.clientX;
      downY = e.clientY;
    },

    onMove(e) {
      if (!isDragging) return;
      const dx = e.clientX - lastX;
      const dy = e.clientY - lastY;
      if (Math.abs(e.clientX - downX) + Math.abs(e.clientY - downY) > 5) {
        dragMoved = true;
      }
      targetRotY += dx * 0.008;
      targetRotX += dy * 0.008;
      targetRotX = Math.max(-1.2, Math.min(1.2, targetRotX));
      lastX = e.clientX;
      lastY = e.clientY;
    },

    onUp(e) {
      if (isDragging && !dragMoved && e && e.clientX !== undefined) {
        this.handleClick(e.clientX, e.clientY);
      }
      isDragging = false;
    },

    onTouchDown(e) {
      if (e.touches.length === 1) {
        isDragging = true;
        dragMoved = false;
        lastX = e.touches[0].clientX;
        lastY = e.touches[0].clientY;
        downX = lastX;
        downY = lastY;
      }
    },

    onTouchMove(e) {
      if (!isDragging || e.touches.length !== 1) return;
      const cx = e.touches[0].clientX;
      const cy = e.touches[0].clientY;
      if (Math.abs(cx - downX) + Math.abs(cy - downY) > 6) {
        dragMoved = true;
      }
      targetRotY += (cx - lastX) * 0.008;
      targetRotX += (cy - lastY) * 0.008;
      targetRotX = Math.max(-1.2, Math.min(1.2, targetRotX));
      lastX = cx;
      lastY = cy;
    },

    onTouchUp(e) {
      if (isDragging && !dragMoved) {
        const touch = (e.changedTouches && e.changedTouches[0]) || null;
        if (touch) this.handleClick(touch.clientX, touch.clientY);
      }
      isDragging = false;
    },

    // --- click → layer rotation ---

    handleClick(clientX, clientY) {
      if (isAnimating || !cubeGroup) return;

      const rect = renderer.domElement.getBoundingClientRect();
      const mouse = new THREE.Vector2(
        ((clientX - rect.left) / rect.width) * 2 - 1,
        -((clientY - rect.top) / rect.height) * 2 + 1
      );
      const raycaster = new THREE.Raycaster();
      raycaster.setFromCamera(mouse, camera);
      const hits = raycaster.intersectObjects(cubeGroup.children, true);
      if (!hits.length) return;

      // Convert hit point into cubeGroup local space → the largest
      // absolute coordinate identifies which outer face was clicked.
      const localPoint = cubeGroup.worldToLocal(hits[0].point.clone());
      const absX = Math.abs(localPoint.x);
      const absY = Math.abs(localPoint.y);
      const absZ = Math.abs(localPoint.z);
      let axis, sign;
      if (absX >= absY && absX >= absZ) {
        axis = "x";
        sign = Math.sign(localPoint.x) || 1;
      } else if (absY >= absZ) {
        axis = "y";
        sign = Math.sign(localPoint.y) || 1;
      } else {
        axis = "z";
        sign = Math.sign(localPoint.z) || 1;
      }

      this.rotateLayer(axis, sign);
    },

    rotateLayer(axis, sign) {
      isAnimating = true;

      const targetCoord = sign * OFFSET;
      const tolerance = 0.15;

      // Build a temporary pivot at cube center and re-parent layer members.
      const pivot = new THREE.Group();
      cubeGroup.add(pivot);

      const members = cubeGroup.children.filter(
        (obj) => obj !== pivot && Math.abs(obj.position[axis] - targetCoord) < tolerance
      );
      members.forEach((m) => pivot.attach(m));

      const rotAxis = new THREE.Vector3(
        axis === "x" ? 1 : 0,
        axis === "y" ? 1 : 0,
        axis === "z" ? 1 : 0
      );
      const targetAngle = (Math.PI / 2) * sign;
      const duration = 420;
      const start = performance.now();

      const step = () => {
        const t = Math.min(1, (performance.now() - start) / duration);
        const eased = 1 - Math.pow(1 - t, 3);
        pivot.setRotationFromAxisAngle(rotAxis, eased * targetAngle);
        if (t < 1) {
          requestAnimationFrame(step);
        } else {
          // Bake: return each member to cubeGroup, preserving world transform,
          // then snap positions to the exact slot grid to avoid float drift.
          [...pivot.children].forEach((m) => {
            cubeGroup.attach(m);
            m.position.x = Math.round(m.position.x / OFFSET) * OFFSET;
            m.position.y = Math.round(m.position.y / OFFSET) * OFFSET;
            m.position.z = Math.round(m.position.z / OFFSET) * OFFSET;
          });
          cubeGroup.remove(pivot);
          isAnimating = false;
        }
      };
      requestAnimationFrame(step);
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
              obj.material.forEach((m) => m.dispose());
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
  touch-action: none;
}

.cube-container:active {
  cursor: grabbing;
}

.drag-hint {
  margin-top: 0.75rem;
  font-size: 0.68rem;
  color: rgba(240, 236, 228, 0.35);
  font-family: "IBM Plex Mono", monospace;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

@media (max-width: 768px) {
  .cube-container {
    width: 240px;
    height: 240px;
  }
}
</style>
