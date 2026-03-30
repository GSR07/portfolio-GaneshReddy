# Portfolio Website — Project Context

> Use this document to give an AI assistant full context about this codebase.

---

## Overview

**Owner:** Ganesh Reddy (`GSR07`)  
**Repo:** `https://github.com/GSR07/portfolio-Ganesh`  
**Live URL:** `https://GSR07.github.io/portfolio-Ganesh/`  
**Type:** Single-page static portfolio website (HTML + CSS + JS — no framework, no build step)  
**Purpose:** Personal portfolio for a Robotics & AI Master's student actively seeking Master's Thesis and working student positions.

---

## File Structure

```
portfolio/
├── index.html                        # Main single-page app (745 lines)
├── style.css                         # All styling (2514 lines, versioned with ?v=N)
├── script.js                         # Typed.js effect, nav scroll, CV download (168 lines)
├── blog.html                         # Blog listing page
├── franka-pick-and-place-blog.html   # Detailed blog post for Franka project
├── ganesh_photo.jpg                  # Profile photo used in hero section
├── hero_bg.png                       # Hero section background image
├── franka_video.mp4                  # Demo video for Franka Panda project (2.4MB)
├── franka_video_trimmed.webm         # Trimmed webm version (39MB)
└── README.md                         # GitHub repo README
```

> **Note:** `cv/` (personal PDFs), `directives/`, `execution/`, `.claude/`, `.venv/` are git-ignored and NOT part of the deployed site.

---

## Tech Stack

- **HTML5** — semantic, single `index.html`, no templating
- **CSS3** — custom properties (`:root` vars), `@keyframes`, `clip-path`, `transform-style: preserve-3d`, `perspective`, `filter`, glassmorphism (backdrop-filter)
- **Vanilla JS** — `script.js` handles: typed text effect (Typed.js CDN), sticky nav highlight, CV download button
- **Fonts (Google Fonts CDN):** `Outfit` (body), `Syne` (headings)
- **No frameworks, no bundler, no npm** — pure static files, deploy directly

### CSS Architecture
- All CSS lives in `style.css` — one file, ~2500 lines
- CSS custom properties defined in `:root`:
  ```
  --bg, --bg-alt, --surface, --surface-border
  --accent (#0284c7), --accent-2 (#4f46e5), --accent-3 (#db2777)
  --text, --text-muted, --text-dim
  --radius (16px), --radius-sm (8px)
  --font-main (Outfit), --font-head (Syne)
  --shadow, --glow, --nav-h (68px), --transition
  ```
- **Cache-busting:** `<link rel="stylesheet" href="style.css?v=16">` — increment `v=N` after every CSS change

---

## Page Sections (in order)

| # | ID | Title | Notes |
|---|-----|-------|-------|
| — | `#navbar` | Navigation | Sticky, blur backdrop, hamburger on mobile |
| 0 | `#hero` | Hero | Drone swarm, typed tagline, profile photo, CTA buttons |
| 1 | `#about` | Who I Am | 3D robot arm animation, about text, stats, research focus card |
| 2 | `#skills` | Technical Stack | TurtleBot scanner, 6 skill groups |
| 3 | `#education` | Academic Background | Timeline, 2 entries |
| 4 | `#experience` | Work & Research | Soldering PCB animation, 3 experience cards |
| 5 | `#projects` | Featured Work | 1 featured + 6 grid cards |
| 6 | `#contact` | Let's Connect | Links, CV download CTA |

---

## Animated Decorative Robots (`.section-robot`)

All robots use `position: absolute`, `z-index: 0`, `aria-hidden="true"` — they sit behind content and are purely decorative.

### 1. Hero — Drone Swarm (`.drone-swarm`)
```html
<div class="section-robot drone-swarm">
  <div class="swarm-drone d1"><!-- 4 rotors --></div>
  <div class="swarm-drone d2"><!-- 4 rotors --></div>
  <div class="swarm-drone d3"><!-- 4 rotors --></div>
</div>
```
- 3 drones fly across the full page width using `vw`/`vh` in keyframes
- Each drone has a unique 7–8.5s animation (`drone-d1`, `drone-d2`, `drone-d3`)
- Spread starting positions: `left: 4%`, `left: 38%`, `right: 6%`
- Keyframes use `translate3d(0→82vw, ±14vh, ±70px)` for full-viewport coverage

