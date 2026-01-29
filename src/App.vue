<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const selectedMaterial = ref(null);
const isRun = ref(true);
const Brushselected = ref(1);

const inkColor = ref("#ff3b7a");
const canvasRef = ref(null);

function selectMaterial() {
  let selectedNow;
  for (let mat of materials) if (mat.id === selectedMaterial.value) selectedNow = mat;
  for (let mat of Liquids) if (mat.id === selectedMaterial.value) selectedNow = mat;
  for (let mat of solid) if (mat.id === selectedMaterial.value) selectedNow = mat;
  for (let mat of Forces_AND_Interactions) if (mat.id === selectedMaterial.value) selectedNow = mat;
  for (let mat of Ink) if (mat.id === selectedMaterial.value) selectedNow = mat;

  if (!selectedNow) return { name: "No material selected", color: "#6a7282" };
  if (selectedNow.id === "ink") return { name: "Ink", color: inkColor.value };
  return { name: selectedNow.name, color: selectedNow.color };
}

const materials = [
  {
    id: "sand",
    name: "Sand",
    color: "#f4d03f",
    icon: `<path fill="currentColor" d="M12 15q1.25 0 2.125-.875T15 12t-.875-2.125T12 9t-2.125.875T9 12t.875 2.125T12 15m0 7q-2.075 0-3.9-.788t-3.175-2.137T2.788 15.9T2 12t.788-3.9t2.137-3.175T8.1 2.788T12 2t3.9.788t3.175 2.137T21.213 8.1T22 12t-.788 3.9t-2.137 3.175t-3.175 2.138T12 22m0-2q3.35 0 5.675-2.325T20 12t-2.325-5.675T12 4T6.325 6.325T4 12t2.325 5.675T12 20m0-8"/>`,
  },
  {
    id: "gunpowder",
    name: "Gunpowder",
    color: "#5b7c99",
    icon: `<path fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="m14.92 8.797l-.624 1.86a4.75 4.75 0 0 1-3.029 3.03l-1.882.626a.316.316 0 0 0 0 .601l1.882.626a4.74 4.74 0 0 1 3.005 3.007l.625 1.883a.317.317 0 0 0 .6 0l.649-1.86a4.75 4.75 0 0 1 3.005-3.007l1.881-.625a.316.316 0 0 0 0-.601l-1.858-.65a4.74 4.74 0 0 1-3.028-3.03l-.625-1.884a.317.317 0 0 0-.6.024M6.859 3.516l-.446 1.329A3.4 3.4 0 0 1 4.25 7.01l-1.345.446a.226.226 0 0 0 0 .43l1.345.447a3.39 3.39 0 0 1 2.146 2.148l.446 1.345a.226.226 0 0 0 .43 0l.462-1.328A3.4 3.4 0 0 1 9.88 8.35l1.345-.447a.226.226 0 0 0 0-.43L9.897 7.01a3.39 3.39 0 0 1-2.163-2.165l-.446-1.346a.226.226 0 0 0-.43.017"/>`,
  },
  {
    id: "ash",
    name: "Ash",
    color: "#95a5a6",
    icon: `<g fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"><path d="M16.5 7c3.485 0 3.485 5 0 5H3m15.411 8c3.452 0 3.452-5 0-5H3"/><path d="M11.411 19c3.452 0 3.452-4 0-4H3m7.872-11c3.506 0 3.506 5 0 5H3"/><path d="M4.872 5c3.506 0 3.506 4 0 4H3"/></g>`,
  },
];

const Liquids = [
  {
    id: "water",
    name: "Water",
    color: "#3498db",
    icon: `<path fill="none" stroke="currentColor" stroke-dasharray="28" stroke-dashoffset="28" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3c0 0 7 6 7 12c0 2 -1 6 -7 6M12 3c0 0 -7 6 -7 12c0 2 1 6 7 6"><animate fill="freeze" attributeName="stroke-dashoffset" dur="0.6s" values="28;0"/></path>`,
  },
  {
    id: "oil",
    name: "Oil",
    color: "#34495e",
    icon: `<path fill="none" stroke="currentColor" stroke-dasharray="28" stroke-dashoffset="28" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3c0 0 7 6 7 12c0 2 -1 6 -7 6M12 3c0 0 -7 6 -7 12c0 2 1 6 7 6"><animate fill="freeze" attributeName="stroke-dashoffset" dur="0.6s" values="28;0"/></path><path fill="currentColor" fill-opacity="0" d="M12 3C12 3 15.27 5.81 17.34 9.5L6 18.41C5.21 17.24 5 15.91 5 15C5 9 12 3 12 3Z"><animate fill="freeze" attributeName="fill-opacity" begin="0.6s" dur="0.15s" values="0;0.3"/></path>`,
  },
  {
    id: "acid",
    name: "Acid",
    color: "#2ecc71",
    icon: `<g fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"><path d="m12.5 17l-.5-1l-.5 1z"/><path d="M15 22a1 1 0 0 0 1-1v-1a2 2 0 0 0 1.56-3.25a8 8 0 1 0-11.12 0A2 2 0 0 0 8 20v1a1 1 0 0 0 1 1z"/><circle cx="15" cy="12" r="1"/><circle cx="9" cy="12" r="1"/></g>`,
  },
];

const solid = [
  {
    id: "wood",
    name: "Wood",
    color: "#8b4513",
    icon: `<g fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"><path d="M15 19v-2h6l-3.5-4.5h3L15 5l-2 2"/><path d="M10 17h7l-4.5-6.5h3L10 3l-5.5 7.5h3L3 17zm0 0v4"/></g>`,
  },
  {
    id: "stone",
    name: "Stone",
    color: "#7f8c8d",
    icon: `<path fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m8 3l4 8l5-5l5 15H2z"/>`,
  },
];

