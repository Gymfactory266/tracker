<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>Talo — Tracker</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;600&display=swap');

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

  :root {
    --red: #e10813;
    --dark: #0a0a0a;
    --card: #141414;
    --border: rgba(255,255,255,0.07);
    --text: #ffffff;
    --muted: rgba(255,255,255,0.4);
    --green: #10b981;
    --yellow: #f59e0b;
  }

  body {
    background: var(--dark);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    min-height: 100vh;
    padding-bottom: 80px;
  }

  .header {
    background: var(--dark);
    border-bottom: 0.5px solid var(--border);
    padding: 16px 20px 12px;
    position: sticky;
    top: 0;
    z-index: 100;
  }
  .header-top {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 14px;
  }
  .logo { font-family: 'Bebas Neue', sans-serif; font-size: 22px; letter-spacing: 2px; color: var(--red); }
  .semana-badge {
    font-size: 11px; letter-spacing: 2px; text-transform: uppercase; color: var(--muted);
    background: rgba(255,255,255,0.05); padding: 4px 10px; border-radius: 20px; border: 0.5px solid var(--border);
  }

  .days-scroll { display: flex; gap: 8px; overflow-x: auto; scrollbar-width: none; padding-bottom: 2px; }
  .days-scroll::-webkit-scrollbar { display: none; }
  .day-tab {
    flex-shrink: 0; padding: 7px 14px; border-radius: 20px; border: 0.5px solid var(--border);
    font-size: 12px; font-weight: 500; color: var(--muted); background: transparent;
    cursor: pointer; transition: all 0.2s; white-space: nowrap;
  }
  .day-tab.active { background: var(--red); border-color: var(--red); color: white; }

  .content { padding: 16px 16px 0; }
  .day-panel { display: none; }
  .day-panel.active { display: block; }

  .day-title { font-family: 'Bebas Neue', sans-serif; font-size: 32px; letter-spacing: 2px; margin-bottom: 4px; }
  .day-subtitle { font-size: 12px; color: var(--muted); letter-spacing: 1px; text-transform: uppercase; margin-bottom: 16px; }

  .progress-wrap { background: rgba(255,255,255,0.06); border-radius: 4px; height: 3px; margin-bottom: 16px; overflow: hidden; }
  .progress-bar { height: 100%; background: var(--red); border-radius: 4px; transition: width 0.3s; }

  .section-label { font-size: 10px; letter-spacing: 2px; text-transform: uppercase; color: var(--red); margin: 20px 0 10px; font-weight: 600; }

  .exercise-card { background: var(--card); border: 0.5px solid var(--border); border-radius: 12px; margin-bottom: 12px; overflow: hidden; }
  .exercise-header { padding: 14px 16px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; user-select: none; }
  .exercise-header-left { flex: 1; }

  .exercise-name-wrap { display: flex; align-items: center; gap: 8px; margin-bottom: 4px; }
  .exercise-name { font-size: 14px; font-weight: 600; color: var(--text); }
  .edit-name-btn {
    font-size: 11px; color: var(--muted); background: rgba(255,255,255,0.05);
    border: 0.5px solid var(--border); border-radius: 6px; padding: 2px 7px;
    cursor: pointer; flex-shrink: 0; font-family: 'DM Sans', sans-serif;
    transition: all 0.2s;
  }
  .edit-name-btn:active { background: rgba(225,8,19,0.15); border-color: var(--red); color: var(--red); }

  .exercise-meta { display: flex; gap: 8px; flex-wrap: wrap; }
  .meta-pill { font-size: 11px; color: var(--muted); background: rgba(255,255,255,0.05); padding: 2px 8px; border-radius: 10px; }
  .meta-pill.rpe { color: var(--yellow); }
  .exercise-chevron { font-size: 18px; color: var(--muted); transition: transform 0.2s; margin-left: 12px; }
  .exercise-card.open .exercise-chevron { transform: rotate(180deg); }

  .series-container { display: none; padding: 0 16px 16px; border-top: 0.5px solid var(--border); }
  .exercise-card.open .series-container { display: block; }

  .series-header {
    display: grid; grid-template-columns: 28px 1fr 1fr 60px 60px 50px;
    gap: 6px; padding: 10px 0 6px;
    font-size: 10px; letter-spacing: 1px; text-transform: uppercase; color: var(--muted);
  }
  .serie-row { display: grid; grid-template-columns: 28px 1fr 1fr 60px 60px 50px; gap: 6px; align-items: center; margin-bottom: 8px; }
  .serie-num { font-size: 13px; font-weight: 600; color: var(--red); text-align: center; }

  .serie-input {
    background: rgba(255,255,255,0.06); border: 0.5px solid var(--border); border-radius: 8px;
    padding: 10px; font-size: 14px; color: var(--text); font-family: 'DM Sans', sans-serif;
    width: 100%; text-align: center; transition: border-color 0.2s;
  }
  .serie-input:focus { outline: none; border-color: var(--red); }
  .serie-input::placeholder { color: rgba(255,255,255,0.2); font-size: 12px; }

  .serie-status { display: flex; align-items: center; justify-content: center; }
  .check-btn {
    width: 32px; height: 32px; border-radius: 50%; border: 0.5px solid var(--border);
    background: transparent; cursor: pointer; display: flex; align-items: center;
    justify-content: center; font-size: 14px; color: var(--muted); transition: all 0.2s;
  }
  .check-btn.done { background: var(--green); border-color: var(--green); color: white; }

  .nota-label { font-size: 10px; letter-spacing: 1px; text-transform: uppercase; color: var(--muted); margin: 10px 0 6px; }
  .nota-input {
    background: rgba(255,255,255,0.04); border: 0.5px solid var(--border); border-radius: 8px;
    padding: 10px 12px; font-size: 13px; color: var(--text); font-family: 'DM Sans', sans-serif;
    width: 100%; resize: none; min-height: 60px; transition: border-color 0.2s;
  }
  .nota-input:focus { outline: none; border-color: rgba(225,8,19,0.4); }
  .nota-input::placeholder { color: rgba(255,255,255,0.2); }

  /* MODAL EDITAR NOMBRE */
  .modal-overlay {
    display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.85);
    z-index: 999; align-items: center; justify-content: center; padding: 20px;
  }
  .modal-overlay.show { display: flex; }
  .modal {
    background: #1a1a1a; border: 0.5px solid rgba(255,255,255,0.1);
    border-radius: 16px; padding: 28px 24px; width: 100%; max-width: 400px;
  }
  .modal-title { font-size: 11px; letter-spacing: 2px; text-transform: uppercase; color: var(--red); margin-bottom: 16px; }
  .modal-input {
    background: rgba(255,255,255,0.06); border: 0.5px solid rgba(255,255,255,0.15);
    border-radius: 10px; padding: 14px 16px; font-size: 15px; color: var(--text);
    font-family: 'DM Sans', sans-serif; width: 100%; margin-bottom: 16px;
  }
  .modal-input:focus { outline: none; border-color: var(--red); }
  .modal-btns { display: flex; gap: 10px; }
  .modal-ok {
    flex: 1; background: var(--red); color: white; border: none; border-radius: 10px;
    padding: 14px; font-size: 13px; font-weight: 600; letter-spacing: 1px;
    text-transform: uppercase; cursor: pointer; font-family: 'DM Sans', sans-serif;
  }
  .modal-cancel {
    background: rgba(255,255,255,0.06); color: var(--muted); border: 0.5px solid var(--border);
    border-radius: 10px; padding: 14px 18px; font-size: 13px; cursor: pointer; font-family: 'DM Sans', sans-serif;
  }

  .bottom-bar {
    position: fixed; bottom: 0; left: 0; right: 0;
    background: rgba(10,10,10,0.95); backdrop-filter: blur(20px);
    border-top: 0.5px solid var(--border); padding: 12px 20px;
    display: flex; gap: 10px; z-index: 100;
  }
  .save-btn {
    flex: 1; background: var(--red); color: white; border: none; border-radius: 10px;
    padding: 14px; font-size: 13px; font-weight: 600; letter-spacing: 1px;
    text-transform: uppercase; cursor: pointer; font-family: 'DM Sans', sans-serif;
  }
  .save-btn:active { opacity: 0.8; }
  .reset-btn {
    background: rgba(255,255,255,0.06); color: var(--muted); border: 0.5px solid var(--border);
    border-radius: 10px; padding: 14px 18px; font-size: 13px; cursor: pointer; font-family: 'DM Sans', sans-serif;
  }

  .toast {
    position: fixed; top: 20px; left: 50%; transform: translateX(-50%) translateY(-100px);
    background: var(--green); color: white; padding: 12px 24px; border-radius: 30px;
    font-size: 13px; font-weight: 600; z-index: 9999; transition: transform 0.3s;
  }
  .toast.show { transform: translateX(-50%) translateY(0); }