### 2. About — 3D Robot Arm (`.about-arm-sim`)
```html
<div class="section-robot about-arm-sim">
  <div class="omni-arm pnp-mode">
    <div class="omni-base"></div>
    <div class="omni-shoulder"></div>
    <div class="omni-elbow"></div>
    <div class="omni-forearm"></div>
    <div class="omni-wrist"></div>
    <div class="omni-finger f1"></div>
    <div class="omni-finger f2"></div>
  </div>
</div>
```
- 3D effect via metallic radial/linear gradients on each segment
- Joint spheres via `::before`/`::after` pseudo-elements with glow:
  - Shoulder: cyan (`#7dd3fc`) sphere with `box-shadow: 0 0 14px #0ea5e9`
  - Elbow: purple (`#a78bfa`) sphere
  - Wrist: pink (`#f472b6`) sphere
- `omni-wrist` has a `wrist-pulse` animation (scale 1→1.1, 3s infinite)

### 3. Skills — TurtleBot Scanner (`.skills-scanner`)
```html
<div class="section-robot skills-scanner">
  <div class="s-turtlebot scan-mode">
    <div class="tb-layer tb-base"></div>
    <div class="tb-layer tb-mid"></div>
    <div class="tb-layer tb-top"></div>
    <div class="tb-lidar">
      <div class="lidar-ray"></div>
    </div>
    <div class="v-scan-cone"></div>
    <div class="v-scan-cone v-scan-inner"></div>
  </div>
</div>
```
- Three-layer disc body with distinct gradients and rim lighting
- Lidar head (`.tb-lidar`) spins 360° every 1.6s
- `.lidar-ray` is a child of lidar — inherits rotation, sweeps with it
- V-scan cone: `clip-path: polygon(0% 50%, 100% 0%, 100% 100%)` — triangle pointing robot-forward
- `.v-scan-inner` is a smaller overlapping cone (offset pulse)
- Navigation: **38s serpentine path** covering full section width/height
  ```
  Keyframe turtle-navigate: 8 waypoints using translate3d(82vw, -65vh, 0) etc.
  rotateX(60deg) for isometric top-down look
  rotateZ accumulated by -90° per turn × 8 turns = -720° total
  Final: rotateZ(-675°) ≡ 45° mod 360° → seamless loop
  ```
- `.s-turtlebot` positioned at `bottom: 10%; left: 5%`

### 4. Experience — Soldering PCB (`.experience-solder`)
```html
<div class="section-robot experience-solder">
  <div class="solder-arm experience-arm"><!-- iron, tip, handle --></div>
  <div class="pcb-board exp-pcb">
    <div class="pcb-ic ic1"><span>ESP32</span></div>
    <div class="pcb-ic ic2"><span>MCU</span></div>
    <div class="pcb-cap cap1/2/3"></div>
    <div class="pcb-res res1/2/3/4"></div>
    <div class="pcb-led led-active"></div>
    <div class="pcb-trace tr1/2/3"></div>
    <div class="pcb-solder-pt"></div>
  </div>
  <div class="spark-shower">
    <div class="smoke-puff sp1/sp2/sp3"></div>
    <div class="spark p1…p10"></div>
  </div>
</div>
```
- Positioned in right gutter: `right: 0.5–2%` (outside card grid, avoids z-index issues)
- PCB has IC chips, capacitors, resistors, LED, traces — all CSS-drawn
- Spark shower: 10 sparks with staggered `animation-delay`, fly outward
- Smoke puffs: 3 puffs floating upward with fade

---

## Projects

### Featured (full-width card)
**Franka Panda — Vision-Guided Pick & Place Simulation**
- ROS2 Humble + MoveIt2 + Ignition Gazebo Fortress
- `ColorDetector` node: HSV segmentation + TF2 camera-to-base transform
- `GraspManager` node: fixed joint attach/detach via Ignition service CLI
- Tags: ROS2 Humble, MoveIt2, Ignition Gazebo, OpenCV, TF2, ros2_control, Python, C++
- Visual: embedded `franka_video.mp4` (autoplay loop muted)

### Grid Cards (6)
| ID | Title | Badges | Tech Tags |
|----|-------|--------|-----------|
| `#proj-amr` | Autonomous Mobile Robot (AMR) | Robotics, Autonomy | ROS2, Nav2, EKF, Gazebo, SolidWorks |
| `#proj-sort` | Automated Grading & Sorting System | Computer Vision, Thesis | OpenCV, Python, Automation |
| `#proj-driver` | Driver Drowsiness Detection | Safety AI, Vision | YOLO, OpenCV, Real-time |
| `#proj-xray` | Lung Disease Detection from Chest X-rays | Medical AI, Deep Learning | PyTorch, CNNs, Transfer Learning |
| `#proj-dl-classify` | Medical Image Classification Pipeline | Medical AI, ML Pipeline | PyTorch, Data Curation, Bias Analysis |
| `#proj-iot` | BLE Indoor Localisation System | IoT, Embedded | ESP32, BLE, KiCad, ESP-IDF |