const Forces_AND_Interactions = [
  {
    id: "fire",
    name: "Fire",
    color: "#ff6b35",
    icon: `<path fill="currentColor" fill-rule="evenodd" d="M14.243 2.514c.215.28.257.587.257.794c0 2.296.608 4.377 2.096 5.88a29 29 0 0 0 .827.802c.774.727 1.774 1.667 2.223 2.94c.23.654.354 1.35.354 2.07c0 4.005-3.732 7-8 7c-3.148 0-5.946-1.6-7.247-4.028A6.3 6.3 0 0 1 4 14.999v-.863c0-1.515.229-3.022.679-4.47c.097-.318.237-.702.485-.979c.141-.157.39-.362.757-.407c.377-.046.674.1.852.232c.299.221.48.559.576.746c.118.233.234.51.342.778c.117.289.284.565.492.823c.124.153.226.282.319.399l.19.239a1.16 1.16 0 0 0 .2-.548l.196-2.183c.102-1.143.312-2.41.784-3.552c.476-1.151 1.241-2.232 2.478-2.892c.22-.117.621-.308 1.061-.264c.271.027.596.15.832.456m-1.68 2.124c-.346.356-.622.806-.842 1.34c-.361.874-.547 1.913-.642 2.966l-.195 2.183c-.04.446-.175.878-.395 1.268c-.28.498-.743.96-1.358 1.131c-.692.192-1.395-.042-1.877-.628c-.098-.12-.217-.269-.339-.422l-.287-.36a6 6 0 0 1-.377-.526A13 13 0 0 0 6 14.136V15c0 .72.182 1.405.516 2.029C7.428 18.73 9.498 20 12 20c3.464 0 6-2.378 6-5c0-.489-.084-.96-.24-1.405c-.28-.79-.915-1.395-1.76-2.196l-.195-.187q-.307-.291-.63-.617c-1.643-1.66-2.403-3.78-2.613-5.957Z" clip-rule="evenodd"/><path fill="currentColor" fill-rule="evenodd" d="m13.073 15.351l.936 1.366l.015.02c.586.811.374 1.955-.439 2.492a1.62 1.62 0 0 1-1.798 0m1.286-3.878l-.027.053l-.018.035a3.95 3.95 0 0 1-1.286 1.471l-.143.098c-.296.204-.428.355-.48.475c-.147.334-.148.634-.055.9c.096.274.318.578.723.846m-.442-4.903c.034-.142.063-.326.098-.543q.03-.2.07-.432c.03-.166.07-.382.127-.554c.015-.048.039-.112.072-.181c.027-.057.092-.184.213-.307a1.027 1.027 0 0 1 1.586.144l2.141 3.123c1.235 1.72.806 4.152-.965 5.322a3.62 3.62 0 0 1-4.002 0c-.706-.466-1.244-1.1-1.509-1.855c-.268-.764-.23-1.588.11-2.363c.281-.64.792-1.053 1.18-1.32l.143-.098c.269-.185.487-.435.634-.726l.053-.103l.033-.07z" clip-rule="evenodd"/>`,
  },
  {
    id: "steam",
    name: "Steam",
    color: "#ecf0f1",
    icon: `<g fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"><path d="M16.5 7c3.485 0 3.485 5 0 5H3m15.411 8c3.452 0 3.452-5 0-5H3"/><path d="M11.411 19c3.452 0 3.452-4 0-4H3m7.872-11c3.506 0 3.506 5 0 5H3"/><path d="M4.872 5c3.506 0 3.506 4 0 4H3"/></g>`,
  },
  {
    id: "eraser",
    name: "Eraser",
    color: "#fff",
    icon: `<g fill="none"><path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9.788 20.5h9.02m-9.02 0a3.47 3.47 0 0 0 2.486-1.02l1.29-1.29M9.788 20.5a3.47 3.47 0 0 1-2.438-1.02l-3.33-3.33a3.47 3.47 0 0 1-1.003-2.802M6.6 11.226l6.662 6.663l.301.301m0 0l5.417-5.417a3.48 3.48 0 0 0 0-4.923l-3.33-3.33a3.47 3.47 0 0 0-2.643-1.015"/><path fill="currentColor" d="M8.6 1.419a.64.64 0 0 0-1.2 0l-.167.449a4 4 0 0 1-2.366 2.365l-.449.166a.64.64 0 0 0 0 1.202l.45.166a4 4 0 0 1 2.365 2.366l.166.449a.64.64 0 0 0 1.202 0l.166-.449a4 4 0 0 1 2.366-2.366l.449-.166a.64.64 0 0 0 0-1.202l-.45-.166a4 4 0 0 1-2.365-2.365zM3.876 7.262a.4.4 0 0 0-.752 0l-.103.28a2.5 2.5 0 0 1-1.479 1.479l-.28.104a.4.4 0 0 0 0 .75l.28.105a2.5 2.5 0 0 1 1.479 1.478l.103.28a.4.4 0 0 0 .752 0l.103-.28A2.5 2.5 0 0 1 5.458 9.98l.28-.104a.4.4 0 0 0 0-.751l-.28-.104a2.5 2.5 0 0 1-1.479-1.479z"/></g>`,
  },
];

const Ink = [
  {
    id: "ink",
    name: "Ink",
    color: "#ff3b7a",
    icon: `<path fill="currentColor" d="M7 2h10a2 2 0 0 1 2 2v8a4 4 0 0 1-4 4h-2l-2 2l-2-2H7a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2Zm0 2v10h3.586L11 14.414L12.414 13H15a2 2 0 0 0 2-2V4H7Z"/>`,
  },
];

const gridW = 240;
const gridH = 130;

const TYPE_EMPTY = 0;
const TYPE_SAND = 1;
const TYPE_GUNPOWDER = 2;
const TYPE_ASH = 3;
const TYPE_WATER = 4;
const TYPE_OIL = 5;
const TYPE_ACID = 6;
const TYPE_WOOD = 7;
const TYPE_STONE = 8;
const TYPE_FIRE = 9;
const TYPE_STEAM = 10;
const TYPE_INK = 11;

const typeMap = {
  sand: TYPE_SAND,
  gunpowder: TYPE_GUNPOWDER,
  ash: TYPE_ASH,
  water: TYPE_WATER,
  oil: TYPE_OIL,
  acid: TYPE_ACID,
  wood: TYPE_WOOD,
  stone: TYPE_STONE,
  fire: TYPE_FIRE,
  steam: TYPE_STEAM,
  ink: TYPE_INK,
  eraser: -1,
};

const baseColors = {
  [TYPE_SAND]: "#f4d03f",
  [TYPE_GUNPOWDER]: "#5b7c99",
  [TYPE_ASH]: "#95a5a6",
  [TYPE_WATER]: "#3498db",
  [TYPE_OIL]: "#34495e",
  [TYPE_ACID]: "#2ecc71",
  [TYPE_WOOD]: "#8b4513",
  [TYPE_STONE]: "#7f8c8d",
  [TYPE_FIRE]: "#ff6b35",
  [TYPE_STEAM]: "#ecf0f1",
};

let typesA, typesB, colorsA, colorsB, heatA, heatB, lifeA, lifeB, acidA, acidB;
let useA = true;
let rafId = 0;

const temperatureNow = ref(20);
const paintedPixels = ref(0);

let offCanvas = null;
let offCtx = null;
let offImage = null;

function idx(x, y) {
  return y * gridW + x;
}

function clamp(v, a, b) {
  return v < a ? a : v > b ? b : v;
}

function hexToRgba32(hex) {
  const h = hex.replace("#", "").trim();
  const r = parseInt(h.slice(0, 2), 16) & 255;
  const g = parseInt(h.slice(2, 4), 16) & 255;
  const b = parseInt(h.slice(4, 6), 16) & 255;
  return (255 << 24) | (b << 16) | (g << 8) | r;
}

