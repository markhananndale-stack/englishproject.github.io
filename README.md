<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Mini Trader — Trading Game</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;800&display=swap" rel="stylesheet">
  <style>
    :root{
      --bg: linear-gradient(180deg,#0f1724,#071226 60%);
      --card: rgba(255,255,255,0.03);
      --glass: rgba(255,255,255,0.04);
      --accent: linear-gradient(90deg,#7dd3fc,#a78bfa);
      --green: #34d399;
      --red: #fb7185;
      --muted: rgba(230,238,248,0.7);
      --radius: 14px;
      --glass-border: rgba(255,255,255,0.04);
      font-synthesis: none;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, Arial;
      background:var(--bg);
      color:#e6eef8;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      padding:28px;
      display:flex;
      align-items:flex-start;
      justify-content:center;
      gap:20px;
    }

    /* Layout */
    .app {
      width:1100px;
      max-width:calc(100% - 48px);
      display:grid;
      grid-template-columns: 360px 1fr;
      gap:20px;
      align-items:start;
    }

    /* Sidebar */
    .panel {
      background:var(--card);
      border:1px solid var(--glass-border);
      border-radius:var(--radius);
      padding:18px;
      box-shadow: 0 8px 30px rgba(2,6,23,0.6);
    }
    .brand {display:flex;align-items:center;gap:12px;margin-bottom:12px}
    .logo {
      width:46px;height:46px;border-radius:10px;
      background:linear-gradient(180deg,#0ea5ff,#7c3aed);
      display:grid;place-items:center;font-weight:800;color:#021025;
      box-shadow:0 8px 20px rgba(12,18,30,0.6);
    }
    h1{font-size:1rem;margin:0}
    .subtitle{color:var(--muted);font-size:12px;margin-top:4px}

    .balance{
      display:flex;flex-direction:column;gap:8px;margin-top:12px;
    }
    .money{
      font-size:1.6rem;font-weight:700;
      background:var(--accent);
      -webkit-background-clip:text;background-clip:text;color:transparent;
    }

    .networth{
      display:flex;justify-content:space-between;gap:8px;margin-top:10px;padding-top:10px;border-top:1px dashed rgba(255,255,255,0.03);
    }
    .stat{font-size:13px;color:var(--muted)}
    .buttons{display:flex;gap:8px;margin-top:12px}
    button.btn{
      border:0;padding:8px 12px;border-radius:10px;font-weight:700;cursor:pointer;
      background:linear-gradient(90deg,#0ea5ff,#7c3aed);color:#021025;
      box-shadow:0 8px 20px rgba(12,18,30,0.5);
    }
    .btn.ghost{background:transparent;color:var(--muted);border:1px solid rgba(255,255,255,0.03)}

    /* Market list */
    .market {margin-top:18px;display:flex;flex-direction:column;gap:12px}
    .item{
      display:flex;align-items:center;justify-content:space-between;gap:12px;
      padding:10px;border-radius:12px;background:linear-gradient(180deg, rgba(255,255,255,0.02), transparent);
      transition:transform .22s ease, box-shadow .22s ease;
      border:1px solid rgba(255,255,255,0.02);
    }
    .item:hover{transform:translateY(-6px);box-shadow:0 12px 30px rgba(2,6,23,0.6)}
    .left{display:flex;gap:12px;align-items:center}
    .icon{width:44px;height:44px;border-radius:10px;display:grid;place-items:center;background:rgba(255,255,255,0.02);font-weight:700}
    .meta{display:flex;flex-direction:column}
    .meta .name{font-weight:700}
    .price{font-weight:800}
    .change{font-size:12px;color:var(--muted)}

    .mini-chart{width:120px;height:36px;border-radius:8px;overflow:hidden;background:#0b1320;border:1px solid rgba(255,255,255,0.02)}

    /* Main area */
    .main-panel{
      display:flex;flex-direction:column;gap:18px;
    }
    .market-panel{
      padding:18px;background:var(--card);border-radius:var(--radius);border:1px solid var(--glass-border);
      box-shadow:0 8px 30px rgba(2,6,23,0.6);
    }

    .trade-area{display:grid;grid-template-columns:1fr 320px;gap:18px}
    .trade-card{
      background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      padding:16px;border-radius:12px;border:1px solid rgba(255,255,255,0.02);
    }
    .trade-card h3{margin:0 0 8px}
    .controls{display:flex;gap:8px;align-items:center;margin-top:8px}
    input[type=number]{width:120px;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit}
    .quick{display:flex;gap:6px}
    .action-btn{padding:10px 14px;border-radius:10px;border:0;font-weight:800;cursor:pointer}
    .buy{background:linear-gradient(90deg,#10b981,#34d399);color:#021025}
    .sell{background:linear-gradient(90deg,#fb7185,#f97316);color:#021025}

    .portfolio{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:12px;margin-top:12px}
    .asset{
      padding:12px;border-radius:10px;background:linear-gradient(180deg, rgba(255,255,255,0.02), transparent);
      border:1px solid rgba(255,255,255,0.02)
    }
    .asset .qty{font-weight:800;font-size:15px}

    /* History */
    .log{
      max-height:200px;overflow:auto;padding:12px;border-radius:8px;background:rgba(0,0,0,0.15);margin-top:12px;font-size:13px;
      border:1px solid rgba(255,255,255,0.02)
    }
    .log .entry{padding:6px 0;border-bottom:1px dashed rgba(255,255,255,0.02)}
    .log .entry:last-child{border-bottom:none}

    /* Achievements */
    .achievements{display:flex;gap:8px;flex-wrap:wrap;margin-top:12px}
    .badge{padding:8px 10px;border-radius:10px;background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.02);font-weight:700;color:var(--muted);font-size:13px}

    /* Footer controls */
    .controls-footer{display:flex;justify-content:space-between;align-items:center;margin-top:10px}
    .small{font-size:12px;color:var(--muted)}

    /* Mobile */
    @media (max-width:900px){
      .app{grid-template-columns:1fr; padding-bottom:40px}
      .trade-area{grid-template-columns:1fr}
      body{padding:18px}
    }

    /* Reduced motion */
    @media (prefers-reduced-motion: reduce){
      .item, .device, .market-panel, .trade-card {transition:none!important;animation:none!important}
    }
  </style>
</head>
<body>
  <div class="app" id="app">
    <aside class="panel">
      <div class="brand">
        <div class="logo">MT</div>
        <div>
          <h1>Mini Trader</h1>
          <div class="subtitle">Start small. Trade smart.</div>
        </div>
      </div>

      <div class="balance">
        <div class="money" id="cash">$10,000.00</div>
        <div class="networth">
          <div class="stat">Portfolio: <span id="portfolio-value">$0.00</span></div>
          <div class="stat">Net: <strong id="networth">$10,000.00</strong></div>
        </div>
      </div>

      <div class="buttons">
        <button class="btn" id="new-day">Advance Day</button>
        <button class="btn ghost" id="reset">Reset</button>
      </div>

      <div class="market" id="market-list" aria-live="polite" style="margin-top:18px">
        <!-- items inserted here -->
      </div>

      <div style="margin-top:18px">
        <div class="small">Shortcuts: <span class="muted">1/2/3 select item • B buy • S sell</span></div>
      </div>
    </aside>

    <main class="main-panel">
      <section class="market-panel">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <h2 style="margin:0">Market</h2>
            <div class="small muted">Prices update every second; events happen randomly.</div>
          </div>
          <div>
            <div class="small muted">Day <span id="day">1</span></div>
          </div>
        </div>

        <div class="trade-area" style="margin-top:14px">
          <div class="trade-card">
            <h3 id="selected-name">Select an asset</h3>
            <div class="small muted" id="selected-price">—</div>

            <div style="margin-top:12px">
              <canvas id="big-chart" width="800" height="240" style="width:100%;height:160px;border-radius:10px;background:#071226;border:1px solid rgba(255,255,255,0.02)"></canvas>
            </div>

            <div class="controls" style="margin-top:10px">
              <label for="qty" class="small">Qty</label>
              <input id="qty" type="number" min="1" value="1" />
              <div class="quick">
                <button class="btn ghost" id="half">HALF</button>
                <button class="btn ghost" id="max">MAX</button>
              </div>
            </div>

            <div class="controls" style="margin-top:10px">
              <button class="action-btn buy" id="buy-btn">Buy</button>
              <button class="action-btn sell" id="sell-btn">Sell</button>
            </div>

            <div class="portfolio" id="portfolio">
              <!-- assets -->
            </div>
          </div>

          <div class="trade-card" style="min-height:260px">
            <h3>Transaction Log</h3>
            <div class="log" id="log"></div>

            <div style="margin-top:12px">
              <h4 style="margin:0 0 6px">Achievements</h4>
              <div class="achievements" id="achievements"></div>
            </div>

            <div class="controls-footer">
              <div class="small muted">Auto-save to localStorage</div>
              <div><button class="btn ghost" id="export">Export</button></div>
            </div>
          </div>
        </div>

      </section>
    </main>
  </div>

<script>
/* ===========================
   Mini Trader — Game Logic
   =========================== */

(() => {
  // Game state and defaults
  const defaultState = {
    day: 1,
    cash: 10000,
    assets: {
      "ORE": { qty: 0 },
      "SPICE": { qty: 0 },
      "GRAIN": { qty: 0 }
    },
    market: {
      // price base and volatility
      "ORE": { price: 120, vol: 0.02, history: [] },
      "SPICE": { price: 48, vol: 0.035, history: [] },
      "GRAIN": { price: 8.4, vol: 0.015, history: [] }
    },
    achievements: {}
  };

  // State (load from localStorage if present)
  let state = loadState() || JSON.parse(JSON.stringify(defaultState));

  // DOM references
  const cashEl = document.getElementById('cash');
  const portfolioValueEl = document.getElementById('portfolio-value');
  const networthEl = document.getElementById('networth');
  const marketList = document.getElementById('market-list');
  const selectedName = document.getElementById('selected-name');
  const selectedPrice = document.getElementById('selected-price');
  const bigChart = document.getElementById('big-chart');
  const qtyInput = document.getElementById('qty');
  const buyBtn = document.getElementById('buy-btn');
  const sellBtn = document.getElementById('sell-btn');
  const halfBtn = document.getElementById('half');
  const maxBtn = document.getElementById('max');
  const portfolioEl = document.getElementById('portfolio');
  const logEl = document.getElementById('log');
  const dayEl = document.getElementById('day');
  const newDayBtn = document.getElementById('new-day');
  const resetBtn = document.getElementById('reset');
  const achievementsEl = document.getElementById('achievements');
  const exportBtn = document.getElementById('export');

  // Internal
  let selectedAsset = Object.keys(state.market)[0];
  let intervalId = null;
  const tickIntervalMs = 1000; // 1s price updates

  // init
  function init() {
    // initialize histories if empty
    for (const k of Object.keys(state.market)) {
      const m = state.market[k];
      if (!Array.isArray(m.history) || m.history.length === 0) {
        m.history = Array.from({length:40}, (_,i) => Math.max(0.01, m.price * (1 + (Math.random()-0.5)*0.06)));
      }
    }
    renderMarket();
    selectAsset(selectedAsset);
    renderPortfolio();
    renderStats();
    renderLog();
    renderAchievements();

    // start price simulation
    if (intervalId) clearInterval(intervalId);
    intervalId = setInterval(gameTick, tickIntervalMs);

    // events
    buyBtn.addEventListener('click', onBuy);
    sellBtn.addEventListener('click', onSell);
    halfBtn.addEventListener('click', onHalf);
    maxBtn.addEventListener('click', onMax);
    newDayBtn.addEventListener('click', advanceDay);
    resetBtn.addEventListener('click', resetGame);
    exportBtn.addEventListener('click', exportState);

    // keyboard shortcuts
    window.addEventListener('keydown', (e) => {
      if (e.target.tagName === 'INPUT') return;
      if (e.key >= '1' && e.key <= '9') {
        const idx = parseInt(e.key) - 1;
        const keys = Object.keys(state.market);
        if (idx < keys.length) selectAsset(keys[idx]);
      }
      if (e.key.toLowerCase() === 'b') buyBtn.click();
      if (e.key.toLowerCase() === 's') sellBtn.click();
    });
  }

  /* ---------- Rendering ---------- */

  function renderMarket() {
    marketList.innerHTML = '';
    Object.entries(state.market).forEach(([symbol, info]) => {
      const price = toMoney(info.price);
      const change = computeChange(info);
      const item = document.createElement('div');
      item.className = 'item';
      item.tabIndex = 0;
      item.innerHTML = `
        <div class="left">
          <div class="icon">${symbol[0]}</div>
          <div class="meta">
            <div class="name">${symbol}</div>
            <div class="change">${toMoney(change.delta)} (${(change.pct*100).toFixed(2)}%)</div>
          </div>
        </div>
        <div style="display:flex;gap:12px;align-items:center">
          <div class="price">${price}</div>
          <div class="mini-chart"><canvas width="120" height="36" data-symbol="${symbol}"></canvas></div>
        </div>
      `;
      item.addEventListener('click', () => selectAsset(symbol));
      marketList.appendChild(item);

      // draw small chart
      const canvas = item.querySelector('canvas');
      if (canvas) drawSparkline(canvas, info.history);
    });
  }

  function selectAsset(symbol) {
    selectedAsset = symbol;
    selectedName.textContent = symbol;
    selectedPrice.textContent = `Price: ${toMoney(state.market[symbol].price)}`;
    // update qty default to 1
    qtyInput.value = 1;
    // draw big chart
    drawBigChart(state.market[symbol].history, state.market[symbol].price);
    // highlight selected
    Array.from(marketList.children).forEach(child => {
      child.style.border = child.querySelector('.name')?.textContent === symbol ? '1px solid rgba(255,255,255,0.06)' : '1px solid rgba(255,255,255,0.02)';
    });
  }

  function renderPortfolio() {
    portfolioEl.innerHTML = '';
    for (const [symbol, data] of Object.entries(state.assets)) {
      const price = state.market[symbol].price;
      const value = data.qty * price;
      const el = document.createElement('div');
      el.className = 'asset';
      el.innerHTML = `
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <div style="font-weight:800">${symbol}</div>
            <div class="small muted">Price ${toMoney(price)}</div>
          </div>
          <div style="text-align:right">
            <div class="qty">${data.qty}</div>
            <div class="small muted">${toMoney(value)}</div>
          </div>
        </div>
      `;
      portfolioEl.appendChild(el);
    }
  }

  function renderStats() {
    const portfolioValue = computePortfolioValue();
    portfolioValueEl.textContent = toMoney(portfolioValue);
    const net = state.cash + portfolioValue;
    networthEl.textContent = toMoney(net);
    cashEl.textContent = toMoney(state.cash);
    dayEl.textContent = state.day;
  }

  function renderLog() {
    logEl.innerHTML = '';
    const entries = (state._log || []).slice().reverse();
    for (const e of entries) {
      const d = document.createElement('div');
      d.className = 'entry';
      d.innerHTML = `<div style="display:flex;justify-content:space-between"><div>${e.text}</div><div class="small muted">${e.time}</div></div>`;
      logEl.appendChild(d);
    }
  }

  function renderAchievements() {
    achievementsEl.innerHTML = '';
    const keys = Object.keys(state.achievements || {});
    if (keys.length === 0) {
      achievementsEl.innerHTML = `<div class="small muted">No achievements yet</div>`;
      return;
    }
    keys.forEach(k=>{
      const b = document.createElement('div');
      b.className = 'badge';
      b.textContent = k;
      achievementsEl.appendChild(b);
    });
  }

  /* ---------- Game mechanics ---------- */

  function gameTick() {
    // update prices for each market entry
    for (const [symbol, info] of Object.entries(state.market)) {
      // random walk with volatility and pull toward base
      const vol = info.vol;
      const shock = (Math.random() - 0.5) * 2 * vol * info.price;
      // small mean reversion (soft)
      const base = info.price;
      let newPrice = Math.max(0.01, info.price + shock);
      // occasional slight damping
      newPrice = newPrice * (1 - (Math.random()*0.003));
      info.price = newPrice;
      info.history.push(info.price);
      if (info.history.length > 160) info.history.shift();
    }

    // occasionally spawn market event
    if (Math.random() < 0.05) {
      spawnMarketEvent();
    }

    // update UI
    renderMarket();
    selectAsset(selectedAsset);
    renderPortfolio();
    renderStats();
    saveState();
  }

  function advanceDay() {
    state.day++;
    // small interest or random cashback
    if (Math.random() < 0.2) {
      const bonus = Math.round(Math.random()*200 - 50);
      state.cash += bonus;
      pushLog(`Daily dividend: ${toMoney(bonus)}`);
    }
    pushLog(`Advanced to day ${state.day}`);
    renderStats();
    saveState();
  }

  function spawnMarketEvent() {
    const symbols = Object.keys(state.market);
    const target = symbols[Math.floor(Math.random()*symbols.length)];
    const magnitude = (Math.random() < 0.5 ? -1 : 1) * (0.05 + Math.random()*0.22);
    // apply multiplier
    const old = state.market[target].price;
    const newPrice = Math.max(0.02, old * (1 + magnitude));
    state.market[target].price = newPrice;
    state.market[target].history.push(newPrice);
    pushLog(`Event: ${target} ${magnitude>0? 'surged':'dipped'} ${ (magnitude*100).toFixed(1) }%`);
    // award achievement when big event
    if (Math.abs(magnitude) > 0.18) {
      awardAchievement(`Market Shock (${target})`);
    }
  }

  /* ---------- Trading ---------- */

  function onBuy() {
    const qty = Math.max(1, Math.floor(Number(qtyInput.value) || 1));
    const price = state.market[selectedAsset].price;
    const cost = qty * price;
    if (cost > state.cash) {
      pushLog(`Not enough cash to buy ${qty} ${selectedAsset} (cost ${toMoney(cost)})`);
      flash('red');
      return;
    }
    state.cash -= cost;
    state.assets[selectedAsset].qty += qty;
    pushLog(`Bought ${qty} ${selectedAsset} for ${toMoney(cost)}`);
    renderPortfolio();
    renderStats();
    checkAchievements();
    saveState();
  }

  function onSell() {
    const qty = Math.max(1, Math.floor(Number(qtyInput.value) || 1));
    const held = state.assets[selectedAsset].qty;
    if (qty > held) {
      pushLog(`You don't have ${qty} ${selectedAsset} to sell.`);
      flash('red');
      return;
    }
    const price = state.market[selectedAsset].price;
    const proceeds = qty * price;
    state.cash += proceeds;
    state.assets[selectedAsset].qty -= qty;
    pushLog(`Sold ${qty} ${selectedAsset} for ${toMoney(proceeds)}`);
    renderPortfolio();
    renderStats();
    checkAchievements();
    saveState();
  }

  function onHalf() {
    const held = state.assets[selectedAsset].qty;
    if (held <= 0) { qtyInput.value = 1; return; }
    qtyInput.value = Math.max(1, Math.floor(held/2));
  }

  function onMax() {
    const affordable = Math.floor(state.cash / state.market[selectedAsset].price);
    qtyInput.value = Math.max(1, affordable);
  }

  /* ---------- Utilities ---------- */

  function computePortfolioValue(){
    let total = 0;
    for (const [symbol, data] of Object.entries(state.assets)) {
      total += data.qty * state.market[symbol].price;
    }
    return total;
  }

  function computeChange(info){
    const h = info.history;
    const prev = h[h.length-2] || h[h.length-1] || info.price;
    const delta = info.price - prev;
    const pct = prev ? (delta / prev) : 0;
    return { delta, pct };
  }

  function toMoney(n){
    const sign = n < 0 ? '-' : '';
    return sign + '$' + Math.abs(n).toLocaleString(undefined, {minimumFractionDigits:2, maximumFractionDigits:2});
  }

  function pushLog(text) {
    const time = new Date().toLocaleTimeString();
    state._log = state._log || [];
    state._log.push({ text, time });
    if (state._log.length > 200) state._log.shift();
    renderLog();
  }

  function flash(color) {
    // tiny flash on body
    const el = document.body;
    const orig = el.style.boxShadow;
    el.style.boxShadow = color === 'red' ? 'inset 0 0 120px rgba(251,113,133,0.14)' : 'inset 0 0 120px rgba(16,185,129,0.08)';
    setTimeout(()=> el.style.boxShadow = orig, 250);
  }

  function awardAchievement(name) {
    if (!state.achievements[name]) {
      state.achievements[name] = { when: new Date().toISOString() };
      pushLog(`Achievement unlocked: ${name}`);
      renderAchievements();
    }
  }

  function checkAchievements() {
    // sample achievements
    if (state.cash >= 50000) awardAchievement('Rich: $50k');
    const totalQty = Object.values(state.assets).reduce((s,a)=>s+a.qty,0);
    if (totalQty >= 1000) awardAchievement('Collector: 1000 units');
    Object.entries(state.assets).forEach(([symbol, data])=>{
      if (data.qty >= 100) awardAchievement(`${symbol} Hoarder: 100+`);
    });
  }

  /* ---------- Persistence ---------- */
  function saveState() {
    try {
      const copy = JSON.parse(JSON.stringify(state));
      // remove large stuff if any
      localStorage.setItem('mini-trader-v1', JSON.stringify(copy));
    } catch (e) {
      console.warn('save failed', e);
    }
  }
  function loadState(){
    try {
      const raw = localStorage.getItem('mini-trader-v1');
      if (!raw) return null;
      return JSON.parse(raw);
    } catch (e) {
      console.warn('load failed', e);
      return null;
    }
  }

  function resetGame(){
    if (!confirm('Reset game and clear save?')) return;
    localStorage.removeItem('mini-trader-v1');
    state = JSON.parse(JSON.stringify(defaultState));
    init();
    pushLog('Game reset.');
    saveState();
  }

  function exportState() {
    const data = JSON.stringify(state, null, 2);
    const blob = new Blob([data], {type:'application/json'});
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'mini-trader-save.json';
    a.click();
    URL.revokeObjectURL(url);
  }

  /* ---------- Canvas charts ---------- */

  function drawSparkline(canvas, history) {
    const ctx = canvas.getContext('2d');
    const w = canvas.width;
    const h = canvas.height;
    ctx.clearRect(0,0,w,h);
    if (!history || history.length === 0) return;
    const max = Math.max(...history);
    const min = Math.min(...history);
    ctx.lineWidth = 2;
    ctx.strokeStyle = '#7dd3fc';
    ctx.beginPath();
    const L = history.length;
    for (let i=0;i<L;i++){
      const x = (i/(L-1)) * w;
      const y = h - ((history[i]-min)/(max-min || 1) * h);
      if (i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
    }
    ctx.stroke();
    // fill gradient
    const grad = ctx.createLinearGradient(0,0,0,h);
    grad.addColorStop(0, 'rgba(125,211,252,0.12)');
    grad.addColorStop(1, 'rgba(125,211,252,0.02)');
    ctx.lineTo(w,h);
    ctx.lineTo(0,h);
    ctx.closePath();
    ctx.fillStyle = grad;
    ctx.fill();
  }

  function drawBigChart(history, currentPrice) {
    const ctx = bigChart.getContext('2d');
    const w = bigChart.width = bigChart.clientWidth * devicePixelRatio;
    const h = bigChart.height = bigChart.clientHeight * devicePixelRatio;
    ctx.clearRect(0,0,w,h);
    const data = history.slice(-120);
    if (data.length < 2) return;
    const max = Math.max(...data, currentPrice);
    const min = Math.min(...data, currentPrice);
    // draw grid
    ctx.strokeStyle = 'rgba(255,255,255,0.02)';
    ctx.lineWidth = 1 * devicePixelRatio;
    for (let i=0;i<3;i++){
      ctx.beginPath();
      ctx.moveTo(0, (i/2)*h);
      ctx.lineTo(w, (i/2)*h);
      ctx.stroke();
    }
    // draw curve
    ctx.beginPath();
    ctx.lineWidth = 3 * devicePixelRatio;
    ctx.strokeStyle = '#7dd3fc';
    for (let i=0;i<data.length;i++){
      const x = (i/(data.length-1)) * w;
      const y = h - ((data[i]-min)/(max-min || 1) * h);
      if (i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
    }
    ctx.stroke();
    // last price marker
    ctx.fillStyle = '#a78bfa';
    ctx.beginPath();
    const lastX = w;
    const lastY = h - ((currentPrice-min)/(max-min || 1)*h);
    ctx.arc(lastX-6*devicePixelRatio, lastY, 6*devicePixelRatio, 0, Math.PI*2);
    ctx.fill();
  }

  /* ---------- Boot ---------- */
  init();

  // ensure big chart redraws on resize
  window.addEventListener('resize', () => drawBigChart(state.market[selectedAsset].history, state.market[selectedAsset].price));

})();
</script>
</body>
</html>
