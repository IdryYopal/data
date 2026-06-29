[dashboard_analitica_avanzada.html](https://github.com/user-attachments/files/29484507/dashboard_analitica_avanzada.html)
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Observatorio Agrícola — Analítica Avanzada</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;600&family=IBM+Plex+Sans:wght@300;400;500;600&display=swap');

  :root {
    --bg: #0d1117;
    --surface: #161b22;
    --surface2: #1c2230;
    --border: #21262d;
    --green: #3fb950;
    --green-dim: #1a4a26;
    --yellow: #d29922;
    --red: #f85149;
    --blue: #58a6ff;
    --purple: #bc8cff;
    --text: #e6edf3;
    --text-muted: #7d8590;
    --text-dim: #484f58;
    --mono: 'IBM Plex Mono', monospace;
    --sans: 'IBM Plex Sans', sans-serif;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    font-size: 14px;
    line-height: 1.6;
    min-height: 100vh;
  }

  /* HEADER */
  .header {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    padding: 18px 32px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: sticky;
    top: 0;
    z-index: 100;
  }
  .header-left { display: flex; align-items: center; gap: 14px; }
  .header-badge {
    background: var(--green-dim);
    border: 1px solid var(--green);
    color: var(--green);
    font-family: var(--mono);
    font-size: 10px;
    padding: 3px 8px;
    border-radius: 4px;
    letter-spacing: 0.08em;
  }
  .header h1 {
    font-size: 15px;
    font-weight: 600;
    color: var(--text);
    letter-spacing: -0.01em;
  }
  .header-sub {
    font-size: 11px;
    color: var(--text-muted);
    font-family: var(--mono);
  }
  .status-pill {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 12px;
    color: var(--text-muted);
    font-family: var(--mono);
  }
  .status-dot {
    width: 7px; height: 7px;
    border-radius: 50%;
    background: var(--text-dim);
  }
  .status-dot.live { background: var(--green); box-shadow: 0 0 6px var(--green); animation: pulse 2s infinite; }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }

  /* NAV TABS */
  .nav-tabs {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    display: flex;
    padding: 0 32px;
    gap: 0;
  }
  .tab-btn {
    background: none;
    border: none;
    border-bottom: 2px solid transparent;
    color: var(--text-muted);
    cursor: pointer;
    font-family: var(--sans);
    font-size: 13px;
    font-weight: 500;
    padding: 12px 20px;
    transition: all 0.2s;
    white-space: nowrap;
  }
  .tab-btn:hover { color: var(--text); }
  .tab-btn.active { color: var(--blue); border-bottom-color: var(--blue); }

  /* MAIN LAYOUT */
  .main { padding: 28px 32px; max-width: 1400px; margin: 0 auto; }

  /* LOADING */
  .loading-screen {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 60vh;
    gap: 20px;
  }
  .loading-bar-wrap { width: 320px; height: 3px; background: var(--border); border-radius: 2px; overflow: hidden; }
  .loading-bar { height: 100%; background: var(--green); border-radius: 2px; transition: width 0.4s; width: 0%; }
  .loading-label { font-family: var(--mono); font-size: 12px; color: var(--text-muted); }

  /* TAB PANELS */
  .tab-panel { display: none; }
  .tab-panel.active { display: block; }

  /* KPI ROW */
  .kpi-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 28px; }
  .kpi-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 20px;
    position: relative;
    overflow: hidden;
  }
  .kpi-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
    background: var(--accent, var(--blue));
  }
  .kpi-label { font-size: 11px; color: var(--text-muted); letter-spacing: 0.06em; text-transform: uppercase; margin-bottom: 8px; }
  .kpi-value { font-family: var(--mono); font-size: 26px; font-weight: 600; color: var(--text); line-height: 1; }
  .kpi-sub { font-size: 11px; color: var(--text-muted); margin-top: 6px; }
  .kpi-change { font-family: var(--mono); font-size: 11px; font-weight: 600; }
  .kpi-change.up { color: var(--green); }
  .kpi-change.down { color: var(--red); }

  /* GRID */
  .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 20px; }
  .grid-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 20px; margin-bottom: 20px; }
  .span-2 { grid-column: span 2; }

  /* CARDS */
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 20px;
  }
  .card-title {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-muted);
    letter-spacing: 0.07em;
    text-transform: uppercase;
    margin-bottom: 16px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .card-title span { color: var(--text); font-size: 13px; font-weight: 600; text-transform: none; letter-spacing: 0; }
  .badge {
    font-family: var(--mono);
    font-size: 10px;
    padding: 2px 7px;
    border-radius: 3px;
    font-weight: 600;
  }
  .badge-blue { background: rgba(88,166,255,0.15); color: var(--blue); }
  .badge-yellow { background: rgba(210,153,34,0.15); color: var(--yellow); }
  .badge-red { background: rgba(248,81,73,0.15); color: var(--red); }
  .badge-green { background: rgba(63,185,80,0.15); color: var(--green); }
  .badge-purple { background: rgba(188,140,255,0.15); color: var(--purple); }

  canvas { max-height: 280px; }

  /* ANOMALY TABLE */
  .anomaly-table { width: 100%; border-collapse: collapse; font-size: 12px; }
  .anomaly-table th {
    color: var(--text-muted);
    font-weight: 500;
    text-align: left;
    padding: 8px 12px;
    border-bottom: 1px solid var(--border);
    font-family: var(--mono);
    font-size: 11px;
    letter-spacing: 0.04em;
  }
  .anomaly-table td {
    padding: 9px 12px;
    border-bottom: 1px solid var(--border);
    color: var(--text);
  }
  .anomaly-table tr:hover td { background: var(--surface2); }
  .severity-high { color: var(--red); font-weight: 600; font-family: var(--mono); }
  .severity-mid { color: var(--yellow); font-weight: 600; font-family: var(--mono); }
  .severity-low { color: var(--green); font-weight: 600; font-family: var(--mono); }

  /* SIMULATOR */
  .sim-controls { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 20px; }
  .sim-group label {
    display: block;
    font-size: 11px;
    color: var(--text-muted);
    margin-bottom: 8px;
    font-family: var(--mono);
    letter-spacing: 0.04em;
  }
  .sim-group input[type=range] {
    width: 100%;
    accent-color: var(--blue);
    cursor: pointer;
  }
  .sim-value {
    font-family: var(--mono);
    font-size: 13px;
    color: var(--blue);
    font-weight: 600;
    display: inline-block;
    min-width: 50px;
  }
  .sim-result {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 16px;
    font-family: var(--mono);
    font-size: 12px;
    line-height: 1.8;
  }
  .sim-result .highlight { color: var(--green); font-weight: 600; }
  .sim-result .warn { color: var(--yellow); font-weight: 600; }

  /* RECOMMEND */
  .rec-list { display: flex; flex-direction: column; gap: 12px; }
  .rec-item {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-left: 3px solid var(--accent, var(--blue));
    border-radius: 6px;
    padding: 14px 16px;
  }
  .rec-item-top { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 6px; }
  .rec-cultivo { font-weight: 600; font-size: 13px; }
  .rec-score { font-family: var(--mono); font-size: 12px; }
  .rec-text { font-size: 12px; color: var(--text-muted); line-height: 1.5; }

  /* CHAT */
  .chat-wrap {
    display: flex;
    flex-direction: column;
    height: 520px;
  }
  .chat-messages {
    flex: 1;
    overflow-y: auto;
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 14px;
    scrollbar-width: thin;
    scrollbar-color: var(--border) transparent;
  }
  .chat-bubble {
    max-width: 85%;
    padding: 12px 16px;
    border-radius: 8px;
    font-size: 13px;
    line-height: 1.6;
  }
  .chat-bubble.user {
    background: rgba(88,166,255,0.12);
    border: 1px solid rgba(88,166,255,0.25);
    align-self: flex-end;
    color: var(--text);
  }
  .chat-bubble.ai {
    background: var(--surface2);
    border: 1px solid var(--border);
    align-self: flex-start;
    color: var(--text);
  }
  .chat-bubble.ai .ai-label {
    font-family: var(--mono);
    font-size: 10px;
    color: var(--green);
    margin-bottom: 6px;
    letter-spacing: 0.06em;
  }
  .chat-input-row {
    border-top: 1px solid var(--border);
    padding: 14px 16px;
    display: flex;
    gap: 10px;
  }
  .chat-input {
    flex: 1;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 6px;
    color: var(--text);
    font-family: var(--sans);
    font-size: 13px;
    padding: 10px 14px;
    outline: none;
    transition: border-color 0.2s;
  }
  .chat-input:focus { border-color: var(--blue); }
  .chat-send {
    background: var(--blue);
    border: none;
    border-radius: 6px;
    color: #000;
    cursor: pointer;
    font-family: var(--sans);
    font-size: 13px;
    font-weight: 600;
    padding: 10px 20px;
    transition: opacity 0.2s;
  }
  .chat-send:hover { opacity: 0.85; }
  .chat-send:disabled { opacity: 0.4; cursor: not-allowed; }

  .typing-dots span {
    display: inline-block;
    width: 6px; height: 6px;
    background: var(--text-muted);
    border-radius: 50%;
    margin: 0 2px;
    animation: bounce 1.2s infinite;
  }
  .typing-dots span:nth-child(2) { animation-delay: 0.2s; }
  .typing-dots span:nth-child(3) { animation-delay: 0.4s; }
  @keyframes bounce { 0%,80%,100%{transform:translateY(0)} 40%{transform:translateY(-6px)} }

  .quick-questions { display: flex; flex-wrap: wrap; gap: 8px; padding: 0 16px 12px; }
  .quick-q {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 16px;
    color: var(--text-muted);
    cursor: pointer;
    font-size: 11px;
    padding: 5px 12px;
    transition: all 0.2s;
  }
  .quick-q:hover { border-color: var(--blue); color: var(--blue); }

  /* REPORT */
  .report-btn {
    background: var(--green-dim);
    border: 1px solid var(--green);
    border-radius: 6px;
    color: var(--green);
    cursor: pointer;
    font-family: var(--sans);
    font-size: 13px;
    font-weight: 600;
    padding: 10px 20px;
    transition: all 0.2s;
    margin-bottom: 16px;
  }
  .report-btn:hover { background: var(--green); color: #000; }
  .report-btn:disabled { opacity: 0.4; cursor: not-allowed; }
  .report-output {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 20px;
    font-size: 13px;
    line-height: 1.8;
    white-space: pre-wrap;
    min-height: 200px;
    color: var(--text);
  }
  .report-output .section-head { color: var(--blue); font-weight: 600; }

  /* PREDICTION TABLE */
  .pred-table { width: 100%; border-collapse: collapse; font-size: 12px; }
  .pred-table th {
    background: var(--surface2);
    color: var(--text-muted);
    font-family: var(--mono);
    font-size: 11px;
    font-weight: 500;
    padding: 10px 14px;
    text-align: left;
    letter-spacing: 0.04em;
  }
  .pred-table td {
    padding: 10px 14px;
    border-bottom: 1px solid var(--border);
    font-family: var(--mono);
  }
  .pred-table tr:hover td { background: var(--surface2); }
  .trend-up { color: var(--green); }
  .trend-down { color: var(--red); }
  .trend-flat { color: var(--yellow); }

  .empty-state {
    text-align: center;
    padding: 40px;
    color: var(--text-muted);
    font-size: 13px;
  }

  @media (max-width: 900px) {
    .kpi-row { grid-template-columns: repeat(2, 1fr); }
    .grid-2, .grid-3 { grid-template-columns: 1fr; }
    .span-2 { grid-column: span 1; }
    .main { padding: 16px; }
  }
</style>
</head>
<body>

<div class="header">
  <div class="header-left">
    <div>
      <div style="display:flex;align-items:center;gap:10px;">
        <h1>Observatorio Agrícola — Analítica Avanzada</h1>
        <div class="header-badge">IA</div>
      </div>
      <div class="header-sub">datos.gov.co · Medellín, Antioquia · 2019–2024</div>
    </div>
  </div>
  <div class="status-pill">
    <div class="status-dot" id="statusDot"></div>
    <span id="statusLabel">Cargando datos…</span>
  </div>
</div>

<div class="nav-tabs">
  <button class="tab-btn active" onclick="switchTab('resumen')">Resumen</button>
  <button class="tab-btn" onclick="switchTab('prediccion')">Predicción 2025</button>
  <button class="tab-btn" onclick="switchTab('anomalias')">Anomalías</button>
  <button class="tab-btn" onclick="switchTab('simulador')">Simulador</button>
  <button class="tab-btn" onclick="switchTab('recomendaciones')">Recomendaciones</button>
  <button class="tab-btn" onclick="switchTab('asistente')">Asistente IA</button>
  <button class="tab-btn" onclick="switchTab('reporte')">Reporte Automático</button>
</div>

<div class="main">

  <!-- LOADING -->
  <div class="loading-screen" id="loadingScreen">
    <div style="font-family:var(--mono);color:var(--green);font-size:13px;">Cargando 10,000 registros…</div>
    <div class="loading-bar-wrap"><div class="loading-bar" id="loadingBar"></div></div>
    <div class="loading-label" id="loadingMsg">Conectando a datos.gov.co</div>
  </div>

  <!-- RESUMEN -->
  <div class="tab-panel" id="tab-resumen">
    <div class="kpi-row" id="kpiRow"></div>
    <div class="grid-2">
      <div class="card">
        <div class="card-title">Producción total por año <span class="badge badge-blue">ton</span></div>
        <canvas id="chartProd"></canvas>
      </div>
      <div class="card">
        <div class="card-title">Top 10 cultivos por producción acumulada <span class="badge badge-green">2019–2024</span></div>
        <canvas id="chartTop"></canvas>
      </div>
    </div>
    <div class="grid-2">
      <div class="card">
        <div class="card-title">Área sembrada vs. cosechada <span class="badge badge-yellow">ha/año</span></div>
        <canvas id="chartArea"></canvas>
      </div>
      <div class="card">
        <div class="card-title">Distribución por grupo de cultivo <span class="badge badge-purple">%</span></div>
        <canvas id="chartGrupo"></canvas>
      </div>
    </div>
  </div>

  <!-- PREDICCIÓN -->
  <div class="tab-panel" id="tab-prediccion">
    <div class="card" style="margin-bottom:20px;">
      <div class="card-title"><span>Proyección 2025</span> <span class="badge badge-blue">Regresión Lineal</span></div>
      <p style="font-size:12px;color:var(--text-muted);margin-bottom:16px;">Modelo de regresión lineal simple aplicado a la serie histórica 2019–2024 de cada cultivo. El R² indica qué tan bien se ajusta la tendencia.</p>
      <table class="pred-table" id="predTable">
        <thead><tr><th>Cultivo</th><th>Prod. 2024 (ton)</th><th>Proyección 2025 (ton)</th><th>Variación</th><th>R²</th><th>Tendencia</th></tr></thead>
        <tbody id="predBody"></tbody>
      </table>
    </div>
    <div class="card">
      <div class="card-title"><span>Proyección visual: Top 5 cultivos</span></div>
      <canvas id="chartPred"></canvas>
    </div>
  </div>

  <!-- ANOMALÍAS -->
  <div class="tab-panel" id="tab-anomalias">
    <div class="card" style="margin-bottom:20px;">
      <div class="card-title"><span>Detección de Anomalías</span> <span class="badge badge-red">Z-Score + IQR</span></div>
      <p style="font-size:12px;color:var(--text-muted);margin-bottom:16px;">Se identifican registros donde la producción, el rendimiento o la relación área-sembrada/cosechada se desvían significativamente del comportamiento histórico del cultivo. Severidad ALTA: |Z|>2.5. MEDIA: |Z|>1.8.</p>
      <table class="anomaly-table" id="anomalyTable">
        <thead><tr><th>Año</th><th>Cultivo</th><th>Variable</th><th>Valor</th><th>Media hist.</th><th>Z-Score</th><th>Severidad</th><th>Interpretación</th></tr></thead>
        <tbody id="anomalyBody"></tbody>
      </table>
    </div>
  </div>

  <!-- SIMULADOR -->
  <div class="tab-panel" id="tab-simulador">
    <div class="grid-2">
      <div class="card">
        <div class="card-title"><span>Simulador de Escenarios</span> <span class="badge badge-yellow">Supuestos ajustables</span></div>
        <p style="font-size:12px;color:var(--text-muted);margin-bottom:20px;">Ajusta los supuestos y observa el impacto proyectado sobre la producción total del municipio para 2025–2026.</p>

        <div class="sim-controls">
          <div class="sim-group">
            <label>EXPANSIÓN DE ÁREA SEMBRADA</label>
            <input type="range" id="sArea" min="-30" max="50" value="10" oninput="updateSim()">
            <div><span class="sim-value" id="vArea">+10%</span></div>
          </div>
          <div class="sim-group">
            <label>FACTOR CLIMÁTICO (rendimiento)</label>
            <input type="range" id="sClima" min="-40" max="20" value="0" oninput="updateSim()">
            <div><span class="sim-value" id="vClima">0%</span></div>
          </div>
          <div class="sim-group">
            <label>ADOPCIÓN TECNOLÓGICA</label>
            <input type="range" id="sTec" min="0" max="30" value="5" oninput="updateSim()">
            <div><span class="sim-value" id="vTec">+5%</span></div>
          </div>
          <div class="sim-group">
            <label>PÉRDIDA POST-COSECHA</label>
            <input type="range" id="sPerdida" min="0" max="40" value="15" oninput="updateSim()">
            <div><span class="sim-value" id="vPerdida">15%</span></div>
          </div>
        </div>

        <div class="sim-result" id="simResult">Cargando simulación…</div>
      </div>
      <div class="card">
        <div class="card-title"><span>Escenarios comparativos</span></div>
        <canvas id="chartSim"></canvas>
      </div>
    </div>
  </div>

  <!-- RECOMENDACIONES -->
  <div class="tab-panel" id="tab-recomendaciones">
    <div class="card">
      <div class="card-title"><span>Sistema de Recomendación</span> <span class="badge badge-purple">Score compuesto</span></div>
      <p style="font-size:12px;color:var(--text-muted);margin-bottom:16px;">Cultivos rankeados por un score que combina: crecimiento de producción (40%), eficiencia cosecha/siembra (30%) y mejora de rendimiento (30%). Mayor score = mayor potencial de inversión.</p>
      <div class="rec-list" id="recList"></div>
    </div>
  </div>

  <!-- ASISTENTE -->
  <div class="tab-panel" id="tab-asistente">
    <div class="card" style="padding:0;">
      <div style="padding:16px 20px;border-bottom:1px solid var(--border);">
        <div class="card-title" style="margin:0;"><span>Asistente IA Agrícola</span> <span class="badge badge-green">claude-sonnet-4-6</span></div>
        <p style="font-size:11px;color:var(--text-muted);margin-top:4px;">Pregúntame sobre los datos, tendencias, cultivos o políticas agrícolas de Medellín.</p>
      </div>
      <div class="quick-questions">
        <div class="quick-q" onclick="sendQuick('¿Cuál es el cultivo con mayor crecimiento en los últimos 3 años?')">Mayor crecimiento</div>
        <div class="quick-q" onclick="sendQuick('¿Qué anomalías son más preocupantes?')">Anomalías críticas</div>
        <div class="quick-q" onclick="sendQuick('¿Qué cultivos recomiendas potenciar?')">Recomendaciones</div>
        <div class="quick-q" onclick="sendQuick('¿Cómo ha evolucionado el café en Medellín?')">Evolución del café</div>
        <div class="quick-q" onclick="sendQuick('¿Cuál es la proyección para 2025?')">Proyección 2025</div>
      </div>
      <div class="chat-wrap">
        <div class="chat-messages" id="chatMessages">
          <div class="chat-bubble ai">
            <div class="ai-label">ASISTENTE IA · OBSERVATORIO AGRÍCOLA</div>
            Hola. Soy el asistente de análisis agrícola de Medellín. Tengo acceso a los datos de producción 2019–2024 del municipio. Puedo analizar tendencias, explicar anomalías, interpretar proyecciones y responder preguntas sobre política agrícola. ¿En qué te puedo ayudar?
          </div>
        </div>
        <div class="chat-input-row">
          <input class="chat-input" id="chatInput" placeholder="Escribe tu pregunta sobre los datos agrícolas…" onkeydown="if(event.key==='Enter')sendChat()">
          <button class="chat-send" id="chatSend" onclick="sendChat()">Enviar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- REPORTE -->
  <div class="tab-panel" id="tab-reporte">
    <div class="card">
      <div class="card-title"><span>Generador de Reporte Automático</span> <span class="badge badge-blue">IA Generativa</span></div>
      <p style="font-size:12px;color:var(--text-muted);margin-bottom:16px;">Genera un reporte ejecutivo institucional basado en los datos reales cargados. El reporte incluye análisis de tendencias, anomalías detectadas y recomendaciones de política pública.</p>
      <button class="report-btn" id="reportBtn" onclick="generateReport()">⬡ Generar Reporte Ejecutivo</button>
      <div class="report-output" id="reportOutput">El reporte aparecerá aquí una vez generado…</div>
    </div>
  </div>

</div>

<script>
// ============================================================
// ESTADO GLOBAL
// ============================================================
let allData = [];
let dataReady = false;
let simChart = null;
let predChart = null;

// ============================================================
// CARGA DE DATOS
// ============================================================
async function loadData() {
  const bar = document.getElementById('loadingBar');
  const msg = document.getElementById('loadingMsg');
  const dot = document.getElementById('statusDot');
  const lbl = document.getElementById('statusLabel');

  const urls = [
    `https://www.datos.gov.co/resource/uejq-wxrr.json?$limit=5000&$offset=0`,
    `https://www.datos.gov.co/resource/uejq-wxrr.json?$limit=5000&$offset=5000`
  ];

  try {
    msg.textContent = 'Descargando lote 1 de 2…';
    bar.style.width = '20%';
    const r1 = await fetch(urls[0]);
    const d1 = await r1.json();
    bar.style.width = '50%';
    msg.textContent = 'Descargando lote 2 de 2…';
    const r2 = await fetch(urls[1]);
    const d2 = await r2.json();
    bar.style.width = '80%';
    msg.textContent = 'Procesando datos…';
    allData = [...d1, ...d2].map(r => ({
      dpto: r.departamento || '',
      mpio: r.municipio || '',
      grupo: r.grupo_cultivo || '',
      subgrupo: r.subgrupo || '',
      cultivo: r.cultivo || '',
      desag: r.desagregaci_n_cultivo || '',
      year: parseInt(r.a_o) || 0,
      periodo: r.periodo || '',
      sembrada: parseFloat(r.rea_sembrada) || 0,
      cosechada: parseFloat(r.rea_cosechada) || 0,
      produccion: parseFloat(r.producci_n) || 0,
      rendimiento: parseFloat(r.rendimiento) || 0,
      ciclo: r.ciclo_del_cultivo || '',
      estado: r.estado_f_sico_del_cultivo || ''
    }));
    bar.style.width = '100%';
    msg.textContent = `${allData.length.toLocaleString()} registros cargados`;
    dot.classList.add('live');
    lbl.textContent = `${allData.length.toLocaleString()} registros · en vivo`;
    setTimeout(() => {
      document.getElementById('loadingScreen').style.display = 'none';
      document.getElementById('tab-resumen').classList.add('active');
      dataReady = true;
      buildResumen();
      buildPrediccion();
      buildAnomalias();
      buildRecomendaciones();
    }, 600);
  } catch(e) {
    msg.textContent = 'Error cargando datos. Verifica conexión.';
    dot.style.background = 'var(--red)';
  }
}

// ============================================================
// HELPERS
// ============================================================
function groupBy(arr, key) {
  return arr.reduce((acc, r) => {
    const k = r[key];
    if (!acc[k]) acc[k] = [];
    acc[k].push(r);
    return acc;
  }, {});
}

function sum(arr, key) { return arr.reduce((s, r) => s + (r[key] || 0), 0); }
function avg(arr, key) { return arr.length ? sum(arr, key) / arr.length : 0; }

function linReg(xs, ys) {
  const n = xs.length;
  if (n < 2) return { m: 0, b: ys[0] || 0, r2: 0 };
  const mx = xs.reduce((a,b) => a+b,0)/n;
  const my = ys.reduce((a,b) => a+b,0)/n;
  let num = 0, den = 0, sstot = 0, ssres = 0;
  for (let i = 0; i < n; i++) {
    num += (xs[i]-mx)*(ys[i]-my);
    den += (xs[i]-mx)**2;
  }
  const m = den ? num/den : 0;
  const b = my - m*mx;
  for (let i = 0; i < n; i++) {
    const pred = m*xs[i]+b;
    ssres += (ys[i]-pred)**2;
    sstot += (ys[i]-my)**2;
  }
  const r2 = sstot ? Math.max(0, 1 - ssres/sstot) : 0;
  return { m, b, r2 };
}

const CHART_COLORS = ['#58a6ff','#3fb950','#d29922','#f85149','#bc8cff','#79c0ff','#56d364','#e3b341','#ff7b72','#d2a8ff'];

function mkChart(id, type, labels, datasets, opts={}) {
  const ctx = document.getElementById(id);
  if (!ctx) return null;
  if (ctx._chart) ctx._chart.destroy();
  const ch = new Chart(ctx, {
    type,
    data: { labels, datasets },
    options: {
      responsive: true,
      maintainAspectRatio: true,
      plugins: {
        legend: { labels: { color: '#7d8590', font: { size: 11, family: 'IBM Plex Mono' } } },
        tooltip: { backgroundColor: '#1c2230', borderColor: '#21262d', borderWidth: 1,
          titleColor: '#e6edf3', bodyColor: '#7d8590', titleFont:{size:12}, bodyFont:{size:11} }
      },
      scales: type !== 'pie' && type !== 'doughnut' ? {
        x: { ticks:{color:'#7d8590',font:{size:11}}, grid:{color:'#21262d'} },
        y: { ticks:{color:'#7d8590',font:{size:11}}, grid:{color:'#21262d'} }
      } : {},
      ...opts
    }
  });
  ctx._chart = ch;
  return ch;
}

// ============================================================
// TAB SWITCH
// ============================================================
function switchTab(name) {
  document.querySelectorAll('.tab-btn').forEach((b,i) => {
    const tabs = ['resumen','prediccion','anomalias','simulador','recomendaciones','asistente','reporte'];
    b.classList.toggle('active', tabs[i] === name);
  });
  document.querySelectorAll('.tab-panel').forEach(p => p.classList.remove('active'));
  const panel = document.getElementById('tab-' + name);
  if (panel) panel.classList.add('active');
  if (name === 'simulador' && dataReady) updateSim();
}

// ============================================================
// RESUMEN
// ============================================================
function buildResumen() {
  const years = [2019,2020,2021,2022,2023,2024];
  const totProd = years.map(y => sum(allData.filter(r=>r.year===y), 'produccion'));
  const totSemb = years.map(y => sum(allData.filter(r=>r.year===y), 'sembrada'));
  const totCos  = years.map(y => sum(allData.filter(r=>r.year===y), 'cosechada'));
  const totalProd = sum(allData,'produccion');
  const totalSemb = sum(allData,'sembrada');
  const cultivosUnicos = [...new Set(allData.map(r=>r.cultivo))].length;
  const eficiencia = (sum(allData,'cosechada') / sum(allData,'sembrada') * 100).toFixed(1);

  // KPIs
  const kpis = [
    { label:'Producción Total', value: (totalProd/1000).toFixed(0)+'K', sub:'Toneladas 2019–2024', accent:'var(--green)', change:'+', delta:'ton' },
    { label:'Área Sembrada', value: (totalSemb/1000).toFixed(0)+'K', sub:'Hectáreas acumuladas', accent:'var(--blue)', change:'', delta:'ha' },
    { label:'Cultivos Registrados', value: cultivosUnicos, sub:'Especies distintas', accent:'var(--purple)', change:'', delta:'' },
    { label:'Eficiencia Cosecha', value: eficiencia+'%', sub:'Cosechado / Sembrado', accent:'var(--yellow)', change:'', delta:'' },
  ];
  document.getElementById('kpiRow').innerHTML = kpis.map(k => `
    <div class="kpi-card" style="--accent:${k.accent}">
      <div class="kpi-label">${k.label}</div>
      <div class="kpi-value">${k.value}</div>
      <div class="kpi-sub">${k.sub}</div>
    </div>`).join('');

  mkChart('chartProd','line', years, [{
    label:'Producción (ton)', data: totProd,
    borderColor:'#3fb950', backgroundColor:'rgba(63,185,80,0.08)',
    tension:0.4, pointRadius:4, fill:true
  }]);

  // Top cultivos
  const byCultivo = groupBy(allData,'cultivo');
  const tops = Object.entries(byCultivo)
    .map(([c,rows]) => ({ c, p: sum(rows,'produccion') }))
    .sort((a,b) => b.p - a.p).slice(0,10);
  mkChart('chartTop','bar', tops.map(t=>t.c), [{
    label:'Producción acumulada (ton)', data: tops.map(t=>t.p),
    backgroundColor: CHART_COLORS, borderRadius: 4
  }], { indexAxis:'y' });

  mkChart('chartArea','bar', years, [
    { label:'Sembrada (ha)', data:totSemb, backgroundColor:'rgba(88,166,255,0.7)', borderRadius:3 },
    { label:'Cosechada (ha)', data:totCos, backgroundColor:'rgba(63,185,80,0.7)', borderRadius:3 }
  ]);

  const byGrupo = groupBy(allData,'grupo');
  const grupos = Object.entries(byGrupo).map(([g,rows]) => ({ g, p: sum(rows,'produccion') }));
  mkChart('chartGrupo','doughnut', grupos.map(g=>g.g), [{
    data: grupos.map(g=>g.p), backgroundColor: CHART_COLORS, borderWidth:0
  }]);
}

// ============================================================
// PREDICCIÓN
// ============================================================
function buildPrediccion() {
  const byCultivo = groupBy(allData,'cultivo');
  const rows = [];

  Object.entries(byCultivo).forEach(([cultivo, records]) => {
    const byYear = groupBy(records,'year');
    const years = Object.keys(byYear).map(Number).sort();
    if (years.length < 3) return;
    const ys = years.map(y => sum(byYear[y],'produccion'));
    const { m, b, r2 } = linReg(years, ys);
    const pred2025 = Math.max(0, m*2025+b);
    const last = ys[ys.length-1];
    rows.push({ cultivo, last, pred2025, m, r2 });
  });

  rows.sort((a,b) => b.last - a.last);
  const top = rows.slice(0,20);

  document.getElementById('predBody').innerHTML = top.map(r => {
    const delta = r.last > 0 ? ((r.pred2025 - r.last)/r.last*100).toFixed(1) : 0;
    const dir = delta > 2 ? 'trend-up' : delta < -2 ? 'trend-down' : 'trend-flat';
    const icon = delta > 2 ? '↑' : delta < -2 ? '↓' : '→';
    return `<tr>
      <td>${r.cultivo}</td>
      <td>${r.last.toFixed(1)}</td>
      <td>${r.pred2025.toFixed(1)}</td>
      <td class="${dir}">${delta > 0?'+':''}${delta}%</td>
      <td>${(r.r2*100).toFixed(0)}%</td>
      <td class="${dir}">${icon}</td>
    </tr>`;
  }).join('');

  // Chart top 5
  const top5 = rows.slice(0,5);
  const years = [2019,2020,2021,2022,2023,2024,2025];
  const datasets = top5.map((r,i) => {
    const byCultivo2 = groupBy(allData.filter(d=>d.cultivo===r.cultivo),'year');
    const hist = [2019,2020,2021,2022,2023,2024].map(y => {
      const recs = byCultivo2[y];
      return recs ? sum(recs,'produccion') : null;
    });
    const { m, b } = linReg([2019,2020,2021,2022,2023,2024].filter((_,i)=>hist[i]!=null), hist.filter(v=>v!=null));
    const pred = Math.max(0, m*2025+b);
    return {
      label: r.cultivo,
      data: [...hist, pred],
      borderColor: CHART_COLORS[i],
      backgroundColor: 'transparent',
      tension: 0.3,
      pointRadius: 3,
      segment: { borderDash: ctx => ctx.p1DataIndex === 6 ? [4,4] : [] }
    };
  });
  if (predChart) predChart.destroy();
  predChart = mkChart('chartPred','line', years, datasets);
}

// ============================================================
// ANOMALÍAS
// ============================================================
function buildAnomalias() {
  const byCultivo = groupBy(allData,'cultivo');
  const anomalies = [];

  Object.entries(byCultivo).forEach(([cultivo, records]) => {
    if (records.length < 4) return;
    ['produccion','rendimiento'].forEach(field => {
      const vals = records.map(r => r[field]).filter(v=>v>0);
      if (vals.length < 3) return;
      const mean = vals.reduce((a,b)=>a+b,0)/vals.length;
      const std = Math.sqrt(vals.reduce((a,b)=>a+(b-mean)**2,0)/vals.length);
      if (std === 0) return;
      records.forEach(r => {
        const v = r[field];
        if (v <= 0) return;
        const z = (v - mean) / std;
        if (Math.abs(z) >= 1.8) {
          const sev = Math.abs(z) >= 2.5 ? 'ALTA' : 'MEDIA';
          const interp = v > mean
            ? `${field === 'produccion' ? 'Producción' : 'Rendimiento'} inusualmente alto en ${r.year}`
            : `${field === 'produccion' ? 'Producción' : 'Rendimiento'} inusualmente bajo en ${r.year}`;
          anomalies.push({ year:r.year, cultivo, field, v, mean, z, sev, interp });
        }
      });
    });
  });

  anomalies.sort((a,b) => Math.abs(b.z) - Math.abs(a.z));

  document.getElementById('anomalyBody').innerHTML = anomalies.slice(0,40).map(a => {
    const cls = a.sev === 'ALTA' ? 'severity-high' : 'severity-mid';
    return `<tr>
      <td>${a.year}</td>
      <td>${a.cultivo}</td>
      <td>${a.field === 'produccion' ? 'Producción' : 'Rendimiento'}</td>
      <td>${a.v.toFixed(2)}</td>
      <td>${a.mean.toFixed(2)}</td>
      <td>${a.z.toFixed(2)}</td>
      <td class="${cls}">${a.sev}</td>
      <td style="color:var(--text-muted);font-size:11px;">${a.interp}</td>
    </tr>`;
  }).join('');
}

// ============================================================
// SIMULADOR
// ============================================================
function updateSim() {
  if (!dataReady) return;
  const area = parseInt(document.getElementById('sArea').value);
  const clima = parseInt(document.getElementById('sClima').value);
  const tec = parseInt(document.getElementById('sTec').value);
  const perdida = parseInt(document.getElementById('sPerdida').value);

  document.getElementById('vArea').textContent = (area>=0?'+':'')+area+'%';
  document.getElementById('vClima').textContent = (clima>=0?'+':'')+clima+'%';
  document.getElementById('vTec').textContent = '+'+tec+'%';
  document.getElementById('vPerdida').textContent = perdida+'%';

  const base2024 = sum(allData.filter(r=>r.year===2024),'produccion');
  const factorArea = 1 + area/100;
  const factorClima = 1 + clima/100;
  const factorTec = 1 + tec/100;
  const factorPerdida = 1 - perdida/100;
  const sim2025 = base2024 * factorArea * factorClima * factorTec * factorPerdida;
  const sim2026 = sim2025 * factorArea * factorTec * factorPerdida;
  const delta25 = ((sim2025/base2024-1)*100).toFixed(1);
  const delta26 = ((sim2026/base2024-1)*100).toFixed(1);

  const col25 = delta25 >= 0 ? 'highlight' : 'warn';
  const col26 = delta26 >= 0 ? 'highlight' : 'warn';

  document.getElementById('simResult').innerHTML = `<strong style="color:var(--text)">RESULTADOS DE SIMULACIÓN</strong>

Base 2024:           ${base2024.toFixed(0)} ton
─────────────────────────────────────────────
Proyección 2025:     <span class="${col25}">${sim2025.toFixed(0)} ton  (${delta25>=0?'+':''}${delta25}%)</span>
Proyección 2026:     <span class="${col26}">${sim2026.toFixed(0)} ton  (${delta26>=0?'+':''}${delta26}%)</span>
─────────────────────────────────────────────
Supuestos aplicados:
  · Expansión área:    ${area>=0?'+':''}${area}%
  · Factor climático:  ${clima>=0?'+':''}${clima}%
  · Adopción tecnológica: +${tec}%
  · Pérdida post-cosecha: ${perdida}%
─────────────────────────────────────────────
<span style="color:var(--text-muted);font-size:11px;">Modelo multiplicativo simple. No incluye efectos de mercado.</span>`;

  // Chart escenarios
  const escBase   = [base2024, base2024*1.0, base2024*1.0];
  const escOptim  = [base2024, base2024*1.15, base2024*1.25];
  const escPesim  = [base2024, base2024*0.85, base2024*0.75];
  const escSim    = [base2024, sim2025, sim2026];

  if (simChart) simChart.destroy();
  simChart = mkChart('chartSim','line',['2024','2025','2026'],[
    { label:'Escenario base', data:escBase, borderColor:'#7d8590', borderDash:[4,4], tension:0, pointRadius:3 },
    { label:'Optimista +15%', data:escOptim, borderColor:'#3fb950', tension:0, pointRadius:3 },
    { label:'Pesimista -15%', data:escPesim, borderColor:'#f85149', tension:0, pointRadius:3 },
    { label:'Tu simulación', data:escSim, borderColor:'#58a6ff', borderWidth:2.5, tension:0, pointRadius:5 },
  ]);
}

// ============================================================
// RECOMENDACIONES
// ============================================================
function buildRecomendaciones() {
  const byCultivo = groupBy(allData,'cultivo');
  const scores = [];

  Object.entries(byCultivo).forEach(([cultivo, records]) => {
    const byYear = groupBy(records,'year');
    const years = Object.keys(byYear).map(Number).sort();
    if (years.length < 3) return;

    const prods = years.map(y => sum(byYear[y],'produccion'));
    const sembs = years.map(y => sum(byYear[y],'sembrada'));
    const coses = years.map(y => sum(byYear[y],'cosechada'));
    const rends = years.map(y => avg(byYear[y],'rendimiento'));

    const last = prods.length-1;
    const growthProd = prods[0] > 0 ? (prods[last]-prods[0])/prods[0] : 0;
    const efic = sembs[last] > 0 ? coses[last]/sembs[last] : 0;
    const growthRend = rends[0] > 0 ? (rends[last]-rends[0])/rends[0] : 0;

    const score = (growthProd * 0.4 + efic * 0.3 + growthRend * 0.3) * 100;
    scores.push({ cultivo, score, growthProd, efic, growthRend, lastProd: prods[last] });
  });

  scores.sort((a,b) => b.score - a.score);
  const top8 = scores.slice(0,8);

  const colors = ['var(--green)','var(--green)','var(--blue)','var(--blue)','var(--yellow)','var(--yellow)','var(--text-muted)','var(--text-muted)'];
  document.getElementById('recList').innerHTML = top8.map((r,i) => `
    <div class="rec-item" style="--accent:${colors[i]}">
      <div class="rec-item-top">
        <div class="rec-cultivo">${i+1}. ${r.cultivo}</div>
        <div class="rec-score" style="color:${colors[i]}">Score: ${r.score.toFixed(1)}</div>
      </div>
      <div class="rec-text">
        Crecimiento de producción: <strong>${(r.growthProd*100).toFixed(1)}%</strong> ·
        Eficiencia cosecha: <strong>${(r.efic*100).toFixed(1)}%</strong> ·
        Mejora de rendimiento: <strong>${(r.growthRend*100).toFixed(1)}%</strong> ·
        Prod. 2024: <strong>${r.lastProd.toFixed(1)} ton</strong>
      </div>
    </div>`).join('');
}

// ============================================================
// ASISTENTE IA
// ============================================================
let chatHistory = [];

function getDataContext() {
  const byYear = groupBy(allData,'year');
  const years = [2019,2020,2021,2022,2023,2024];
  const prodByYear = years.map(y => ({ year:y, prod: sum(byYear[y]||[],'produccion').toFixed(0) }));
  const byCultivo = groupBy(allData,'cultivo');
  const tops = Object.entries(byCultivo)
    .map(([c,r]) => ({ c, p: sum(r,'produccion') }))
    .sort((a,b)=>b.p-a.p).slice(0,10)
    .map(r => `${r.c}: ${r.p.toFixed(0)} ton`).join(', ');
  const totalRegistros = allData.length;
  return `DATOS REALES CARGADOS — Observatorio Agrícola de Medellín, Antioquia, Colombia. Fuente: datos.gov.co (API uejq-wxrr). Total registros: ${totalRegistros}.

Producción total por año (ton): ${prodByYear.map(r=>`${r.year}: ${r.prod}`).join(' | ')}
Top 10 cultivos por producción acumulada: ${tops}
Cultivos únicos: ${[...new Set(allData.map(r=>r.cultivo))].length}
Grupos: ${[...new Set(allData.map(r=>r.grupo))].join(', ')}
Área sembrada total: ${sum(allData,'sembrada').toFixed(0)} ha
Área cosechada total: ${sum(allData,'cosechada').toFixed(0)} ha`;
}

async function sendChat() {
  const input = document.getElementById('chatInput');
  const msg = input.value.trim();
  if (!msg || !dataReady) return;
  input.value = '';
  appendBubble('user', msg);
  chatHistory.push({ role:'user', content: msg });
  const btn = document.getElementById('chatSend');
  btn.disabled = true;
  const typingId = appendTyping();

  try {
    const systemPrompt = `Eres un experto en análisis agrícola y política pública colombiana, especializado en los datos de producción del municipio de Medellín, Antioquia. Respondes SIEMPRE en español. Eres conciso, analítico y orientado a datos concretos. 

${getDataContext()}

Cuando el usuario pregunte, usa estos datos reales para fundamentar tus respuestas. Si detectas tendencias, anomalías o recomendaciones, explícalas con números específicos de los datos. No inventes cifras que no estén en el contexto.`;

    const resp = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-6',
        max_tokens: 1000,
        system: systemPrompt,
        messages: chatHistory
      })
    });
    const data = await resp.json();
    const text = data.content?.[0]?.text || 'No se pudo obtener respuesta.';
    removeTyping(typingId);
    appendBubble('ai', text);
    chatHistory.push({ role:'assistant', content: text });
  } catch(e) {
    removeTyping(typingId);
    appendBubble('ai', 'Error de conexión con la IA. Verifica tu acceso a la API.');
  }
  btn.disabled = false;
}