function density(t) {
  if (t === TYPE_STONE) return 10;
  if (t === TYPE_SAND) return 6;
  if (t === TYPE_GUNPOWDER) return 5;
  if (t === TYPE_ASH) return 3;
  if (t === TYPE_WATER) return 2;
  if (t === TYPE_ACID) return 2.2;
  if (t === TYPE_OIL) return 1.4;
  if (t === TYPE_WOOD) return 1.2;
  if (t === TYPE_INK) return 4.2;
  if (t === TYPE_FIRE) return 0.2;
  if (t === TYPE_STEAM) return 0.1;
  return 0;
}

function isLiquid(t) {
  return t === TYPE_WATER || t === TYPE_OIL || t === TYPE_ACID;
}

function isPowder(t) {
  return t === TYPE_SAND || t === TYPE_GUNPOWDER || t === TYPE_ASH;
}

function isFlammable(t) {
  return t === TYPE_WOOD || t === TYPE_OIL || t === TYPE_GUNPOWDER || t === TYPE_INK;
}

function isDissolvableByAcid(t) {
  return t !== TYPE_EMPTY && t !== TYPE_STONE && t !== TYPE_ACID && t !== TYPE_STEAM;
}

function currentBuffers() {
  if (useA) return { t: typesA, c: colorsA, h: heatA, l: lifeA, a: acidA };
  return { t: typesB, c: colorsB, h: heatB, l: lifeB, a: acidB };
}

function nextBuffers() {
  if (useA) return { t: typesB, c: colorsB, h: heatB, l: lifeB, a: acidB };
  return { t: typesA, c: colorsA, h: heatA, l: lifeA, a: acidA };
}

function setCell(buf, x, y, t, col32, heat, life, acidStrength) {
  const i = idx(x, y);
  buf.t[i] = t;
  buf.c[i] = col32;
  buf.h[i] = heat;
  buf.l[i] = life;
  buf.a[i] = acidStrength;
}

function copyCell(src, dst, sx, sy, dx, dy) {
  const si = idx(sx, sy);
  const di = idx(dx, dy);
  dst.t[di] = src.t[si];
  dst.c[di] = src.c[si];
  dst.h[di] = src.h[si];
  dst.l[di] = src.l[si];
  dst.a[di] = src.a[si];
}

function clearWorld() {
  const bufA = { t: typesA, c: colorsA, h: heatA, l: lifeA, a: acidA };
  const bufB = { t: typesB, c: colorsB, h: heatB, l: lifeB, a: acidB };
  for (let i = 0; i < gridW * gridH; i++) {
    bufA.t[i] = TYPE_EMPTY;
    bufA.c[i] = 0;
    bufA.h[i] = 20;
    bufA.l[i] = 0;
    bufA.a[i] = 0;

    bufB.t[i] = TYPE_EMPTY;
    bufB.c[i] = 0;
    bufB.h[i] = 20;
    bufB.l[i] = 0;
    bufB.a[i] = 0;
  }
}

function placeAtCell(cx, cy) {
  const selected = selectedMaterial.value;
  const code = typeMap[selected];
  const radius = Brushselected.value === 1 ? 5 : Brushselected.value === 2 ? 3 : 1;

  const buf = currentBuffers();
  for (let dy = -radius; dy <= radius; dy++) {
    for (let dx = -radius; dx <= radius; dx++) {
      if (dx * dx + dy * dy > radius * radius) continue;
      const x = cx + dx;
      const y = cy + dy;
      if (x < 0 || x >= gridW || y < 0 || y >= gridH) continue;
      const i = idx(x, y);

      if (code === -1) {
        buf.t[i] = TYPE_EMPTY;
        buf.c[i] = 0;
        buf.h[i] = 20;
        buf.l[i] = 0;
        buf.a[i] = 0;
        continue;
      }

      if (code === TYPE_INK) {
        buf.t[i] = TYPE_INK;
        buf.c[i] = hexToRgba32(inkColor.value);
        buf.h[i] = 20;
        buf.l[i] = 0;
        buf.a[i] = 0;
        continue;
      }

      buf.t[i] = code;
      buf.c[i] = hexToRgba32(baseColors[code] || "#ffffff");

      if (code === TYPE_FIRE) {
        buf.h[i] = 420 + Math.random() * 120;
        buf.l[i] = 28 + ((Math.random() * 24) | 0);
        buf.a[i] = 0;
      } else if (code === TYPE_STEAM) {
        buf.h[i] = 120;
        buf.l[i] = 28 + ((Math.random() * 30) | 0);
        buf.a[i] = 0;
      } else if (code === TYPE_ACID) {
        buf.h[i] = 22;
        buf.l[i] = 0;
        buf.a[i] = 240;
      } else {
        buf.h[i] = 20;
        buf.l[i] = 0;
        buf.a[i] = 0;
      }
    }
  }
}

function clientToCell(ev) {
  const canvas = canvasRef.value;
  const rect = canvas.getBoundingClientRect();
  const x = (ev.clientX - rect.left) / rect.width;
  const y = (ev.clientY - rect.top) / rect.height;
  const cx = clamp((x * gridW) | 0, 0, gridW - 1);
  const cy = clamp((y * gridH) | 0, 0, gridH - 1);
  return { cx, cy };
}

let drawing = false;

function onPointerDown(e) {
  drawing = true;
  const { cx, cy } = clientToCell(e);
  placeAtCell(cx, cy);
}

function onPointerMove(e) {
  if (!drawing) return;
  const { cx, cy } = clientToCell(e);
  placeAtCell(cx, cy);
}

function onPointerUp() {
  drawing = false;
}

function neighbors(x, y) {
  const list = [];
  for (let oy = -1; oy <= 1; oy++) {
    for (let ox = -1; ox <= 1; ox++) {
      if (ox === 0 && oy === 0) continue;
      const nx = x + ox;
      const ny = y + oy;
      if (nx < 0 || nx >= gridW || ny < 0 || ny >= gridH) continue;
      list.push({ x: nx, y: ny });
    }
  }
  return list;
}

function explodeAt(src, x, y, strength) {
  const dst = nextBuffers();
  const r = strength;
  for (let oy = -r; oy <= r; oy++) {
    for (let ox = -r; ox <= r; ox++) {
      const nx = x + ox;
      const ny = y + oy;
      if (nx < 0 || nx >= gridW || ny < 0 || ny >= gridH) continue;
      const d2 = ox * ox + oy * oy;
      if (d2 > r * r) continue;

      const si = idx(nx, ny);
      const t0 = src.t[si];
      if (t0 === TYPE_STONE) continue;

      const edge = d2 > (r * 0.7) * (r * 0.7);
      if (edge) {
        setCell(dst, nx, ny, TYPE_ASH, hexToRgba32(baseColors[TYPE_ASH]), 80, 0, 0);
      } else {
        setCell(
          dst,
          nx,
          ny,
          TYPE_FIRE,
          hexToRgba32(baseColors[TYPE_FIRE]),
          520 + Math.random() * 160,
          16 + ((Math.random() * 18) | 0),
          0
        );
      }
    }
  }
}

