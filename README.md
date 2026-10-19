<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Sathish Kumar | BIM & Revit API Developer</title>
<style>
  html, body { margin: 0; height: 100%; overflow: hidden; background: #0d1117; font-family: 'Segoe UI', system-ui, sans-serif; color: #fff; }
  canvas { display: block; }
  .overlay { position: fixed; left: 6vw; top: 50%; transform: translateY(-50%); max-width: 420px; pointer-events: none; }
  .overlay h1 { font-size: clamp(34px, 6vw, 64px); margin: 0 0 8px; letter-spacing: 1px; }
  .overlay h1 span { color: #ff4d4d; }
  .overlay p { margin: 0 0 22px; color: #c9d1d9; font-size: clamp(14px, 2vw, 18px); line-height: 1.5; }
  .links { pointer-events: auto; display: flex; gap: 12px; flex-wrap: wrap; }
  .links a { padding: 10px 18px; border: 1px solid #ff4d4d; color: #fff; text-decoration: none; border-radius: 8px; font-size: 14px; transition: background .2s; }
  .links a:hover { background: #ff4d4d; }
  .hint { position: fixed; bottom: 16px; width: 100%; text-align: center; font-size: 12px; color: #8b949e; }
  @media (max-width: 700px) { .overlay { top: 12%; transform: none; } }
</style>
</head>
<body>
<div class="overlay">
  <h1>Sathish <span>Kumar</span></h1>
  <p>BIM Technology Specialist | Revit API Developer<br>Building Revit add-ins with C# &amp; WPF</p>
  <div class="links">
    <a href="https://github.com/sathihs0023-cloud/BIMSheetPro">BIMSheetPro</a>
    <a href="https://github.com/sathihs0023-cloud/sketchup-mcp-setup">SketchUp MCP</a>
    <a href="https://github.com/sathihs0023-cloud">GitHub</a>
  </div>
</div>
<div class="hint">Drag to rotate · Scroll to zoom</div>

<script type="importmap">
{ "imports": {
  "three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js",
  "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/"
} }
</script>
<script type="module">
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x0d1117);
scene.fog = new THREE.Fog(0x0d1117, 18, 45);

const camera = new THREE.PerspectiveCamera(45, innerWidth / innerHeight, 0.1, 100);
camera.position.set(11, 8, 13);

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
renderer.setSize(innerWidth, innerHeight);
document.body.appendChild(renderer.domElement);

const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.autoRotate = true;
controls.autoRotateSpeed = 1.2;
controls.target.set(0, 4.5, 0);
controls.maxPolarAngle = Math.PI * 0.49;
controls.minDistance = 8;
controls.maxDistance = 30;

scene.add(new THREE.AmbientLight(0x6666aa, 0.8));
const red = new THREE.PointLight(0xff4d4d, 90, 40); red.position.set(6, 10, 6); scene.add(red);
const blue = new THREE.PointLight(0x5a4bff, 70, 40); blue.position.set(-8, 6, -6); scene.add(blue);

// ground grid + base
const grid = new THREE.GridHelper(40, 40, 0xff4d4d, 0x30363d);
grid.material.opacity = 0.35; grid.material.transparent = true;
scene.add(grid);

const tower = new THREE.Group();
const floors = 26, floorH = 0.38, gap = 0.28;
const plateMat = new THREE.MeshStandardMaterial({ color: 0x1b1f2a, metalness: 0.6, roughness: 0.35 });
const glassMat = new THREE.MeshStandardMaterial({ color: 0x4a5bff, transparent: true, opacity: 0.28, metalness: 0.9, roughness: 0.1, emissive: 0x1a1f66, emissiveIntensity: 0.6 });
const edgeMat = new THREE.LineBasicMaterial({ color: 0xff4d4d });

for (let i = 0; i < floors; i++) {
  const y = i * (floorH + gap);
  const scale = 1 - i * 0.012;
  const twist = i * 0.07;

  const plate = new THREE.Mesh(new THREE.BoxGeometry(4 * scale, floorH, 4 * scale), plateMat);
  plate.position.y = y; plate.rotation.y = twist;
  const edges = new THREE.LineSegments(new THREE.EdgesGeometry(plate.geometry), edgeMat);
  plate.add(edges);
  tower.add(plate);

  const glass = new THREE.Mesh(new THREE.BoxGeometry(3.7 * scale, gap, 3.7 * scale), glassMat);
  glass.position.y = y + (floorH + gap) / 2; glass.rotation.y = twist;
  tower.add(glass);
}
// core
const coreH = floors * (floorH + gap);
const core = new THREE.Mesh(new THREE.CylinderGeometry(0.7, 0.7, coreH, 24), new THREE.MeshStandardMaterial({ color: 0xff4d4d, emissive: 0xff2a2a, emissiveIntensity: 0.5, metalness: 0.5, roughness: 0.4 }));
core.position.y = coreH / 2 - 0.2;
tower.add(core);

// podium
const podium = new THREE.Mesh(new THREE.BoxGeometry(7, 0.5, 7), plateMat);
podium.position.y = -0.4;
podium.add(new THREE.LineSegments(new THREE.EdgesGeometry(podium.geometry), edgeMat));
tower.add(podium);
scene.add(tower);

// floating particles
const pGeo = new THREE.BufferGeometry();
const pos = new Float32Array(300 * 3);
for (let i = 0; i < pos.length; i++) pos[i] = (Math.random() - 0.5) * 36;
pGeo.setAttribute('position', new THREE.BufferAttribute(pos, 3));
const particles = new THREE.Points(pGeo, new THREE.PointsMaterial({ color: 0xff4d4d, size: 0.07, transparent: true, opacity: 0.7 }));
scene.add(particles);

addEventListener('resize', () => {
  camera.aspect = innerWidth / innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});

const clock = new THREE.Clock();
renderer.setAnimationLoop(() => {
  const t = clock.getElapsedTime();
  tower.position.y = Math.sin(t * 0.8) * 0.15;
  particles.rotation.y = t * 0.02;
  red.intensity = 80 + Math.sin(t * 2) * 15;
  controls.update();
  renderer.render(scene, camera);
});
</script>
</body>
</html>
