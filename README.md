# break-even-tool
[index.html](https://github.com/user-attachments/files/26588892/index.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Break-Even Analysis Tool</title>
  <link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@400;500;600&display=swap" rel="stylesheet" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg: #f7f5f0;
      --surface: #ffffff;
      --surface2: #f0ede6;
      --border: rgba(0,0,0,0.10);
      --text: #1a1814;
      --muted: #7a7570;
      --accent: #2a5f3f;
      --accent-light: #e6f0eb;
      --danger: #c0392b;
      --danger-light: #fdf0ee;
      --warning: #b45309;
      --warning-light: #fef3e2;
      --radius: 12px;
      --radius-sm: 8px;
    }

    body {
      font-family: 'DM Sans', sans-serif;
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      padding: 0;
    }

    /* Header */
    .site-header {
      background: var(--accent);
      padding: 2rem 2rem 1.5rem;
      color: white;
    }
    .site-header h1 {
      font-family: 'DM Serif Display', serif;
      font-size: clamp(1.6rem, 4vw, 2.4rem);
      font-weight: 400;
      letter-spacing: -0.01em;
      margin-bottom: 0.4rem;
    }
    .site-header p {
      font-size: 14px;
      opacity: 0.8;
    }

    .container {
      max-width: 900px;
      margin: 0 auto;
      padding: 1.5rem 1.25rem 3rem;
    }

    /* Cards */
    .card {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 1.25rem 1.5rem;
      margin-bottom: 1.25rem;
    }
    .card-title {
      font-size: 11px;
      font-weight: 600;
      letter-spacing: 0.08em;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 1rem;
    }

    /* Inputs */
    .input-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(175px, 1fr));
      gap: 1rem;
    }
    .input-group label {
      display: block;
      font-size: 13px;
      color: var(--muted);
      margin-bottom: 6px;
    }
    .input-group input {
      width: 100%;
      padding: 10px 12px;
      font-size: 15px;
      font-family: 'DM Sans', sans-serif;
      border: 1px solid var(--border);
      border-radius: var(--radius-sm);
      background: var(--bg);
      color: var(--text);
      transition: border-color 0.15s;
    }
    .input-group input:focus {
      outline: none;
      border-color: var(--accent);
      background: white;
    }
    .input-group input.error { border-color: var(--danger); }
    .error-msg { font-size: 11px; color: var(--danger); margin-top: 4px; }

    /* Slider */
    .slider-wrap { display: flex; align-items: center; gap: 12px; }
    .slider-wrap input[type=range] {
      flex: 1;
      accent-color: var(--accent);
      height: 4px;
    }
    .slider-label { font-size: 13px; color: var(--muted); white-space: nowrap; }
    .slider-val { font-size: 15px; font-weight: 600; color: var(--accent); min-width: 90px; text-align: right; }

    /* Scenarios */
    .scenario-row { display: flex; gap: 8px; flex-wrap: wrap; }
    .scenario-btn {
      padding: 7px 16px;
      font-size: 13px;
      font-family: 'DM Sans', sans-serif;
      border: 1px solid var(--border);
      border-radius: 999px;
      background: var(--surface2);
      color: var(--muted);
      cursor: pointer;
      transition: all 0.15s;
    }
    .scenario-btn:hover { border-color: var(--accent); color: var(--accent); }
    .scenario-btn.active { background: var(--accent); color: white; border-color: var(--accent); }

    /* Metrics */
    .metrics-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
      gap: 10px;
    }
    .metric {
      background: var(--surface2);
      border-radius: var(--radius-sm);
      padding: 12px 14px;
    }
    .metric-label { font-size: 11px; color: var(--muted); margin-bottom: 6px; text-transform: uppercase; letter-spacing: 0.05em; }
    .metric-value { font-size: 19px; font-weight: 600; color: var(--text); }
    .metric-value.profit { color: var(--accent); }
    .metric-value.loss { color: var(--danger); }

    /* Chart */
    .chart-container { position: relative; width: 100%; height: 300px; }

    /* Legend */
    .legend { display: flex; gap: 20px; margin-bottom: 12px; flex-wrap: wrap; }
    .legend-item { display: flex; align-items: center; gap: 6px; font-size: 12px; color: var(--muted); }
    .legend-dot { width: 24px; height: 3px; border-radius: 2px; }
    .legend-dot.rev { background: var(--accent); }
    .legend-dot.cost { background: var(--danger); border-top: 2px dashed var(--danger); background: none; }

    /* Insights */
    .insight {
      display: flex;
      gap: 10px;
      align-items: flex-start;
      padding: 12px 14px;
      border-radius: var(--radius-sm);
      margin-bottom: 8px;
      font-size: 13px;
      line-height: 1.6;
    }
    .insight.success { background: var(--accent-light); color: #1a3d2b; }
    .insight.danger { background: var(--danger-light); color: #7a1e15; }
    .insight.warning { background: var(--warning-light); color: #7c3a0a; }
    .insight.neutral { background: var(--surface2); color: var(--muted); }
    .insight-icon { font-size: 16px; flex-shrink: 0; margin-top: 1px; }

    /* Download */
    .download-btn {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      padding: 10px 20px;
      font-size: 13px;
      font-family: 'DM Sans', sans-serif;
      font-weight: 500;
      border: 1px solid var(--border);
      border-radius: var(--radius-sm);
      background: var(--surface);
      color: var(--text);
      cursor: pointer;
      transition: background 0.15s;
    }
    .download-btn:hover { background: var(--surface2); }

    /* Footer */
    .site-footer {
      text-align: center;
      padding: 2rem;
      font-size: 12px;
      color: var(--muted);
      border-top: 1px solid var(--border);
    }

    @media (max-width: 480px) {
      .site-header { padding: 1.5rem 1rem 1rem; }
      .card { padding: 1rem; }
      .metrics-grid { grid-template-columns: 1fr 1fr; }
      .input-grid { grid-template-columns: 1fr 1fr; }
    }
  </style>
</head>
<body>

<header class="site-header">
  <h1>Break-Even Analysis Tool</h1>
  <p>Know exactly when your business becomes profitable</p>
</header>

<div class="container">

  <!-- Inputs -->
  <div class="card">
    <div class="card-title">Your inputs</div>
    <div class="input-grid">
      <div class="input-group">
        <label>Fixed cost (₹)</label>
        <input type="number" id="fixedCost" value="100000" min="0" />
        <div class="error-msg" id="err-fixedCost"></div>
      </div>
      <div class="input-group">
        <label>Variable cost / unit (₹)</label>
        <input type="number" id="varCost" value="200" min="0" />
        <div class="error-msg" id="err-varCost"></div>
      </div>
      <div class="input-group">
        <label>Selling price / unit (₹)</label>
        <input type="number" id="price" value="500" min="0" />
        <div class="error-msg" id="err-price"></div>
      </div>
      <div class="input-group">
        <label>Expected units sold</label>
        <input type="number" id="unitsSoldInput" value="400" min="0" />
        <div class="error-msg" id="err-units"></div>
      </div>
    </div>
  </div>

  <!-- Slider -->
  <div class="card">
    <div class="card-title">Adjust units sold dynamically</div>
    <div class="slider-wrap">
      <span class="slider-label">0</span>
      <input type="range" id="unitsSlider" min="0" max="2000" step="10" value="400" />
      <span class="slider-label" id="sliderMax">2,000</span>
      <span class="slider-val" id="sliderDisplay">400 units</span>
    </div>
  </div>

  <!-- Scenarios -->
  <div class="card">
    <div class="card-title">Scenario comparison</div>
    <div class="scenario-row">
      <button class="scenario-btn active" id="btn-base" onclick="applyScenario('base')">Your inputs</button>
      <button class="scenario-btn" id="btn-best" onclick="applyScenario('best')">Best case</button>
      <button class="scenario-btn" id="btn-worst" onclick="applyScenario('worst')">Worst case</button>
    </div>
  </div>

  <!-- Results -->
  <div class="card">
    <div class="card-title">Results</div>
    <div class="metrics-grid">
      <div class="metric"><div class="metric-label">Break-even units</div><div class="metric-value" id="m-beUnits">—</div></div>
      <div class="metric"><div class="metric-label">Break-even revenue</div><div class="metric-value" id="m-beRev">—</div></div>
      <div class="metric"><div class="metric-label">Contribution margin</div><div class="metric-value" id="m-cm">—</div></div>
      <div class="metric"><div class="metric-label">Profit / Loss</div><div class="metric-value" id="m-profit">—</div></div>
      <div class="metric"><div class="metric-label">Margin ratio</div><div class="metric-value" id="m-marginRatio">—</div></div>
      <div class="metric"><div class="metric-label">Units above/below BE</div><div class="metric-value" id="m-buffer">—</div></div>
    </div>
  </div>

  <!-- Chart -->
  <div class="card">
    <div class="card-title">Break-even chart</div>
    <div class="legend">
      <div class="legend-item"><div class="legend-dot rev"></div> Revenue</div>
      <div class="legend-item"><span style="display:inline-block;width:24px;border-top:2px dashed var(--danger)"></span>&nbsp; Total Cost</div>
    </div>
    <div class="chart-container">
      <canvas id="beChart" role="img" aria-label="Line chart showing revenue and total cost against units sold, intersecting at break-even point.">Break-even chart showing revenue line and cost line.</canvas>
    </div>
  </div>

  <!-- Insights -->
  <div class="card">
    <div class="card-title">Decision insights</div>
    <div id="insights"></div>
  </div>

  <!-- Download -->
  <div class="card" style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;">
    <span style="font-size:13px;color:var(--muted)">Export your analysis as a text report</span>
    <button class="download-btn" onclick="downloadReport()">
      ↓ Download report
    </button>
  </div>

</div>

<footer class="site-footer">
  Built with Break-Even Analysis Tool &mdash; for small businesses, startups &amp; MBA students
</footer>

<script>
  let chartInstance = null;
  let savedInputs = {};

  const fmt = n => '₹' + Math.round(n).toLocaleString('en-IN');
  const fmtN = n => Math.round(n).toLocaleString('en-IN');

  function getInputs() {
    return {
      fixed: parseFloat(document.getElementById('fixedCost').value) || 0,
      varC: parseFloat(document.getElementById('varCost').value) || 0,
      price: parseFloat(document.getElementById('price').value) || 0,
      units: parseFloat(document.getElementById('unitsSlider').value) || 0,
    };
  }

  function validate(inputs) {
    let valid = true;
    ['fixedCost','varCost','price'].forEach(id => {
      document.getElementById('err-' + id).textContent = '';
      document.getElementById(id).classList.remove('error');
    });
    if (inputs.fixed < 0) { document.getElementById('err-fixedCost').textContent = 'Must be ≥ 0'; document.getElementById('fixedCost').classList.add('error'); valid = false; }
    if (inputs.varC < 0) { document.getElementById('err-varCost').textContent = 'Must be ≥ 0'; document.getElementById('varCost').classList.add('error'); valid = false; }
    if (inputs.price <= 0) { document.getElementById('err-price').textContent = 'Must be > 0'; document.getElementById('price').classList.add('error'); valid = false; }
    if (inputs.price <= inputs.varC) { document.getElementById('err-price').textContent = 'Must exceed variable cost'; document.getElementById('price').classList.add('error'); valid = false; }
    return valid;
  }

  function calculate(inputs) {
    const cm = inputs.price - inputs.varC;
    const beUnits = inputs.fixed / cm;
    const beRev = beUnits * inputs.price;
    const revenue = inputs.price * inputs.units;
    const totalCost = inputs.fixed + inputs.varC * inputs.units;
    const profit = revenue - totalCost;
    const marginRatio = inputs.price > 0 ? (cm / inputs.price) * 100 : 0;
    return { cm, beUnits, beRev, revenue, totalCost, profit, marginRatio };
  }

  function buildChartData(inputs, beUnits) {
    const maxUnits = Math.max(beUnits * 2.2, inputs.units * 1.5, 200);
    const steps = 40;
    const labels = [], revenueData = [], costData = [];
    for (let i = 0; i <= steps; i++) {
      const u = Math.round((i / steps) * maxUnits);
      labels.push(fmtN(u));
      revenueData.push(Math.round(inputs.price * u));
      costData.push(Math.round(inputs.fixed + inputs.varC * u));
    }
    return { labels, revenueData, costData, maxUnits };
  }

  function renderChart(inputs, result) {
    const { labels, revenueData, costData } = buildChartData(inputs, result.beUnits);
    const ctx = document.getElementById('beChart').getContext('2d');
    if (chartInstance) chartInstance.destroy();
    chartInstance = new Chart(ctx, {
      type: 'line',
      data: {
        labels,
        datasets: [
          { label: 'Revenue', data: revenueData, borderColor: '#2a5f3f', borderWidth: 2.5, pointRadius: 0, fill: false, tension: 0 },
          { label: 'Total Cost', data: costData, borderColor: '#c0392b', borderWidth: 2.5, pointRadius: 0, fill: false, tension: 0, borderDash: [8, 5] }
        ]
      },
      options: {
        responsive: true, maintainAspectRatio: false,
        interaction: { mode: 'index', intersect: false },
        plugins: {
          legend: { display: false },
          tooltip: {
            callbacks: {
              label: ctx => ctx.dataset.label + ': ₹' + Math.round(ctx.parsed.y).toLocaleString('en-IN'),
              title: lbls => 'Units: ' + lbls[0]
            }
          }
        },
        scales: {
          x: {
            ticks: { autoSkip: true, maxTicksLimit: 7, color: '#7a7570', font: { size: 11 } },
            grid: { color: 'rgba(0,0,0,0.06)' },
            title: { display: true, text: 'Units sold', color: '#7a7570', font: { size: 11 } }
          },
          y: {
            ticks: {
              color: '#7a7570', font: { size: 11 },
              callback: v => '₹' + (v >= 1000000 ? (v/1000000).toFixed(1)+'M' : v >= 1000 ? (v/1000).toFixed(0)+'K' : v)
            },
            grid: { color: 'rgba(0,0,0,0.06)' },
            title: { display: true, text: 'Amount (₹)', color: '#7a7570', font: { size: 11 } }
          }
        }
      }
    });
  }

  function renderInsights(inputs, result) {
    const box = document.getElementById('insights');
    const insights = [];
    const cmPct = result.marginRatio;

    if (result.profit < 0) {
      insights.push({ type: 'danger', icon: '✕', text: 'Your business is running at a loss at ' + fmtN(inputs.units) + ' units. You need to sell at least ' + fmtN(Math.ceil(result.beUnits)) + ' units to break even.' });
    } else if (result.profit === 0) {
      insights.push({ type: 'neutral', icon: '◎', text: 'You are exactly at break-even. Any additional units sold will generate profit.' });
    } else {
      insights.push({ type: 'success', icon: '✓', text: 'Profitable! You are ' + fmtN(Math.round(inputs.units - result.beUnits)) + ' units above break-even, generating ' + fmt(result.profit) + ' in profit.' });
    }

    if (result.beUnits > 5000) {
      insights.push({ type: 'warning', icon: '!', text: 'High-risk business model: you need to sell ' + fmtN(Math.ceil(result.beUnits)) + ' units before making any profit. Consider reducing fixed costs.' });
    }

    if (cmPct < 20) {
      insights.push({ type: 'warning', icon: '!', text: 'Low contribution margin (' + cmPct.toFixed(1) + '%). Consider increasing your selling price or reducing variable costs.' });
    } else if (cmPct > 60) {
      insights.push({ type: 'success', icon: '✓', text: 'Strong contribution margin (' + cmPct.toFixed(1) + '%). Your pricing strategy leaves healthy room for profit.' });
    }

    if (inputs.price < inputs.varC * 1.1) {
      insights.push({ type: 'danger', icon: '✕', text: 'Selling price is dangerously close to variable cost. A small cost increase could make every unit a loss.' });
    }

    box.innerHTML = insights.map(i => `<div class="insight ${i.type}"><span class="insight-icon">${i.icon}</span><span>${i.text}</span></div>`).join('');
  }

  function update() {
    const inputs = getInputs();
    if (!validate(inputs)) return;
    const result = calculate(inputs);

    document.getElementById('m-beUnits').textContent = fmtN(Math.ceil(result.beUnits)) + ' units';
    document.getElementById('m-beRev').textContent = fmt(result.beRev);
    document.getElementById('m-cm').textContent = fmt(result.cm) + '/unit';

    const profitEl = document.getElementById('m-profit');
    profitEl.textContent = (result.profit >= 0 ? '+' : '') + fmt(result.profit);
    profitEl.className = 'metric-value ' + (result.profit > 0 ? 'profit' : result.profit < 0 ? 'loss' : '');

    document.getElementById('m-marginRatio').textContent = result.marginRatio.toFixed(1) + '%';

    const buffer = Math.round(inputs.units - result.beUnits);
    const bufEl = document.getElementById('m-buffer');
    bufEl.textContent = (buffer >= 0 ? '+' : '') + fmtN(buffer) + ' units';
    bufEl.className = 'metric-value ' + (buffer >= 0 ? 'profit' : 'loss');

    renderChart(inputs, result);
    renderInsights(inputs, result);
  }

  function syncSliderAndInput(val) {
    document.getElementById('unitsSlider').value = val;
    document.getElementById('unitsSoldInput').value = val;
    document.getElementById('sliderDisplay').textContent = fmtN(val) + ' units';
  }

  document.getElementById('unitsSlider').addEventListener('input', function() {
    syncSliderAndInput(this.value);
    update();
  });

  document.getElementById('unitsSoldInput').addEventListener('input', function() {
    const v = parseFloat(this.value) || 0;
    if (v > parseInt(document.getElementById('unitsSlider').max)) {
      document.getElementById('unitsSlider').max = Math.ceil(v * 1.5);
      document.getElementById('sliderMax').textContent = fmtN(Math.ceil(v * 1.5));
    }
    syncSliderAndInput(v);
    update();
  });

  ['fixedCost','varCost','price'].forEach(id => {
    document.getElementById(id).addEventListener('input', () => {
      const fc = parseFloat(document.getElementById('fixedCost').value) || 0;
      const vc = parseFloat(document.getElementById('varCost').value) || 0;
      const sp = parseFloat(document.getElementById('price').value) || 0;
      if (sp > vc && sp > 0) {
        const be = Math.ceil(fc / (sp - vc));
        const newMax = Math.max(be * 3, 500);
        document.getElementById('unitsSlider').max = newMax;
        document.getElementById('sliderMax').textContent = fmtN(newMax);
      }
      update();
    });
  });

  const scenarios = {
    best: { fixed: 80000, varC: 150, price: 600, units: 600 },
    worst: { fixed: 150000, varC: 280, price: 400, units: 200 }
  };

  function applyScenario(s) {
    document.querySelectorAll('.scenario-btn').forEach(b => b.classList.remove('active'));
    document.getElementById('btn-' + s).classList.add('active');
    if (s === 'base') {
      if (savedInputs.fixed !== undefined) {
        document.getElementById('fixedCost').value = savedInputs.fixed;
        document.getElementById('varCost').value = savedInputs.varC;
        document.getElementById('price').value = savedInputs.price;
        syncSliderAndInput(savedInputs.units);
      }
    } else {
      savedInputs = getInputs();
      const sc = scenarios[s];
      document.getElementById('fixedCost').value = sc.fixed;
      document.getElementById('varCost').value = sc.varC;
      document.getElementById('price').value = sc.price;
      syncSliderAndInput(sc.units);
    }
    update();
  }

  function downloadReport() {
    const inputs = getInputs();
    const r = calculate(inputs);
    const lines = [
      'BREAK-EVEN ANALYSIS REPORT',
      '==========================',
      '',
      'INPUTS',
      'Fixed Cost:              ' + fmt(inputs.fixed),
      'Variable Cost/Unit:      ' + fmt(inputs.varC),
      'Selling Price/Unit:      ' + fmt(inputs.price),
      'Expected Units Sold:     ' + fmtN(inputs.units),
      '',
      'RESULTS',
      'Break-Even Units:        ' + fmtN(Math.ceil(r.beUnits)),
      'Break-Even Revenue:      ' + fmt(r.beRev),
      'Contribution Margin:     ' + fmt(r.cm) + ' per unit',
      'Contribution Margin %:   ' + r.marginRatio.toFixed(1) + '%',
      'Total Revenue:           ' + fmt(r.revenue),
      'Total Cost:              ' + fmt(r.totalCost),
      'Profit / Loss:           ' + (r.profit >= 0 ? '+' : '') + fmt(r.profit),
      '',
      'Generated: ' + new Date().toLocaleString('en-IN')
    ];
    const blob = new Blob([lines.join('\n')], { type: 'text/plain' });
    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob);
    a.download = 'break-even-report.txt';
    a.click();
  }

  update();
</script>
</body>
</html>