function tryFallTwo(src, x, y, dst) {
  const y1 = y + 1;
  if (y1 >= gridH) return false;
  const i1 = idx(x, y1);
  if (src.t[i1] === TYPE_EMPTY) {
    const y2 = y + 2;
    if (y2 < gridH && src.t[idx(x, y2)] === TYPE_EMPTY) {
      copyCell(src, dst, x, y, x, y2);
      setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
      return true;
    }
    copyCell(src, dst, x, y, x, y1);
    setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
    return true;
  }
  return false;
}

function stepSimulation() {
  const src = currentBuffers();
  const dst = nextBuffers();

  for (let i = 0; i < gridW * gridH; i++) {
    dst.t[i] = src.t[i];
    dst.c[i] = src.c[i];
    dst.h[i] = src.h[i];
    dst.l[i] = src.l[i];
    dst.a[i] = src.a[i];
  }

  for (let y = gridH - 1; y >= 0; y--) {
    for (let x = 0; x < gridW; x++) {
      const i = idx(x, y);
      const t = src.t[i];
      if (t === TYPE_EMPTY) continue;

      if (t === TYPE_FIRE) {
        let life = src.l[i];
        let heat = src.h[i];

        const ns = neighbors(x, y);
        let hasWater = false;
        for (let k = 0; k < ns.length; k++) {
          const ni = idx(ns[k].x, ns[k].y);
          if (src.t[ni] === TYPE_WATER) hasWater = true;
        }

        if (hasWater) {
          setCell(dst, x, y, TYPE_STEAM, hexToRgba32(baseColors[TYPE_STEAM]), 120, 24 + ((Math.random() * 24) | 0), 0);
          continue;
        }

        for (let k = 0; k < ns.length; k++) {
          const nx = ns[k].x;
          const ny = ns[k].y;
          const ni = idx(nx, ny);
          const nt = src.t[ni];

          if (nt === TYPE_GUNPOWDER) {
            explodeAt(src, nx, ny, 6);
            setCell(dst, nx, ny, TYPE_EMPTY, 0, 20, 0, 0);
          } else if (isFlammable(nt)) {
            if (Math.random() < (nt === TYPE_OIL ? 0.42 : nt === TYPE_GUNPOWDER ? 0.55 : 0.28)) {
              setCell(dst, nx, ny, TYPE_FIRE, hexToRgba32(baseColors[TYPE_FIRE]), 420 + Math.random() * 180, 18 + ((Math.random() * 28) | 0), 0);
            }
          } else if (nt === TYPE_WATER) {
            setCell(dst, nx, ny, TYPE_STEAM, hexToRgba32(baseColors[TYPE_STEAM]), 120, 20 + ((Math.random() * 20) | 0), 0);
          }
        }

        if (life > 0) life--;
        heat = Math.max(90, heat - 7);

        if (life <= 0) {
          if (Math.random() < 0.55) {
            setCell(dst, x, y, TYPE_ASH, hexToRgba32(baseColors[TYPE_ASH]), 70, 0, 0);
          } else {
            setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
          }
          continue;
        } else {
          dst.l[i] = life;
          dst.h[i] = heat;
          if (y > 0 && src.t[idx(x, y - 1)] === TYPE_EMPTY && Math.random() < 0.32) {
            copyCell(src, dst, x, y, x, y - 1);
            setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
          }
          continue;
        }
      }

      if (t === TYPE_STEAM) {
        let life = src.l[i];
        let heat = src.h[i];
        if (life > 0) life--;
        heat = Math.max(30, heat - 4);

        if (life <= 0) {
          setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
          continue;
        } else {
          dst.l[i] = life;
          dst.h[i] = heat;

          if (y > 0 && src.t[idx(x, y - 1)] === TYPE_EMPTY) {
            copyCell(src, dst, x, y, x, y - 1);
            setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
          } else if (Math.random() < 0.45) {
            const dir = Math.random() < 0.5 ? -1 : 1;
            const nx = x + dir;
            if (nx >= 0 && nx < gridW && src.t[idx(nx, y)] === TYPE_EMPTY) {
              copyCell(src, dst, x, y, nx, y);
              setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
            }
          }
          continue;
        }
      }

      if (t === TYPE_ACID) {
        let strength = src.a[i] || 240;
        const ns = neighbors(x, y);

        for (let k = 0; k < ns.length; k++) {
          const nx = ns[k].x;
          const ny = ns[k].y;
          const ni = idx(nx, ny);
          const nt = src.t[ni];

          if (isDissolvableByAcid(nt) && Math.random() < 0.28) {
            setCell(dst, nx, ny, TYPE_EMPTY, 0, 20, 0, 0);
            strength = Math.max(0, strength - 10);
            if (Math.random() < 0.28) {
              setCell(dst, x, y, TYPE_STEAM, hexToRgba32(baseColors[TYPE_STEAM]), 110, 16 + ((Math.random() * 18) | 0), 0);
              break;
            }
          }
        }

        if (dst.t[idx(x, y)] === TYPE_ACID) dst.a[idx(x, y)] = strength;

        if (strength <= 10 && dst.t[idx(x, y)] === TYPE_ACID) {
          setCell(dst, x, y, TYPE_WATER, hexToRgba32(baseColors[TYPE_WATER]), 20, 0, 0);
        }
      }

      if (t === TYPE_WATER || t === TYPE_OIL || t === TYPE_ACID) {
        if (tryFallTwo(src, x, y, dst)) continue;

        const belowY = y + 1;
        if (belowY < gridH) {
          const bi = idx(x, belowY);
          const bt = src.t[bi];

          if (isLiquid(bt) || isPowder(bt) || bt === TYPE_FIRE || bt === TYPE_STEAM) {
            if (density(t) > density(bt) && bt !== TYPE_STONE) {
              copyCell(src, dst, x, y, x, belowY);
              copyCell(src, dst, x, belowY, x, y);
              continue;
            }
          }
        }

        const dir = Math.random() < 0.5 ? -1 : 1;
        const nx = x + dir;
        if (nx >= 0 && nx < gridW && src.t[idx(nx, y)] === TYPE_EMPTY) {
          copyCell(src, dst, x, y, nx, y);
          setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
          continue;
        } else {
          const ox = x - dir;
          if (ox >= 0 && ox < gridW && src.t[idx(ox, y)] === TYPE_EMPTY) {
            copyCell(src, dst, x, y, ox, y);
            setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
            continue;
          }
        }

        if (t === TYPE_WATER) {
          if (src.h[i] > 105 && Math.random() < 0.18) {
            setCell(dst, x, y, TYPE_STEAM, hexToRgba32(baseColors[TYPE_STEAM]), 120, 28 + ((Math.random() * 25) | 0), 0);
          }
        }
      }

      if (t === TYPE_SAND || t === TYPE_GUNPOWDER || t === TYPE_ASH || t === TYPE_WOOD || t === TYPE_INK) {
        if (tryFallTwo(src, x, y, dst)) continue;

        const by = y + 1;
        if (by < gridH) {
          const bi = idx(x, by);
          const bt = src.t[bi];

          if ((isLiquid(bt) || bt === TYPE_FIRE || bt === TYPE_STEAM) && density(t) > density(bt) && bt !== TYPE_STONE) {
            copyCell(src, dst, x, y, x, by);
            copyCell(src, dst, x, by, x, y);
            continue;
          }

          if (bt !== TYPE_EMPTY && bt !== TYPE_STONE && density(t) <= density(bt)) {
          } else if (bt === TYPE_EMPTY) {
            copyCell(src, dst, x, y, x, by);
            setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
            continue;
          }

          if (t !== TYPE_WOOD) {
            const d = Math.random() < 0.5 ? -1 : 1;
            const dx1 = x + d;
            const dx2 = x - d;

            if (dx1 >= 0 && dx1 < gridW) {
              const di1 = idx(dx1, by);
              const dt1 = src.t[di1];
              if (dt1 === TYPE_EMPTY || ((isLiquid(dt1) || dt1 === TYPE_FIRE || dt1 === TYPE_STEAM) && density(t) > density(dt1))) {
                copyCell(src, dst, x, y, dx1, by);
                setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
                continue;
              }
            }

            if (dx2 >= 0 && dx2 < gridW) {
              const di2 = idx(dx2, by);
              const dt2 = src.t[di2];
              if (dt2 === TYPE_EMPTY || ((isLiquid(dt2) || dt2 === TYPE_FIRE || dt2 === TYPE_STEAM) && density(t) > density(dt2))) {
                copyCell(src, dst, x, y, dx2, by);
                setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
                continue;
              }
            }
          }
        }

        if (t === TYPE_ASH) {
          if (y > 0 && src.t[idx(x, y - 1)] === TYPE_EMPTY && Math.random() < 0.12) {
            copyCell(src, dst, x, y, x, y - 1);
            setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
            continue;
          }
          if (Math.random() < 0.15) {
            const dir = Math.random() < 0.5 ? -1 : 1;
            const nx = x + dir;
            if (nx >= 0 && nx < gridW && src.t[idx(nx, y)] === TYPE_EMPTY) {
              copyCell(src, dst, x, y, nx, y);
              setCell(dst, x, y, TYPE_EMPTY, 0, 20, 0, 0);
              continue;
            }
          }
        }

        if (t === TYPE_WOOD) {
          if (y > 0) {
            const up = idx(x, y - 1);
            if (isLiquid(src.t[up]) && density(src.t[up]) > density(TYPE_WOOD) && Math.random() < 0.18) {
              copyCell(src, dst, x, y, x, y - 1);
              copyCell(src, dst, x, y - 1, x, y);
              continue;
            }
          }
        }
      }
    }
  }

  for (let y = 0; y < gridH; y++) {
    for (let x = 0; x < gridW; x++) {
      const i = idx(x, y);
      const t = dst.t[i];
      let h = dst.h[i];

      if (t === TYPE_EMPTY) {
        dst.h[i] = 20;
        continue;
      }

      if (t === TYPE_FIRE) h = Math.max(h, 380);
      if (t === TYPE_STEAM) h = Math.max(h, 110);

      let sum = h * 0.68;
      let cnt = 0.68;

      if (x > 0) {
        sum += dst.h[idx(x - 1, y)] * 0.08;
        cnt += 0.08;
      }
      if (x + 1 < gridW) {
        sum += dst.h[idx(x + 1, y)] * 0.08;
        cnt += 0.08;
      }
      if (y > 0) {
        sum += dst.h[idx(x, y - 1)] * 0.08;
        cnt += 0.08;
      }
      if (y + 1 < gridH) {
        sum += dst.h[idx(x, y + 1)] * 0.08;
        cnt += 0.08;
      }

      let nh = sum / cnt;
      if (t !== TYPE_FIRE && t !== TYPE_STEAM) nh = Math.max(20, nh - 0.55);
      dst.h[i] = nh;

      if (t === TYPE_WATER && nh > 105 && Math.random() < 0.12) {
        dst.t[i] = TYPE_STEAM;
        dst.c[i] = hexToRgba32(baseColors[TYPE_STEAM]);
        dst.h[i] = 120;
        dst.l[i] = 25 + ((Math.random() * 25) | 0);
        dst.a[i] = 0;
      }
    }
  }

  useA = !useA;
}