### Project Card Visual Classes
- `.pv-franka` — custom video embed
- `.pv-robotics` — blue/cyan gradient
- `.pv-cv` — orange/amber gradient
- `.pv-driver` — dark/red gradient
- `.pv-medical` — teal/green gradient
- `.pv-dl` — purple/violet gradient
- `.pv-iot` — blue gradient (`background: linear-gradient(135deg, rgba(37,99,235,0.18), rgba(14,165,233,0.14)) !important`)

> **Bug history:** `.pv-iot` had a duplicate rule later in style.css with `background: #0b1120 !important` and `display: none` on `.pv-icon` — this caused the BLE card to show a dark blank. Fixed by updating the later rule to the gradient.

---

## Experience Cards

| Badge | Title | Org | Period |
|-------|-------|-----|--------|
| Research | Student Research Assistant | IDEA LABS · FAU Erlangen | Nov 2025 – Present |
| Industry | Internet of Things Engineer | Digital Associates India · Hyderabad | Jan 2023 – Oct 2023 |
| Internship | Systems Engineer Intern | Centre for Autonomous Systems Engineering · Hubballi | Jan 2022 – Jul 2022 |

---

## Education

| Degree | University | Status |
|--------|-----------|--------|
| M.Sc. Autonomy Technologies | Friedrich-Alexander-Universität (FAU) Erlangen-Nürnberg | Ongoing — 67.5 ECTS |
| B.Tech Automation & Robotics | — | Completed |

Bachelor's Thesis: *"Automated Grading and Sorting System using Computer Vision"*

---

## Skills Grid (6 groups)

| Icon | Group | Key Tags |
|------|-------|----------|
| 🤖 | Robotics & Manipulation | ROS2 (hot), MoveIt2 (hot), Franka Panda (hot) |
| 👁 | Computer Vision | OpenCV (hot), YOLO (hot), HSV Colour Detection |
| 💻 | Programming | Python (hot), C++, MATLAB |
| 🏥 | Medical Imaging & AI | Chest X-ray Analysis (hot), PyTorch, TensorFlow |
| 📟 | Embedded & IoT | ESP32, Arduino, Raspberry Pi |
| 🛠 | Tools & DevOps | Linux, Git, Docker |

---

## About Section Stats

- **67.5** ECTS Earned
- **2+** Years Experience
- **3** Research areas (Robotics, CV, Embedded)

---

## Contact Info

- **Email:** ganeshreddy30102000@gmail.com
- **Phone:** +49 155 10182395
- **LinkedIn:** linkedin.com/in/ganeshssreddy09
- **GitHub:** github.com/gsr07

---

## CSS Conventions & Gotchas

1. **Cache-busting:** Always increment `?v=N` in `<link href="style.css?v=N">` after CSS changes
2. **z-index trap:** `.section-robot` uses `z-index: 0` — decorative robots must be in page gutters (right edge / far left) to be visible. Positioning behind content cards makes them invisible.
3. **Duplicate rules:** Check for duplicate class rules deep in the file — a later rule with `!important` can silently override earlier fixes.
4. **Rotation accumulation:** For smooth CSS animation loops involving multiple rotations, accumulate rotation values (e.g., -90° per step × 8 steps = -720°) rather than resetting to 0° mid-animation.
5. **`vw`/`vh` in transforms:** Used throughout drone and TurtleBot animations to make movement span the full viewport regardless of screen size.
6. **Glassmorphism:** Cards use `.glass-card` class: `background: var(--surface)`, `backdrop-filter: blur(...)`, `border: 1px solid var(--surface-border)`.

---

## Known Architecture Decisions

- **No JavaScript frameworks** — intentional, keeps deploy simple (just push files to GitHub Pages)
- **No CSS preprocessor** — plain CSS with custom properties
- **No build step** — `index.html` references `style.css?v=16` and `script.js` directly
- **Section robots are purely decorative** — all have `aria-hidden="true"`, no interactivity
- **Blog is separate pages** (`blog.html`, `franka-pick-and-place-blog.html`), not embedded in `index.html`

---

## GitHub Pages Deployment

- Branch: `main`, root `/`
- Deployed URL: `https://GSR07.github.io/portfolio-Ganesh/`
- To deploy a change: `git add <files> && git commit -m "message" && git push origin main`
- Pages updates automatically within ~1–2 minutes of push