</style>
</head>
<body>

<div class="header">
  <div class="header-top">
    <div class="logo">GYM FACTORY</div>
    <div class="semana-badge">Semana 1</div>
  </div>
  <div class="days-scroll" id="dayTabs"></div>
</div>

<div class="content" id="dayPanels"></div>

<div class="modal-overlay" id="modalOverlay">
  <div class="modal">
    <div class="modal-title">Editar ejercicio</div>
    <input class="modal-input" id="modalInput" type="text" placeholder="Nombre del ejercicio">
    <div class="modal-btns">
      <button class="modal-cancel" onclick="closeModal()">Cancelar</button>
      <button class="modal-ok" onclick="confirmEdit()">Guardar</button>
    </div>
  </div>
</div>

<div class="bottom-bar">
  <button class="reset-btn" onclick="resetDay()">↺</button>
  <button class="save-btn" onclick="saveData()">Guardar sesión</button>
</div>

<div class="toast" id="toast">✓ Guardado</div>

<script>
const DAYS = [
  {
    id: 'empuje', name: 'Empuje', sub: 'Pecho · Hombro · Tríceps',
    sections: [
      { label: 'Trabajo inicial', exercises: [
        { name: 'Press de banco grip cerrado', rp: 8, rpe: 8, series: 3, pausa: '2.5', rir: 3 },
        { name: 'Press con mancuernas inclinado', rp: 10, rpe: 8, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Extensión de tríceps unilateral', rp: 10, rpe: 7, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Extensión tras nuca bilateral', rp: 12, rpe: 6, series: 3, pausa: '2.5', rir: 3 },
        { name: 'Press francés', rp: 12, rpe: 6, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Elevación frontal con mancuernas', rp: 8, rpe: 8, series: 3, pausa: '2.5', rir: 2 },
      ]},
      { label: 'Vuelta a la calma', exercises: [
        { name: 'Ejercicio cardiovascular', rp: 20, rpe: 4, series: 1, pausa: '-', rir: '-' },
      ]}
    ]
  },
  {
    id: 'traccion', name: 'Tracción', sub: 'Espalda · Bíceps',
    sections: [
      { label: 'Trabajo inicial', exercises: [
        { name: 'Jalón alto cerrado', rp: 10, rpe: 8, series: 2, pausa: '2.5', rir: 2 },
        { name: 'Dominada de potencia y bajada controlada', rp: 8, rpe: 8, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Remo en polea agarre cerrado', rp: 12, rpe: 7, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Posterior en máquina', rp: 12, rpe: 7, series: 3, pausa: '2.5', rir: 1 },
        { name: 'Curl con banco inclinado', rp: 10, rpe: 7, series: 4, pausa: '2.5', rir: 2 },
        { name: 'Curl libre con barra Z', rp: 8, rpe: 7, series: 4, pausa: '2.5', rir: 2 },
      ]},
      { label: 'Vuelta a la calma', exercises: [
        { name: 'Ejercicio cardiovascular', rp: 20, rpe: 4, series: 1, pausa: '-', rir: '-' },
      ]}
    ]
  },
  {
    id: 'piernas', name: 'Piernas', sub: 'Cuádriceps · Isquios · Glúteos',
    sections: [
      { label: 'Trabajo inicial', exercises: [
        { name: 'Extensión de cuádriceps lentas', rp: 10, rpe: 8, series: 3, pausa: '2.5', rir: 1 },
        { name: 'Sentadilla en Smith', rp: 8, rpe: 8, series: 2, pausa: '2.5', rir: 3 },
        { name: 'Peso muerto rumano en 2 tiempos', rp: 8, rpe: 7, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Hip Thrust en máquina', rp: 12, rpe: 7, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Aductor en máquina', rp: 12, rpe: 7, series: 4, pausa: '2.5', rir: '-' },
        { name: 'Curl femoral acostado', rp: 10, rpe: 8, series: 4, pausa: '2.5', rir: '-' },
      ]},
      { label: 'Vuelta a la calma', exercises: [
        { name: 'Ejercicio cardiovascular', rp: 20, rpe: 4, series: 1, pausa: '-', rir: '-' },
      ]}
    ]
  },
  {
    id: 'brazos', name: 'Brazos', sub: 'Hipertrofia Bíceps · Tríceps · Hombros',
    sections: [
      { label: 'Trabajo inicial', exercises: [
        { name: 'Laterales en polea', rp: 15, rpe: 8, series: 3, pausa: '2.5', rir: 1 },
        { name: 'Patada de posterior', rp: 12, rpe: 8, series: 3, pausa: '2.5', rir: 1 },
        { name: 'Curl en banco Scott', rp: 8, rpe: 8, series: 3, pausa: '2.5', rir: 1 },
        { name: 'Martillo con banco inclinado', rp: 15, rpe: 8, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Extensión de tríceps en polea', rp: 10, rpe: 8, series: 3, pausa: '2.5', rir: 1 },
        { name: 'Extensión tras nuca en polea', rp: 10, rpe: 8, series: 3, pausa: '2.5', rir: 1 },
      ]},
      { label: 'Vuelta a la calma', exercises: [
        { name: 'Ejercicio cardiovascular', rp: 20, rpe: 4, series: 1, pausa: '-', rir: '-' },
      ]}
    ]
  },
  {
    id: 'acc-piernas', name: 'Acc. Piernas', sub: 'Accesorios · Gemelos · Glúteos',
    sections: [
      { label: 'Trabajo inicial', exercises: [
        { name: 'Gemelos en máquina', rp: 15, rpe: 8, series: 2, pausa: '2.5', rir: '-' },
        { name: 'Sentadilla sumo en máquina', rp: 10, rpe: 6, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Buenos días', rp: 12, rpe: 7, series: 3, pausa: '2.5', rir: 2 },
        { name: 'Estocadas en movimiento', rp: 10, rpe: 7, series: 3, pausa: '2.5', rir: 1 },
        { name: 'Hack Squat', rp: 8, rpe: 8, series: 4, pausa: '2.5', rir: 1 },
      ]},
      { label: 'Vuelta a la calma', exercises: [
        { name: 'Ejercicio cardiovascular', rp: 20, rpe: 4, series: 1, pausa: '-', rir: '-' },
      ]}
    ]
  },
  {
    id: 'core', name: 'Core', sub: 'Lumbar · Abdomen · Hombros',
    sections: [
      { label: 'Trabajo inicial', exercises: [
        { name: 'Hiperextensiones lumbares', rp: 15, rpe: 6, series: 3, pausa: '-', rir: '-' },
        { name: 'Press militar parado con barra', rp: 10, rpe: 7, series: 3, pausa: '-', rir: '-' },
        { name: 'Pallof press con banda', rp: 15, rpe: 6, series: 3, pausa: '-', rir: '-' },
        { name: 'Extensiones de tríceps con banda elástica', rp: 15, rpe: 6, series: 3, pausa: '-', rir: '-' },
        { name: 'L-sit', rp: 12, rpe: 6, series: 3, pausa: '-', rir: '-' },
      ]},
      { label: 'Vuelta a la calma', exercises: [
        { name: 'Ejercicio cardiovascular', rp: 20, rpe: 4, series: 1, pausa: '-', rir: '-' },
      ]}
    ]
  }
];

let activeDay = 0;
let data = JSON.parse(localStorage.getItem('talo-tracker-v2') || '{}');
let editingKey = null;

function buildUI() {
  const tabsEl = document.getElementById('dayTabs');
  const panelsEl = document.getElementById('dayPanels');
  tabsEl.innerHTML = '';
  panelsEl.innerHTML = '';

  DAYS.forEach((day, di) => {
    const tab = document.createElement('button');
    tab.className = 'day-tab' + (di === activeDay ? ' active' : '');
    tab.textContent = day.name;
    tab.onclick = () => { activeDay = di; buildUI(); };
    tabsEl.appendChild(tab);

    const panel = document.createElement('div');
    panel.className = 'day-panel' + (di === activeDay ? ' active' : '');

    const totalSeries = day.sections.reduce((acc, s) => acc + s.exercises.reduce((a, e) => a + e.series, 0), 0);
    const doneSeries = countDone(day.id, day.sections);
    const pct = totalSeries > 0 ? Math.round((doneSeries / totalSeries) * 100) : 0;

    let html = `<div class="day-title">${day.name}</div>
                <div class="day-subtitle">${day.sub}</div>
                <div class="progress-wrap"><div class="progress-bar" style="width:${pct}%"></div></div>`;

    day.sections.forEach((section, si) => {
      html += `<div class="section-label">${section.label}</div>`;
      section.exercises.forEach((ex, ei) => {
        const key = `${day.id}-${si}-${ei}`;
        const saved = data[key] || {};
        const isOpen = saved.open || false;
        const customName = saved.customName || ex.name;
        html += buildExCard(key, ex, customName, isOpen);
      });
    });

    panel.innerHTML = html;
    panelsEl.appendChild(panel);
  });

  attachEvents();
}

function buildExCard(key, ex, customName, isOpen) {
  const saved = data[key] || {};
  const seriesArr = saved.series || {};
  const nota = saved.nota || '';
  const openClass = isOpen ? ' open' : '';

  let seriesRows = '';
  for (let i = 0; i < ex.series; i++) {
    const s = seriesArr[i] || {};
    const doneClass = s.done ? ' done' : '';
    const doneIcon = s.done ? '✓' : '';
    seriesRows += `
      <div class="serie-row">
        <div class="serie-num">${i+1}</div>
        <input class="serie-input" type="number" placeholder="${ex.rp}" value="${s.reps || ''}" inputmode="numeric"
          onchange="saveField('${key}','series',${i},'reps',this.value)">
        <input class="serie-input" type="number" placeholder="kg" value="${s.carga || ''}" inputmode="decimal"
          onchange="saveField('${key}','series',${i},'carga',this.value)">
        <input class="serie-input" type="number" placeholder="${ex.rpe}" value="${s.rpe || ''}" inputmode="decimal" style="font-size:12px"
          onchange="saveField('${key}','series',${i},'rpe',this.value)">
        <input class="serie-input" type="number" placeholder="${ex.rir}" value="${s.rir || ''}" inputmode="numeric" style="font-size:12px"
          onchange="saveField('${key}','series',${i},'rir',this.value)">
        <div class="serie-status">
          <button class="check-btn${doneClass}" data-key="${key}" data-idx="${i}">${doneIcon}</button>
        </div>
      </div>`;
  }

  return `
    <div class="exercise-card${openClass}" data-key="${key}">
      <div class="exercise-header">
        <div class="exercise-header-left">
          <div class="exercise-name-wrap">
            <div class="exercise-name">${customName}</div>
            <button class="edit-name-btn" data-key="${key}" data-name="${customName}" onclick="openModal(event, '${key}', this.dataset.name)">✎ editar</button>
          </div>
          <div class="exercise-meta">
            <span class="meta-pill">${ex.series} × ${ex.rp} reps</span>
            <span class="meta-pill rpe">RPE ${ex.rpe}</span>
            <span class="meta-pill">RIR ${ex.rir}</span>
            <span class="meta-pill">Pausa ${ex.pausa}′</span>
          </div>
        </div>
        <div class="exercise-chevron">⌄</div>
      </div>
      <div class="series-container">
        <div class="series-header">
          <div>#</div><div>Reps</div><div>Carga</div><div>RPE real</div><div>RIR real</div><div>✓</div>
        </div>
        ${seriesRows}
        <div class="nota-label">Notas</div>
        <textarea class="nota-input" placeholder="Sensaciones, ajustes, PR..."
          onchange="saveNota('${key}', this.value)">${nota}</textarea>
      </div>
    </div>`;
}

function attachEvents() {
  document.querySelectorAll('.exercise-header').forEach(h => {
    h.onclick = function(e) {
      if (e.target.classList.contains('edit-name-btn')) return;
      const card = this.closest('.exercise-card');
      const key = card.dataset.key;
      card.classList.toggle('open');
      if (!data[key]) data[key] = {};
      data[key].open = card.classList.contains('open');
    };
  });

  document.querySelectorAll('.check-btn').forEach(btn => {
    btn.onclick = function() {
      this.classList.toggle('done');
      this.textContent = this.classList.contains('done') ? '✓' : '';
      const key = this.dataset.key;
      const idx = this.dataset.idx;
      if (!data[key]) data[key] = {};
      if (!data[key].series) data[key].series = {};
      if (!data[key].series[idx]) data[key].series[idx] = {};
      data[key].series[idx].done = this.classList.contains('done');
      updateProgress();
    };
  });
}

function openModal(e, key, currentName) {
  e.stopPropagation();
  editingKey = key;
  document.getElementById('modalInput').value = currentName;
  document.getElementById('modalOverlay').classList.add('show');
  setTimeout(() => document.getElementById('modalInput').focus(), 100);
}

function closeModal() {
  document.getElementById('modalOverlay').classList.remove('show');
  editingKey = null;
}

function confirmEdit() {
  const newName = document.getElementById('modalInput').value.trim();
  if (!newName || !editingKey) { closeModal(); return; }
  if (!data[editingKey]) data[editingKey] = {};
  data[editingKey].customName = newName;
  closeModal();
  saveData(true);
  buildUI();
}

document.getElementById('modalOverlay').addEventListener('click', function(e) {
  if (e.target === this) closeModal();
});

document.getElementById('modalInput').addEventListener('keydown', function(e) {
  if (e.key === 'Enter') confirmEdit();
  if (e.key === 'Escape') closeModal();
});

function saveField(key, section, idx, field, value) {
  if (!data[key]) data[key] = {};
  if (!data[key][section]) data[key][section] = {};
  if (!data[key][section][idx]) data[key][section][idx] = {};
  data[key][section][idx][field] = value;
}

function saveNota(key, value) {
  if (!data[key]) data[key] = {};
  data[key].nota = value;
}

function countDone(dayId, sections) {
  let count = 0;
  sections.forEach((s, si) => {
    s.exercises.forEach((ex, ei) => {
      const key = `${dayId}-${si}-${ei}`;
      const saved = data[key] || {};
      const series = saved.series || {};
      for (let i = 0; i < ex.series; i++) {
        if (series[i] && series[i].done) count++;
      }
    });
  });
  return count;
}

function updateProgress() {
  const day = DAYS[activeDay];
  const totalSeries = day.sections.reduce((acc, s) => acc + s.exercises.reduce((a, e) => a + e.series, 0), 0);
  const doneSeries = countDone(day.id, day.sections);
  const pct = totalSeries > 0 ? Math.round((doneSeries / totalSeries) * 100) : 0;
  const bar = document.querySelector('.progress-bar');
  if (bar) bar.style.width = pct + '%';
}

function saveData(silent) {
  localStorage.setItem('talo-tracker-v2', JSON.stringify(data));
  if (!silent) {
    const toast = document.getElementById('toast');
    toast.classList.add('show');
    setTimeout(() => toast.classList.remove('show'), 2000);
  }
}

function resetDay() {
  if (!confirm('¿Borrar datos del día actual?')) return;
  const day = DAYS[activeDay];
  day.sections.forEach((s, si) => {
    s.exercises.forEach((ex, ei) => {
      const key = `${day.id}-${si}-${ei}`;
      if (data[key]) { data[key].series = {}; data[key].nota = ''; }
    });
  });
  saveData(true);
  buildUI();
}

buildUI();
</script>
</body>
</html>