function render() {
  const canvas = canvasRef.value;
  if (!canvas) return;
  const ctx = canvas.getContext("2d", { alpha: false });

  const buf = currentBuffers();

  let painted = 0;
  let tempSum = 0;
  let tempCnt = 0;

  if (!offCanvas) {
    offCanvas = document.createElement("canvas");
    offCanvas.width = gridW;
    offCanvas.height = gridH;
    offCtx = offCanvas.getContext("2d", { alpha: false });
    offImage = offCtx.createImageData(gridW, gridH);
  }

  const data = offImage.data;
  const bg = hexToRgba32("#06131a");

  for (let y = 0; y < gridH; y++) {
    for (let x = 0; x < gridW; x++) {
      const i = idx(x, y);
      const t = buf.t[i];

      let color32 = bg;

      if (t !== TYPE_EMPTY) {
        painted++;
        tempSum += buf.h[i];
        tempCnt++;

        if (t === TYPE_INK) color32 = buf.c[i] || hexToRgba32(inkColor.value);
        else color32 = buf.c[i] || hexToRgba32(baseColors[t] || "#ffffff");
      }

      let r = color32 & 255;
      let g = (color32 >> 8) & 255;
      let b = (color32 >> 16) & 255;

      const ht = buf.h[i];
      if (t === TYPE_FIRE) {
        const k = clamp((ht - 260) / 300, 0, 1);
        r = clamp((r + 110 * k) | 0, 0, 255);
        g = clamp((g + 25 * k) | 0, 0, 255);
        b = clamp((b - 35 * k) | 0, 0, 255);
      } else if (t === TYPE_STEAM) {
        const k = clamp((ht - 40) / 160, 0, 1);
        r = clamp((r + 35 * k) | 0, 0, 255);
        g = clamp((g + 35 * k) | 0, 0, 255);
        b = clamp((b + 35 * k) | 0, 0, 255);
      } else if (t !== TYPE_EMPTY) {
        const warm = clamp((ht - 20) / 360, 0, 1);
        r = clamp((r + 40 * warm) | 0, 0, 255);
        g = clamp((g + 10 * warm) | 0, 0, 255);
      }

      const p = (y * gridW + x) * 4;
      data[p] = r;
      data[p + 1] = g;
      data[p + 2] = b;
      data[p + 3] = 255;
    }
  }

  offCtx.putImageData(offImage, 0, 0);
  ctx.imageSmoothingEnabled = false;
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.drawImage(offCanvas, 0, 0, canvas.width, canvas.height);

  paintedPixels.value = painted;
  temperatureNow.value = tempCnt ? Math.round(tempSum / tempCnt) : 20;
}