function sendQuick(q) {
  document.getElementById('chatInput').value = q;
  sendChat();
}

function appendBubble(role, text) {
  const container = document.getElementById('chatMessages');
  const div = document.createElement('div');
  div.className = `chat-bubble ${role}`;
  if (role === 'ai') div.innerHTML = `<div class="ai-label">ASISTENTE IA · OBSERVATORIO AGRÍCOLA</div>${text.replace(/\n/g,'<br>')}`;
  else div.textContent = text;
  container.appendChild(div);
  container.scrollTop = container.scrollHeight;
  return div;
}

function appendTyping() {
  const id = 'typing-' + Date.now();
  const container = document.getElementById('chatMessages');
  const div = document.createElement('div');
  div.className = 'chat-bubble ai';
  div.id = id;
  div.innerHTML = `<div class="ai-label">ASISTENTE IA · OBSERVATORIO AGRÍCOLA</div><div class="typing-dots"><span></span><span></span><span></span></div>`;
  container.appendChild(div);
  container.scrollTop = container.scrollHeight;
  return id;
}

function removeTyping(id) {
  const el = document.getElementById(id);
  if (el) el.remove();
}

// ============================================================
// REPORTE AUTOMÁTICO
// ============================================================
async function generateReport() {
  if (!dataReady) return;
  const btn = document.getElementById('reportBtn');
  const output = document.getElementById('reportOutput');
  btn.disabled = true;
  btn.textContent = 'Generando…';
  output.textContent = 'La IA está analizando los datos y redactando el reporte…';

  const byYear = groupBy(allData,'year');
  const byCultivo = groupBy(allData,'cultivo');
  const years = [2019,2020,2021,2022,2023,2024];
  const prods = years.map(y => sum(byYear[y]||[],'produccion').toFixed(0));
  const tops = Object.entries(byCultivo).map(([c,r])=>({c,p:sum(r,'produccion')})).sort((a,b)=>b.p-a.p).slice(0,5);
  const anomCount = allData.length; // use as proxy

  const prompt = `Eres el sistema de generación de reportes del Observatorio Agrícola de Medellín. Redacta un reporte ejecutivo institucional en español, formal, con secciones claramente diferenciadas usando títulos en mayúsculas. El reporte debe incluir:

1. ENCABEZADO con nombre del reporte, entidad, fecha y fuente
2. RESUMEN EJECUTIVO (3 párrafos)
3. TENDENCIAS DE PRODUCCIÓN (análisis de los datos reales)
4. HALLAZGOS PRINCIPALES (mínimo 4 puntos concretos)
5. ANOMALÍAS DETECTADAS (menciona 2-3 casos específicos)
6. RECOMENDACIONES DE POLÍTICA PÚBLICA (mínimo 4 recomendaciones)
7. CONCLUSIONES

DATOS REALES A USAR:
- Municipio: Medellín, Antioquia
- Período: 2019–2024
- Registros analizados: ${allData.length.toLocaleString()}
- Producción por año (ton): ${years.map((y,i)=>`${y}: ${prods[i]}`).join(', ')}
- Top 5 cultivos: ${tops.map(r=>`${r.c} (${r.p.toFixed(0)} ton)`).join(', ')}
- Cultivos únicos: ${[...new Set(allData.map(r=>r.cultivo))].length}
- Fuente: datos.gov.co, API uejq-wxrr

Usa cifras concretas. Tono institucional colombiano. No uses markdown con asteriscos, solo texto plano con títulos en mayúsculas.`;

  try {
    const resp = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-6',
        max_tokens: 1000,
        messages: [{ role:'user', content: prompt }]
      })
    });
    const data = await resp.json();
    const text = data.content?.[0]?.text || 'No se pudo generar el reporte.';
    output.textContent = text;
  } catch(e) {
    output.textContent = 'Error al conectar con la IA. Verifica tu acceso a la API.';
  }
  btn.disabled = false;
  btn.textContent = '⬡ Regenerar Reporte';
}

// ============================================================
// INIT
// ============================================================
loadData();
</script>
</body>
</html>