function loop() {
  if (isRun.value) {
    stepSimulation();
    stepSimulation();
  }
  render();
  rafId = requestAnimationFrame(loop);
}

function resizeCanvas() {
  const canvas = canvasRef.value;
  if (!canvas) return;
  const parent = canvas.parentElement;
  if (!parent) return;
  const rect = parent.getBoundingClientRect();
  const targetW = Math.max(320, Math.floor(rect.width));
  const targetH = Math.max(320, Math.floor(rect.height));
  canvas.width = targetW;
  canvas.height = targetH;
}

const fileInputRef = ref(null);

function openFileDialog() {
  fileInputRef.value?.click();
}

function importImageToWorld(file) {
  const reader = new FileReader();
  reader.onload = () => {
    const img = new Image();
    img.onload = () => {
      const off = document.createElement("canvas");
      off.width = gridW;
      off.height = gridH;
      const octx = off.getContext("2d");
      octx.clearRect(0, 0, gridW, gridH);

      const scale = Math.max(gridW / img.width, gridH / img.height);
      const dw = img.width * scale;
      const dh = img.height * scale;
      const dx = (gridW - dw) / 2;
      const dy = (gridH - dh) / 2;

      octx.drawImage(img, dx, dy, dw, dh);
      const data = octx.getImageData(0, 0, gridW, gridH).data;

      const buf = currentBuffers();
      for (let y = 0; y < gridH; y++) {
        for (let x = 0; x < gridW; x++) {
          const p = (y * gridW + x) * 4;
          const r = data[p];
          const g = data[p + 1];
          const b = data[p + 2];
          const a = data[p + 3];

          const i = idx(x, y);
          if (a < 25) {
            buf.t[i] = TYPE_EMPTY;
            buf.c[i] = 0;
            buf.h[i] = 20;
            buf.l[i] = 0;
            buf.a[i] = 0;
          } else {
            buf.t[i] = TYPE_INK;
            buf.c[i] = (255 << 24) | (b << 16) | (g << 8) | r;
            buf.h[i] = 20;
            buf.l[i] = 0;
            buf.a[i] = 0;
          }
        }
      }
    };
    img.src = reader.result;
  };
  reader.readAsDataURL(file);
}

function onFileChange(e) {
  const file = e.target.files?.[0];
  if (!file) return;
  importImageToWorld(file);
  e.target.value = "";
}

onMounted(() => {
  typesA = new Uint8Array(gridW * gridH);
  typesB = new Uint8Array(gridW * gridH);
  colorsA = new Uint32Array(gridW * gridH);
  colorsB = new Uint32Array(gridW * gridH);
  heatA = new Float32Array(gridW * gridH);
  heatB = new Float32Array(gridW * gridH);
  lifeA = new Uint16Array(gridW * gridH);
  lifeB = new Uint16Array(gridW * gridH);
  acidA = new Uint8Array(gridW * gridH);
  acidB = new Uint8Array(gridW * gridH);

  clearWorld();
  resizeCanvas();
  window.addEventListener("resize", resizeCanvas);
  rafId = requestAnimationFrame(loop);
});

onBeforeUnmount(() => {
  cancelAnimationFrame(rafId);
  window.removeEventListener("resize", resizeCanvas);
});
</script>

<template>
  <header class="flex items-center py-6 mx-4 md:mx-[3rem] gap-4">
    <div class="relative flex items-center justify-center w-[50px] h-[50px]">
      <span class="absolute inset-0 text-cyan-400 opacity-70 ripple-anim">
        <svg xmlns="http://www.w3.org/2000/svg" width="50" height="50" viewBox="0 0 24 24">
          <g fill="none" stroke="currentColor" stroke-width="1.5">
            <path
              stroke-linecap="round"
              d="M12 5.793a28 28 0 0 1 3.342 2.865A28 28 0 0 1 18.207 12M12 5.793a28 28 0 0 0-3.342 2.865A28 28 0 0 0 5.793 12M12 5.793c3.57-2.584 6.947-3.554 8.354-2.147S20.791 8.43 18.207 12m0 0c2.584 3.57 3.554 6.947 2.147 8.354c-1.043 1.043-3.17.78-5.654-.48M18.207 12a28 28 0 0 1-2.865 3.342A28 28 0 0 1 12 18.207m0 0a28 28 0 0 1-3.342-2.865A28 28 0 0 1 5.793 12M12 18.207c-3.57 2.584-6.947 3.554-8.354 2.147S3.209 15.57 5.793 12m0 0C3.21 8.43 2.24 5.053 3.646 3.646c1.043-1.043 3.17-.78 5.654.48"
            />
            <circle cx="12" cy="12" r="2" />
          </g>
        </svg>
      </span>

      <span class="relative z-10 text-cyan-400">
        <svg xmlns="http://www.w3.org/2000/svg" width="50" height="50" viewBox="0 0 24 24">
          <g fill="none" stroke="currentColor" stroke-width="1.5">
            <path
              stroke-linecap="round"
              d="M12 5.793a28 28 0 0 1 3.342 2.865A28 28 0 0 1 18.207 12M12 5.793a28 28 0 0 0-3.342 2.865A28 28 0 0 0 5.793 12M12 5.793c3.57-2.584 6.947-3.554 8.354-2.147S20.791 8.43 18.207 12m0 0c2.584 3.57 3.554 6.947 2.147 8.354c-1.043 1.043-3.17.78-5.654-.48M18.207 12a28 28 0 0 1-2.865 3.342A28 28 0 0 1 12 18.207m0 0a28 28 0 0 1-3.342-2.865A28 28 0 0 1 5.793 12M12 18.207c-3.57 2.584-6.947 3.554-8.354 2.147S3.209 15.57 5.793 12m0 0C3.21 8.43 2.24 5.053 3.646 3.646c1.043-1.043 3.17-.78 5.654.48"
            />
            <circle cx="12" cy="12" r="2" />
          </g>
        </svg>
      </span>
    </div>

    <div>
      <h1 class="text-2xl linerColor font-bold">Simulation of elementary physics</h1>
      <p class="text-xs text-gray-600 mt-1">Interactive sandbox with realistic simulation of elements and interactions</p>
    </div>
  </header>

  <hr class="border-[#01141b]" />

  <main class="grid grid-cols-1 lg:grid-cols-12 gap-6 min-h-[calc(100vh-110px)] mx-4 md:mx-[3rem] py-6">
    <aside class="lg:col-span-2 col-span-1">
      <div class="p-4 border border-[#0c3345] rounded-2xl flex flex-col gap-5 AllBG w-full lg:w-[200px]">
        <div>
          <h3 class="text-xs font-semibold mb-2 linerColor">Granular materials</h3>
          <div class="flex flex-wrap gap-3">
            <button
              v-for="mat in materials"
              :key="mat.id"
              @click="selectedMaterial = mat.id"
              class="group w-[45px] h-[45px] px-3 py-2 rounded-lg border-2 hover:scale-110 transition-all duration-300 relative flex justify-center items-center"
              :class="[
                selectedMaterial === mat.id ? 'bg-[#083242] border-[#00d3f3]' : 'bg-[#141b29] border-[#242e3d] hover:bg-gray-700',
              ]"
            >
              <span class="absolute w-2 h-2 rounded-full top-1 right-1" :style="{ backgroundColor: mat.color }"></span>
              <div
                class="transition-colors duration-300"
                :style="{ color: selectedMaterial === mat.id ? mat.color : '' }"
                :class="selectedMaterial !== mat.id ? 'text-white' : ''"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  v-html="mat.icon"
                  class="transition-colors duration-300 group-hover:!text-[color:var(--hover-color)]"
                  :style="{ '--hover-color': mat.color }"
                ></svg>
              </div>
            </button>
          </div>
        </div>

        <div>
          <h3 class="text-xs font-semibold mb-2 linerColor">Liquids</h3>
          <div class="flex flex-wrap gap-3">
            <button
              v-for="mat in Liquids"
              :key="mat.id"
              @click="selectedMaterial = mat.id"
              class="group w-[45px] h-[45px] px-3 py-2 rounded-lg border-2 hover:scale-110 transition-all duration-300 relative flex justify-center items-center"
              :class="[
                selectedMaterial === mat.id ? 'bg-[#083242] border-[#00d3f3]' : 'bg-[#141b29] border-[#242e3d] hover:bg-gray-700',
              ]"
            >
              <span class="absolute w-2 h-2 rounded-full top-1 right-1" :style="{ backgroundColor: mat.color }"></span>
              <div
                class="transition-colors duration-300"
                :style="{ color: selectedMaterial === mat.id ? mat.color : '' }"
                :class="selectedMaterial !== mat.id ? 'text-white' : ''"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  v-html="mat.icon"
                  class="transition-colors duration-300 group-hover:!text-[color:var(--hover-color)]"
                  :style="{ '--hover-color': mat.color }"
                ></svg>
              </div>
            </button>
          </div>
        </div>

        <div>
          <h3 class="text-xs font-semibold mb-2 linerColor">solid</h3>
          <div class="flex flex-wrap gap-3">
            <button
              v-for="mat in solid"
              :key="mat.id"
              @click="selectedMaterial = mat.id"
              class="group w-[45px] h-[45px] px-3 py-2 rounded-lg border-2 hover:scale-110 transition-all duration-300 relative flex justify-center items-center"
              :class="[
                selectedMaterial === mat.id ? 'bg-[#083242] border-[#00d3f3]' : 'bg-[#141b29] border-[#242e3d] hover:bg-gray-700',
              ]"
            >
              <span class="absolute w-2 h-2 rounded-full top-1 right-1" :style="{ backgroundColor: mat.color }"></span>
              <div
                class="transition-colors duration-300"
                :style="{ color: selectedMaterial === mat.id ? mat.color : '' }"
                :class="selectedMaterial !== mat.id ? 'text-white' : ''"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  v-html="mat.icon"
                  class="transition-colors duration-300 group-hover:!text-[color:var(--hover-color)]"
                  :style="{ '--hover-color': mat.color }"
                ></svg>
              </div>
            </button>
          </div>
        </div>

        <div>
          <h3 class="text-xs font-semibold mb-2 linerColor">Forces and interactions</h3>
          <div class="flex flex-wrap gap-3">
            <button
              v-for="mat in Forces_AND_Interactions"
              :key="mat.id"
              @click="selectedMaterial = mat.id"
              class="group w-[45px] h-[45px] px-3 py-2 rounded-lg border-2 hover:scale-110 transition-all duration-300 relative flex justify-center items-center"
              :class="[
                selectedMaterial === mat.id ? 'bg-[#083242] border-[#00d3f3]' : 'bg-[#141b29] border-[#242e3d] hover:bg-gray-700',
              ]"
            >
              <span class="absolute w-2 h-2 rounded-full top-1 right-1" :style="{ backgroundColor: mat.color }"></span>
              <div
                class="transition-colors duration-300"
                :style="{ color: selectedMaterial === mat.id ? mat.color : '' }"
                :class="selectedMaterial !== mat.id ? 'text-white' : ''"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  v-html="mat.icon"
                  class="transition-colors duration-300 group-hover:!text-[color:var(--hover-color)]"
                  :style="{ '--hover-color': mat.color }"
                ></svg>
              </div>
            </button>
          </div>
        </div>

        <div>
          <h3 class="text-xs font-semibold mb-2 linerColor">Ink</h3>
          <div class="flex items-center gap-3">
            <button
              v-for="mat in Ink"
              :key="mat.id"
              @click="selectedMaterial = mat.id"
              class="group w-[45px] h-[45px] px-3 py-2 rounded-lg border-2 hover:scale-110 transition-all duration-300 relative flex justify-center items-center"
              :class="[
                selectedMaterial === mat.id ? 'bg-[#083242] border-[#00d3f3]' : 'bg-[#141b29] border-[#242e3d] hover:bg-gray-700',
              ]"
            >
              <span class="absolute w-2 h-2 rounded-full top-1 right-1" :style="{ backgroundColor: inkColor }"></span>
              <div
                class="transition-colors duration-300"
                :style="{ color: selectedMaterial === mat.id ? inkColor : '' }"
                :class="selectedMaterial !== mat.id ? 'text-white' : ''"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  v-html="mat.icon"
                  class="transition-colors duration-300 group-hover:!text-[color:var(--hover-color)]"
                  :style="{ '--hover-color': inkColor }"
                ></svg>
              </div>
            </button>

            <div class="flex flex-col gap-1">
              <span class="text-[11px] text-[#6a7282]">Ink color</span>
              <input v-model="inkColor" type="color" class="w-[55px] h-[28px] rounded-md border border-[#242e3d] bg-[#141b29]" />
            </div>
          </div>
        </div>

        <hr class="border-[#1b2129]" />

        <div class="flex justify-center items-center flex-col">
          <h4 class="text-[#6a7282] text-sm">Current material</h4>
          <p class="text-sm mt-3" :style="{ color: selectMaterial().color }">{{ selectMaterial().name }}</p>
        </div>
      </div>
    </aside>

    <section class="lg:col-span-10 col-span-1 p-0 lg:p-6 flex flex-col">
      <header class="w-full flex justify-center items-center">
        <div class="linerBG w-full xl:w-[78%] flex flex-wrap justify-center items-center gap-4 rounded-lg p-3 border border-[#0c3345]">
          <span class="text-[#6a7282]">control</span>

          <div class="flex justify-evenly items-center gap-3">
            <button
              @click="isRun = !isRun"
              class="p-3 border rounded-lg transition-all duration-300 ease-in-out active:scale-95 hover:scale-105"
              :class="isRun ? 'bg-[#ec4f0043] text-orange-600 border-orange-600' : 'bg-[#ff000043] text-red-600 border-red-600'"
            >
              <Transition
                mode="out-in"
                enter-active-class="transition duration-200 ease-out"
                enter-from-class="opacity-0 scale-75 rotate-45"
                enter-to-class="opacity-100 scale-100 rotate-0"
                leave-active-class="transition duration-200 ease-in"
                leave-from-class="opacity-100 scale-100 rotate-0"
                leave-to-class="opacity-0 scale-75 -rotate-45"
              >
                <svg v-if="isRun" key="pause" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                  <g fill="currentColor" fill-opacity=".25" stroke="currentColor" stroke-linecap="round" stroke-width="1.2">
                    <rect width="4" height="14" x="6" y="5" rx="1" />
                    <rect width="4" height="14" x="14" y="5" rx="1" />
                  </g>
                </svg>

                <svg v-else key="play" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                  <path fill="currentColor" d="M8 19V5l11 7zm2-3.65L15.25 12L10 8.65z" />
                </svg>
              </Transition>
            </button>

            <button
              @click="clearWorld"
              class="p-3 border rounded-lg bg-[#2600ff43] text-blue-600 border-blue-600 hover:scale-105 active:scale-95 transition-all ease-[cubic-bezier(0.19, 1, 0.22, 1)] duration-[2s] hover:rotate-[-180deg]"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                <!-- Icon from Basil by Craftwork - https://creativecommons.org/licenses/by/4.0/ -->
                <path
                  fill="currentColor"
                  d="M6.545 8.163a.75.75 0 0 1-.487-1.044l1.66-3.535a.75.75 0 0 1 1.36.002l.732 1.569l.08-.027a8.15 8.15 0 1 1-5.8 5.903a.75.75 0 1 1 1.456.364a6.65 6.65 0 1 0 4.907-4.862l.74 1.583a.75.75 0 0 1-.872 1.043z"
                />
              </svg>
            </button>

            <button
              @click="openFileDialog"
              class="p-3 border rounded-lg bg-[#00d3f325] text-cyan-400 border-[#00d3f3] hover:scale-105 active:scale-95 transition-all duration-300"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                <path
                  fill="currentColor"
                  d="M19 20H5a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h4l2 2h8a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2ZM7 9h10v2H7V9Zm0 4h10v2H7v-2Z"
                />
              </svg>
            </button>

            <input ref="fileInputRef" type="file" accept="image/*" class="hidden" @change="onFileChange" />
          </div>

          <span class="text-[#6a7282]">brush size</span>
          <div class="flex justify-center items-center gap-2">
            <span @click="Brushselected = 1" :class="Brushselected == 1 ? 'active-brush' : ''" class="w-4 rounded-full h-4 border border-white"></span>
            <span @click="Brushselected = 2" :class="Brushselected == 2 ? 'active-brush' : ''" class="w-3 rounded-full h-3 border border-white"></span>
            <span @click="Brushselected = 3" :class="Brushselected == 3 ? 'active-brush' : ''" class="w-2 rounded-full h-2 border border-white"></span>
          </div>

          <span class="text-[#6a7282]">status</span>
          <div class="p-2 border rounded-lg text-sm flex justify-center items-center gap-2" :class="isRun ? 'text-green-600' : 'text-red-600'">
            <span class="w-2 h-2 rounded-full blur-[1px]" :class="isRun ? 'bg-green-600' : 'bg-red-600'"></span>
            {{ isRun ? "Running" : "Stopped" }}
          </div>
        </div>
      </header>

      <div class="w-full flex-1 mt-4 min-h-[420px]">
        <div class="w-full h-full border border-[#0c3345] rounded-2xl overflow-hidden AllBG relative">
          <canvas
            ref="canvasRef"
            class="w-full h-full touch-none select-none"
            @pointerdown="onPointerDown"
            @pointermove="onPointerMove"
            @pointerup="onPointerUp"
            @pointercancel="onPointerUp"
            @pointerleave="onPointerUp"
          ></canvas>

          <div class="absolute top-3 left-3 flex flex-col gap-2">
            <div class="px-3 py-2 rounded-xl border border-[#0c3345] bg-[#0000006b] backdrop-blur-md text-xs text-[#cbd5e1] flex items-center gap-2">
              <span class="w-2 h-2 rounded-full bg-cyan-400 blur-[1px]"></span>
              Temperature: <span class="text-cyan-400">{{ temperatureNow }}°C</span>
            </div>
            <div class="px-3 py-2 rounded-xl border border-[#0c3345] bg-[#0000006b] backdrop-blur-md text-xs text-[#cbd5e1] flex items-center gap-2">
              <span class="w-2 h-2 rounded-full bg-purple-400 blur-[1px]"></span>
              Pixels: <span class="text-purple-300">{{ paintedPixels }}</span>
            </div>
          </div>

          <div class="absolute bottom-3 right-3 px-3 py-2 rounded-xl border border-[#0c3345] bg-[#0000006b] backdrop-blur-md text-xs text-[#cbd5e1] flex items-center gap-2">
            <span class="w-2 h-2 rounded-full" :style="{ backgroundColor: selectMaterial().color }"></span>
            <span :style="{ color: selectMaterial().color }">{{ selectMaterial().name }}</span>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style>
.linerColor {
  background: linear-gradient(45deg, #00d0f3, #aa88ff);
  /* -webkit-background-clip: text; */
  -webkit-text-fill-color: transparent;
}

.AllBG {
  background: linear-gradient(90deg, #0c3345, #000);
}

.linerBG {
  background: linear-gradient(90deg, #0c3345, #000, #0c3345);
}

@keyframes ripple {
  0% {
    transform: scale(1);
    opacity: 0.5;
  }

  100% {
    transform: scale(1.6);
    opacity: 0;
  }
}

.ripple-anim {
  animation: ripple 2s infinite ease-out;
}

button:hover svg {
  color: var(--hover-color) !important;
}

.active-brush {
  background-color: white;
  filter: blur(2px);
  transition: all 0.3s ease-in-out;
}
</style>
