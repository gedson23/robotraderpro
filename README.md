<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes, viewport-fit=cover">
  <title>🤖 Robô Trader PRO</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
  <script src="https://unpkg.com/lightweight-charts@4.0.1/dist/lightweight-charts.standalone.production.js"></script>
  <style>
    * { box-sizing: border-box; margin: 0; }
    body { 
      font-family: 'Segoe UI', Arial, sans-serif; 
      background: #0f172a; color: #fff; padding: 12px; padding-bottom: 40px;
      -webkit-tap-highlight-color: transparent;
    }
    h1 { color: #38bdf8; text-align: center; margin-bottom: 8px; font-size: 1.6rem; }
    .wake-lock-status {
      background: #1e293b; border-radius: 30px; padding: 6px 12px;
      display: inline-block; font-size: 12px; margin-bottom: 8px;
    }
    .chart-wrapper { background: #1e293b; border-radius: 14px; padding: 8px; margin-bottom: 12px; }
    #chart-container { width: 100%; height: 350px; }
    .toolbar { display: flex; gap: 6px; margin-bottom: 8px; align-items: center; flex-wrap: wrap; }
    .toolbar select, .toolbar button { 
      padding: 10px 8px; border-radius: 8px; border: none; 
      background: #0f172a; color: #fff; font-weight: bold; 
      cursor: pointer; font-size: 13px; min-height: 44px;
      -webkit-appearance: none;
    }
    .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 12px; }
    .card { background: #1e293b; border-radius: 14px; padding: 12px; margin-bottom: 12px; }
    .row { display: flex; justify-content: space-between; margin: 6px 0; font-size: 14px; }
    .buy { color: #22c55e; } .sell { color: #ef4444; } .wait { color: #facc15; }
    .bad { color: #ef4444; } .ok { color: #22c55e; }
    .small { opacity: 0.8; font-size: 12px; margin-top: 6px; }
    button { 
      padding: 12px 12px; border-radius: 10px; border: none; 
      font-weight: bold; cursor: pointer; transition: 0.2s; 
      min-height: 48px; font-size: 15px; touch-action: manipulation;
      background: #2d3a4f; color: #fff;
    }
    button:active { opacity: 0.8; transform: scale(0.98); }
    .btn-success { background: #22c55e; color: #fff; }
    .btn-danger { background: #ef4444; color: #fff; }
    .btn-stop { background: #dc2626; color: #fff; }
    select, input { 
      padding: 10px; border-radius: 8px; border: none; 
      background: #0f172a; color: #fff; width: 100%; 
      margin-top: 4px; font-size: 14px; min-height: 44px;
      -webkit-appearance: none;
    }
    #log, #history-log { 
      max-height: 200px; overflow-y: auto; font-family: monospace; 
      background: #0f172a; padding: 10px; border-radius: 8px; 
      font-size: 12px; line-height: 1.4;
    }
    .status-badge { display: inline-block; padding: 4px 8px; border-radius: 20px; font-size: 12px; font-weight: bold; }
    .flex-row { display: flex; gap: 6px; align-items: center; flex-wrap: wrap; }
    .trade-item { border-bottom: 1px solid #334155; padding: 8px 0; }
    .profit { color: #22c55e; } .loss { color: #ef4444; }
    .badge { background: #0f172a; padding: 4px 8px; border-radius: 12px; }
    .action-buttons { display: flex; gap: 8px; margin: 12px 0; }
    .action-buttons button { flex: 1; }
    .pnl-signal-row {
      display: flex; justify-content: space-between; align-items: center;
      background: #1e293b; border-radius: 14px; padding: 12px; margin-bottom: 12px;
    }
    .signal-text { font-size: 22px; font-weight: bold; }
    .interval-badge { background: #2d3a4f; padding: 4px 8px; border-radius: 20px; font-size: 12px; margin-left: 6px; }
    .timer-badge { background: #1e3a5f; padding: 4px 8px; border-radius: 20px; font-size: 12px; font-weight: bold; }
    .timer-footer { display: flex; justify-content: flex-end; margin-top: 6px; }
    input[type="checkbox"] {
      width: 24px; height: 24px; accent-color: #38bdf8;
      background-color: #0f172a; border: 2px solid #38bdf8;
      border-radius: 6px; margin-right: 8px; vertical-align: middle;
      -webkit-appearance: none; appearance: none; position: relative;
    }
    input[type="checkbox"]:checked {
      background-color: #38bdf8; border-color: #38bdf8;
    }
    input[type="checkbox"]:checked::after {
      content: '✓'; color: white; font-size: 18px;
      position: absolute; top: -1px; left: 3px;
    }
    label.flex-row {
      display: flex; align-items: center; font-size: 15px; padding: 4px 0;
    }
    .pause-news-btn { background: #b45309; color: #fff; }
    .reconnect-info { font-size: 12px; margin-left: 8px; opacity: 0.9; }
    .api-section { border-top: 1px solid #334155; margin-top: 10px; padding-top: 10px; }
    
    .activation-screen {
      display: flex; flex-direction: column; align-items: center; justify-content: center;
      height: 100vh; background: #0f172a; padding: 20px;
    }
    .activation-box {
      background: #1e293b; border-radius: 20px; padding: 30px; width: 100%; max-width: 400px;
    }
    .activation-box h2 { color: #38bdf8; margin-bottom: 20px; text-align: center; }
    .activation-box input {
      margin: 10px 0; padding: 14px; font-size: 16px; text-align: center; letter-spacing: 2px;
    }
    .activation-box button { width: 100%; margin-top: 15px; }
    .activation-box .small { text-align: center; margin-top: 20px; }
    .error-msg { color: #ef4444; font-size: 13px; margin-top: 5px; }
    .loading-msg { color: #facc15; font-size: 13px; margin-top: 5px; }
  </style>
</head>
<body>

<!-- TELA DE ATIVAÇÃO -->
<div id="activationScreen" class="activation-screen">
  <div class="activation-box">
    <h2>🔐 Ativar Robô Trader PRO</h2>
    <p style="text-align:center; margin-bottom:15px;">Insira o código de acesso para continuar</p>
    <input type="text" id="activationCode" placeholder="Ex: RB-12345" autocomplete="off">
    <button onclick="ativarRobo()" style="background:#38bdf8; color:#000;">ATIVAR</button>
    <p id="activationError" class="error-msg"></p>
    <p id="activationLoading" class="loading-msg"></p>
    <div class="small" style="margin-top:15px;">
      Não tem um código?<br>
      Adquira o acesso: <b>bit.ly/robo-trader-pro</b><br>
      (Substitua pelo seu link de vendas)
    </div>
  </div>
</div>

<!-- APP PRINCIPAL -->
<div id="mainApp" style="display:none;">
  <h1>🤖 Robô Trader PRO <span style="font-size:14px;">v5.6</span></h1>
  <div style="display:flex; justify-content:space-between; align-items:center;">
    <span class="wake-lock-status" id="wakelockStatus">🔋 Tela: Normal</span>
    <span style="font-size:13px;">📱 Mantenha esta aba aberta</span>
  </div>
  <div class="chart-wrapper">
    <div class="toolbar">
      <select id="exchangeSelect">
        <option value="bybit">Bybit</option>
        <option value="binance">Binance</option>
      </select>
      <select id="symbolSelect">
        <option value="BTCUSDT">BTC/USDT</option>
        <option value="ETHUSDT">ETH/USDT</option>
        <option value="BNBUSDT">BNB/USDT</option>
        <option value="SOLUSDT">SOL/USDT</option>
      </select>
      <select id="intervalSelect">
        <option value="1">1m</option><option value="3">3m</option><option value="5" selected>5m</option>
        <option value="15">15m</option><option value="30">30m</option><option value="60">1h</option>
      </select>
      <button id="refreshChartBtn">🔄</button>
      <span style="flex:1;"></span>
      <span id="chartStatus" class="status-badge wait">⏳</span>
      <span id="intervalDisplay" class="interval-badge">5m</span>
    </div>
    <div id="chart-container"></div>
    <div class="timer-footer"><span id="timerDisplay" class="timer-badge">⏱️ --:--</span></div>
  </div>
  <div class="action-buttons">
    <button class="btn-success" onclick="executarOrdemManual('BUY')">📈 COMPRAR</button>
    <button class="btn-danger" onclick="executarOrdemManual('SELL')">📉 VENDER</button>
    <button class="btn-stop" onclick="pararAutoTrade()">⛔ PARAR AUTO</button>
  </div>
  <div class="pnl-signal-row">
    <div class="pnl-box">
      <div style="font-size:14px; opacity:0.8;">PnL Atual</div>
      <span style="font-size:26px; font-weight:bold;" id="pnlDisplay">—</span>
      <span class="badge" id="pnlPercentDisplay">0.00%</span>
      <div style="margin-top:6px;">
        <div><span>USDT:</span> <b id="pnlUSDT">0.00</b></div>
        <div><span>BRL:</span> <b id="pnlBRL">R$ 0,00</b></div>
      </div>
    </div>
    <div class="signal-box">
      <div style="font-size:14px; opacity:0.8;">Sinal (EMA + RSI)</div>
      <span class="signal-text wait" id="signalDisplay">AGUARDANDO</span>
      <div class="small" id="reasonDisplay">—</div>
    </div>
  </div>
  <div class="grid-2">
    <div class="card">
      <div class="row"><span>Preço USDT</span><b id="price">--</b></div>
      <div class="row"><span>Preço BRL</span><b id="priceBRL">--</b></div>
      <div class="row"><span>EMA 9</span><b id="ema9">--</b></div>
      <div class="row"><span>EMA 21</span><b id="ema21">--</b></div>
      <div class="row"><span>RSI (14)</span><b id="rsi">--</b></div>
    </div>
    <div class="card">
      <div class="row"><span>💰 Saldo USDT</span><b id="saldoUSDT">--</b></div>
      <div class="row"><span>💰 Saldo BRL</span><b id="saldoBRL">--</b></div>
      <div class="row"><span>Posição</span><b id="position">—</b></div>
      <div class="row"><span>Qtd. Moeda</span><b id="positionQty">—</b></div>
      <div class="row"><span>Entrada (USDT)</span><b id="entryPrice">—</b></div>
    </div>
  </div>

  <div class="card">
    <div class="flex-row" style="justify-content: space-between;">
      <b style="color:#38bdf8;">⚙️ Estratégia &amp; Conta</b>
      <label class="flex-row"><input type="checkbox" id="autoTradeEnabled"> 🤖 Auto-Trade</label>
    </div>
    <div class="grid-2">
      <div><label>Qtd. Ordem (BTC)</label><input id="orderQty" value="0.001" step="0.0001"></div>
      <div><label>💵 Comprar em R$</label><input id="orderBRL" value="300"><button onclick="calcularBTCfromBRL()" style="margin-top:4px; padding:10px;">Calcular BTC</button></div>
      <div><label>Take Profit %</label><input id="tpInput" value="0.8"></div>
      <div><label>Stop Loss %</label><input id="slInput" value="0.5"></div>
      <div><label>Meta Lucro (R$)</label><input id="profitGoalBRL" value="200"></div>
      <div><label>Intervalo (s)</label><input id="intervalInput" value="15"></div>
    </div>
    <div class="grid-2" style="margin-top:8px;">
      <div><label>RSI min (entrada)</label><input id="rsiMin" value="25" type="number" step="1" min="0" max="100"></div>
      <div><label>RSI max (entrada)</label><input id="rsiMax" value="75" type="number" step="1" min="0" max="100"></div>
      <div><label>Trailing Stop %</label><input id="trailingStop" value="0.3" step="0.1"></div>
      <div><label>Ativar Trailing em %</label><input id="trailingActivation" value="0.5" step="0.1"></div>
    </div>
    <label class="flex-row"><input type="checkbox" id="useTrailingStop" checked> 🛡️ Usar Trailing Stop</label>
    
    <div class="flex-row" style="margin-top:10px; justify-content:space-between;">
      <label class="flex-row"><input type="checkbox" id="safeNewsMode"> 🛡️ Modo Safe News</label>
      <button onclick="pausarPorNoticias()" class="pause-news-btn" style="padding:8px 12px; min-height:40px;">⏸️ Pausar 30min</button>
    </div>
    
    <div class="flex-row" style="margin-top:10px;">
      <span id="connStatus" class="status-badge wait">⏳ Desconectado</span>
      <button id="connectBtn" onclick="toggleConnection()" style="flex:1;">🔌 Conectar</button>
      <span id="reconnectAttemptsDisplay" class="reconnect-info"></span>
    </div>

    <div class="api-section">
      <div class="small">🔐 API da <span id="exchangeLabel">Bybit</span></div>
      <input id="apiKey" placeholder="API Key" autocomplete="off">
      <input id="apiSecret" type="password" placeholder="API Secret" autocomplete="off">
      <label class="flex-row"><input type="checkbox" id="testnetMode" checked> 🧪 Testnet</label>
      <button onclick="atualizarSaldoReal()" style="margin-top:10px;">💰 Atualizar Saldo da Conta</button>
    </div>
    <div class="small" style="margin-top:8px;">⚠️ Mantenha a tela ligada para operar 24h.</div>
  </div>

  <div class="card">
    <div class="flex-row" style="justify-content: space-between;">
      <b style="color:#38bdf8;">📊 Histórico Realizado</b>
      <span id="totalPnLRealizado" style="font-weight: bold;">R$ 0,00</span>
    </div>
    <div id="history-log"><div class="small">Nenhuma operação fechada.</div></div>
    <button onclick="limparHistoricoTrades()" style="margin-top:8px;">🗑️ Limpar Histórico</button>
  </div>

  <div class="card">
    <b>📜 Log</b>
    <div id="log"></div>
    <button onclick="document.getElementById('log').innerHTML=''" style="margin-top:8px;">🗑️ Limpar Log</button>
  </div>
</div>

<script>
  // ========== SISTEMA DE ATIVAÇÃO (LISTA LOCAL) ==========
  const CODIGOS_VALIDOS = [
    "RB-00001", "RB-00002", "RB-00003", "RB-00004", "RB-00005",
    "RB-00006", "RB-00007", "RB-00008", "RB-00009", "RB-00010"
  ];

  function verificarCodigo(codigo) {
    return CODIGOS_VALIDOS.includes(codigo);
  }

  function verificarAtivacaoLocal() {
    return localStorage.getItem('robo_ativado') === 'true';
  }

  function mostrarApp() {
    document.getElementById('activationScreen').style.display = 'none';
    document.getElementById('mainApp').style.display = 'block';
    iniciarRobo();
  }

  function ativarRobo() {
    const codigo = document.getElementById('activationCode').value.trim();
    const errorEl = document.getElementById('activationError');
    const loadingEl = document.getElementById('activationLoading');
    errorEl.innerText = '';
    loadingEl.innerText = 'Verificando...';

    if (!codigo) {
      errorEl.innerText = 'Digite o código.';
      loadingEl.innerText = '';
      return;
    }

    if (verificarCodigo(codigo)) {
      localStorage.setItem('robo_ativado', 'true');
      mostrarApp();
    } else {
      errorEl.innerText = 'Código inválido.';
      loadingEl.innerText = '';
    }
  }

  if (verificarAtivacaoLocal()) mostrarApp();

  // ========== ROBÔ TRADER PRO ==========
  function iniciarRobo() {
    (function(){
      "use strict";

      let wakeLock = null;
      async function requestWakeLock() {
        try {
          if ('wakeLock' in navigator) {
            wakeLock = await navigator.wakeLock.request('screen');
            document.getElementById('wakelockStatus').innerHTML = '🔋 Tela: Sempre ligada ✅';
            log('🔋 Wake Lock ativado');
            wakeLock.addEventListener('release', () => {
              document.getElementById('wakelockStatus').innerHTML = '🔋 Tela: Normal';
              log('⚠️ Wake Lock liberado');
            });
          } else {
            document.getElementById('wakelockStatus').innerHTML = '🔋 Wake Lock não suportado';
          }
        } catch (err) { document.getElementById('wakelockStatus').innerHTML = '🔋 Wake Lock negado'; }
      }
      document.addEventListener('visibilitychange', () => {
        if (document.visibilityState === 'visible') requestWakeLock();
      });
      requestWakeLock();

      let series = [], ws = null, chart = null, candleSeries = null;
      let ema9Series, ema21Series, ema50Series, markers = [];
      let currentSymbol = 'BTCUSDT', currentInterval = '5';
      let currentExchange = 'bybit', currentPosition = null;
      let lastOrderTime = 0, tradeHistory = [];
      let saldoUSDT = 10000, qtyAtivo = 0, usdBrlRate = 5.20;
      let autoTradeEnabled = false, pauseUntil = 0, cooldownUntil = 0;
      let trailingActive = false, trailingHighest = Infinity, trailingLowest = 0;
      let STRATEGY = { orderQty: 0.001, tp: 0.008, sl: 0.005, minOrderInterval: 15, profitGoalBRL: 200,
                       rsiMin: 25, rsiMax: 75, trailingStop: 0.003, trailingActivation: 0.005, useTrailing: true };
      const STORAGE_KEY = 'roboTraderPro_v5.6';
      let wsReconnectTimer = null, reconnectAttempts = 0, manualDisconnect = false;

      function salvarEstado() {
        const estado = {
          saldoUSDT, qtyAtivo, currentPosition, tradeHistory,
          autoTradeEnabled: document.getElementById('autoTradeEnabled').checked,
          safeNewsMode: document.getElementById('safeNewsMode').checked,
          exchange: currentExchange, symbol: currentSymbol, interval: currentInterval,
          testnet: document.getElementById('testnetMode').checked,
          strategy: {
            orderQty: document.getElementById('orderQty').value,
            tp: document.getElementById('tpInput').value,
            sl: document.getElementById('slInput').value,
            profitGoalBRL: document.getElementById('profitGoalBRL').value,
            minOrderInterval: document.getElementById('intervalInput').value,
            rsiMin: document.getElementById('rsiMin').value,
            rsiMax: document.getElementById('rsiMax').value,
            trailingStop: document.getElementById('trailingStop').value,
            trailingActivation: document.getElementById('trailingActivation').value,
            useTrailing: document.getElementById('useTrailingStop').checked
          }
        };
        localStorage.setItem(STORAGE_KEY, JSON.stringify(estado));
      }

      function carregarEstado() {
        const saved = localStorage.getItem(STORAGE_KEY);
        if (!saved) return false;
        try {
          const e = JSON.parse(saved);
          saldoUSDT = e.saldoUSDT || 10000; qtyAtivo = e.qtyAtivo || 0;
          currentPosition = e.currentPosition || null; tradeHistory = e.tradeHistory || [];
          autoTradeEnabled = e.autoTradeEnabled || false;
          document.getElementById('autoTradeEnabled').checked = autoTradeEnabled;
          document.getElementById('safeNewsMode').checked = e.safeNewsMode || false;
          if (e.exchange) currentExchange = e.exchange;
          if (e.symbol) currentSymbol = e.symbol;
          if (e.interval) currentInterval = e.interval;
          document.getElementById('exchangeSelect').value = currentExchange;
          document.getElementById('symbolSelect').value = currentSymbol;
          document.getElementById('intervalSelect').value = currentInterval;
          document.getElementById('intervalDisplay').innerText = currentInterval + (currentInterval==='60'?'h':'m');
          document.getElementById('testnetMode').checked = e.testnet !== false;
          if (e.strategy) {
            document.getElementById('orderQty').value = e.strategy.orderQty || 0.001;
            document.getElementById('tpInput').value = e.strategy.tp || 0.8;
            document.getElementById('slInput').value = e.strategy.sl || 0.5;
            document.getElementById('profitGoalBRL').value = e.strategy.profitGoalBRL || 200;
            document.getElementById('intervalInput').value = e.strategy.minOrderInterval || 15;
            document.getElementById('rsiMin').value = e.strategy.rsiMin || 25;
            document.getElementById('rsiMax').value = e.strategy.rsiMax || 75;
            document.getElementById('trailingStop').value = e.strategy.trailingStop || 0.3;
            document.getElementById('trailingActivation').value = e.strategy.trailingActivation || 0.5;
            document.getElementById('useTrailingStop').checked = e.strategy.useTrailing !== false;
          }
          atualizarUIExchange(); log('📂 Estado restaurado.'); return true;
        } catch (err) { return false; }
      }

      function atualizarUIExchange() {
        document.getElementById('exchangeLabel').innerText = currentExchange === 'bybit' ? 'Bybit' : 'Binance';
        document.getElementById('apiKey').placeholder = currentExchange === 'bybit' ? 'API Key Bybit' : 'API Key Binance';
        document.getElementById('apiSecret').placeholder = currentExchange === 'bybit' ? 'API Secret Bybit' : 'API Secret Binance';
      }

      function log(msg) {
        const el = document.getElementById('log');
        el.innerHTML = `[${new Date().toLocaleTimeString()}] ${msg}<br>` + el.innerHTML;
        if (el.children.length > 50) el.lastChild.remove();
      }
      function setText(id, val) { const el = document.getElementById(id); if (el) el.innerText = val; }
      function formatBRL(usdt) { return (usdt * usdBrlRate).toFixed(2); }

      async function atualizarCotacaoBRL() {
        try {
          const resp = await fetch('https://economia.awesomeapi.com.br/last/USD-BRL');
          usdBrlRate = parseFloat((await resp.json()).USDBRL.bid);
        } catch (e) {}
        atualizarSaldoUI();
      }

      function atualizarSaldoUI() {
        setText('saldoUSDT', saldoUSDT.toFixed(2));
        setText('saldoBRL', `R$ ${formatBRL(saldoUSDT)}`);
        const price = series.length ? series[series.length-1].close : 0;
        setText('priceBRL', price ? `R$ ${(price * usdBrlRate).toFixed(2)}` : '--');
      }

      function initChart() {
        const container = document.getElementById('chart-container');
        container.innerHTML = '';
        chart = LightweightCharts.createChart(container, {
          width: container.clientWidth, height: 350,
          layout: { background: { color: '#1e293b' }, textColor: '#cbd5e1' },
          grid: { vertLines: { color: '#2d3a4f' }, horzLines: { color: '#2d3a4f' } },
          timeScale: { timeVisible: true, secondsVisible: false },
        });
        candleSeries = chart.addCandlestickSeries({
          upColor: '#22c55e', downColor: '#ef4444', borderUpColor: '#22c55e',
          borderDownColor: '#ef4444', wickUpColor: '#22c55e', wickDownColor: '#ef4444',
        });
        ema9Series = chart.addLineSeries({ color: '#fbbf24', lineWidth: 1 });
        ema21Series = chart.addLineSeries({ color: '#60a5fa', lineWidth: 1 });
        ema50Series = chart.addLineSeries({ color: '#f472b6', lineWidth: 1 });
        window.addEventListener('resize', () => chart.applyOptions({ width: container.clientWidth }));
      }

      function adicionarMarcador(side) {
        if (!candleSeries || !series.length) return;
        markers.push({
          time: series[series.length-1].time,
          position: side === 'BUY' ? 'belowBar' : 'aboveBar',
          color: side === 'BUY' ? '#22c55e' : '#ef4444',
          shape: side === 'BUY' ? 'arrowUp' : 'arrowDown', text: side, size: 2
        });
        candleSeries.setMarkers(markers);
      }

      function atualizarLinhasEMA() {
        if (!series.length) return;
        const closes = series.map(c => c.close);
        const calcEMA = (arr, period) => {
          if (arr.length < period) return [];
          const k = 2 / (period + 1);
          const res = [];
          let ema = arr.slice(0, period).reduce((a,b)=>a+b,0)/period;
          for (let i = period; i < arr.length; i++) {
            ema = arr[i] * k + ema * (1 - k);
            res.push({ time: series[i].time, value: ema });
          }
          return res;
        };
        ema9Series.setData(calcEMA(closes, 9));
        ema21Series.setData(calcEMA(closes, 21));
        ema50Series.setData(calcEMA(closes, 50));
      }

      function temAPI() { return document.getElementById('apiKey').value.trim() !== ''; }

      function exchangeBaseUrl() {
        const testnet = document.getElementById('testnetMode').checked;
        return currentExchange === 'bybit'
          ? (testnet ? 'https://api-testnet.bybit.com' : 'https://api.bybit.com')
          : (testnet ? 'https://testnet.binance.vision' : 'https://api.binance.com');
      }

      function signAndHeaders(method, endpoint, body) {
        const apiKey = document.getElementById('apiKey').value.trim();
        const apiSecret = document.getElementById('apiSecret').value.trim();
        if (!apiKey || !apiSecret) throw new Error('API não configurada');
        if (currentExchange === 'bybit') {
          const ts = Date.now().toString();
          const bodyStr = body ? JSON.stringify(body) : '';
          const sign = CryptoJS.HmacSHA256(ts + apiSecret + '5000' + bodyStr, apiSecret).toString(CryptoJS.enc.Hex);
          return {
            base: exchangeBaseUrl(),
            headers: {
              'X-BAPI-API-KEY': apiKey, 'X-BAPI-TIMESTAMP': ts,
              'X-BAPI-SIGN': sign, 'X-BAPI-RECV-WINDOW': '5000', 'Content-Type': 'application/json'
            },
            body: bodyStr || undefined
          };
        } else {
          let qs = '';
          if (body) qs = Object.keys(body).map(k => `${k}=${body[k]}`).join('&');
          const ts = Date.now();
          const payload = qs ? `${qs}&timestamp=${ts}` : `timestamp=${ts}`;
          const sign = CryptoJS.HmacSHA256(payload, apiSecret).toString(CryptoJS.enc.Hex);
          return {
            base: exchangeBaseUrl(),
            headers: { 'X-MBX-APIKEY': apiKey, 'Content-Type': 'application/x-www-form-urlencoded' },
            body: `${payload}&signature=${sign}`
          };
        }
      }

      async function exchangeRequest(endpoint, method='GET', body=null) {
        const cfg = signAndHeaders(method, endpoint, body);
        const url = cfg.base + endpoint;
        const opt = { method, headers: cfg.headers };
        if (cfg.body && method !== 'GET') opt.body = cfg.body;
        else if (cfg.body && method === 'GET') {
          const resp = await fetch(`${url}?${cfg.body}`, { headers: cfg.headers });
          const data = await resp.json();
          if (data.code && data.code < 0) throw new Error(data.msg);
          if (data.retCode && data.retCode !== 0) throw new Error(data.retMsg);
          return data;
        }
        const resp = await fetch(url, opt);
        const data = await resp.json();
        if (currentExchange === 'bybit') {
          if (data.retCode !== 0) throw new Error(data.retMsg);
          return data.result;
        } else {
          if (data.code && data.code < 0) throw new Error(data.msg);
          return data;
        }
      }

      async function atualizarSaldoReal() {
        if (!temAPI()) { log('Configure API'); return; }
        try {
          if (currentExchange === 'bybit') {
            const info = await exchangeRequest('/v5/account/wallet-balance?accountType=UNIFIED');
            saldoUSDT = parseFloat(info.list[0].coin.find(c=>c.coin==='USDT')?.walletBalance || 0);
            const pos = await exchangeRequest('/v5/position/list?category=spot&symbol='+currentSymbol);
            if (pos.list?.length) {
              const p = pos.list[0];
              currentPosition = { side: p.side==='Buy'?'BUY':'SELL', qty: +p.size, entryPrice: +p.avgPrice };
              qtyAtivo = p.side==='Buy' ? +p.size : -+p.size;
            } else { currentPosition = null; qtyAtivo = 0; }
          } else {
            const info = await exchangeRequest('/api/v3/account', 'GET');
            saldoUSDT = parseFloat(info.balances.find(b=>b.asset==='USDT')?.free || 0);
            currentPosition = null; qtyAtivo = 0;
            log('Saldo Binance carregado (posição spot não rastreada).');
          }
          atualizarSaldoUI(); renderPosition(); salvarEstado();
          log(`Saldo real: ${saldoUSDT.toFixed(2)} USDT`);
        } catch (e) { log('Erro saldo: ' + e.message); }
      }

      async function placeOrderReal(side, qty) {
        if (currentExchange === 'bybit') {
          await exchangeRequest('/v5/order/create', 'POST', {
            category: 'spot', symbol: currentSymbol, side, orderType: 'Market', qty: qty.toString()
          });
        } else {
          await exchangeRequest('/api/v3/order', 'POST', {
            symbol: currentSymbol, side: side==='BUY'?'BUY':'SELL', type: 'MARKET', quantity: qty.toString()
          });
        }
        log(`✅ Ordem ${side}`);
        adicionarMarcador(side);
        const price = series.length ? series[series.length-1].close : 0;
        if (side === 'BUY' || side === 'Buy') simularCompra(price, qty);
        else simularVenda(price, qty);
        setTimeout(atualizarSaldoReal, 2000);
      }

      async function fecharPosicaoReal() {
        if (!currentPosition) return;
        const side = currentPosition.side === 'BUY'
          ? (currentExchange==='bybit'?'Sell':'SELL')
          : (currentExchange==='bybit'?'Buy':'BUY');
        await placeOrderReal(side, currentPosition.qty);
      }

      function resetTrailing() {
        trailingActive = false; trailingHighest = 0; trailingLowest = Infinity;
      }

      function simularCompra(price, qty) {
        if (!currentPosition) {
          if (saldoUSDT < price*qty) { log('❌ Saldo insuficiente'); return false; }
          saldoUSDT -= price*qty; qtyAtivo = qty;
          currentPosition = { side: 'BUY', qty, entryPrice: price };
          adicionarMarcador('BUY'); log(`🟢 ABERTURA LONG ${qty} a ${price.toFixed(2)}`);
          resetTrailing(); trailingHighest = price;
        } else if (currentPosition.side === 'SELL') {
          const lucro = (currentPosition.entryPrice - price) * currentPosition.qty;
          saldoUSDT += currentPosition.qty * currentPosition.entryPrice + lucro;
          adicionarTradeHistorico({ tipo:'FECHAR SHORT', qtd: currentPosition.qty, entrada: currentPosition.entryPrice, saida: price, lucro, lucroPercent: (lucro/(currentPosition.qty*currentPosition.entryPrice))*100, data:new Date() });
          adicionarMarcador('BUY'); log(`🔵 FECHAMENTO SHORT +${lucro.toFixed(2)} USDT`);
          qtyAtivo = 0; currentPosition = null; cooldownUntil = Date.now()+30000; resetTrailing();
        } else {
          if (saldoUSDT < price*qty) { log('❌ Saldo insuficiente'); return false; }
          saldoUSDT -= price*qty;
          const total = currentPosition.qty*currentPosition.entryPrice + price*qty;
          currentPosition.qty += qty; currentPosition.entryPrice = total / currentPosition.qty;
          qtyAtivo = currentPosition.qty; adicionarMarcador('BUY'); log(`🟢 AUMENTO LONG +${qty}`);
          trailingHighest = Math.max(trailingHighest, price);
        }
        atualizarSaldoUI(); renderPosition(); salvarEstado(); return true;
      }

      function simularVenda(price, qty) {
        if (!currentPosition) {
          const garantia = price*qty*0.5;
          if (saldoUSDT < garantia) { log('❌ Saldo insuficiente'); return false; }
          saldoUSDT -= garantia; qtyAtivo = -qty;
          currentPosition = { side:'SELL', qty, entryPrice: price };
          adicionarMarcador('SELL'); log(`🔴 ABERTURA SHORT ${qty} a ${price.toFixed(2)}`);
          resetTrailing(); trailingLowest = price;
        } else if (currentPosition.side === 'BUY') {
          const lucro = (price - currentPosition.entryPrice) * currentPosition.qty;
          saldoUSDT += price * currentPosition.qty;
          adicionarTradeHistorico({ tipo:'FECHAR LONG', qtd:currentPosition.qty, entrada:currentPosition.entryPrice, saida:price, lucro, lucroPercent:((price-currentPosition.entryPrice)/currentPosition.entryPrice)*100, data:new Date() });
          adicionarMarcador('SELL'); log(`🔴 FECHAMENTO LONG +${lucro.toFixed(2)} USDT`);
          qtyAtivo = 0; currentPosition = null; cooldownUntil = Date.now()+30000; resetTrailing();
        } else {
          const garantia = price*qty*0.5;
          if (saldoUSDT < garantia) { log('❌ Saldo insuficiente'); return false; }
          saldoUSDT -= garantia;
          const totalQty = currentPosition.qty + qty;
          const totalVal = currentPosition.qty*currentPosition.entryPrice + qty*price;
          currentPosition.qty = totalQty; currentPosition.entryPrice = totalVal/totalQty;
          qtyAtivo = -currentPosition.qty; adicionarMarcador('SELL'); log(`🔴 AUMENTO SHORT +${qty}`);
          trailingLowest = Math.min(trailingLowest, price);
        }
        atualizarSaldoUI(); renderPosition(); salvarEstado(); verificarMetaLucro(); return true;
      }

      async function executarOrdem(side) {
        STRATEGY.orderQty = +document.getElementById('orderQty').value || 0.001;
        STRATEGY.tp = +document.getElementById('tpInput').value/100;
        STRATEGY.sl = +document.getElementById('slInput').value/100;
        STRATEGY.minOrderInterval = +document.getElementById('intervalInput').value;
        STRATEGY.rsiMin = +document.getElementById('rsiMin').value;
        STRATEGY.rsiMax = +document.getElementById('rsiMax').value;
        STRATEGY.trailingStop = +document.getElementById('trailingStop').value/100;
        STRATEGY.trailingActivation = +document.getElementById('trailingActivation').value/100;
        STRATEGY.useTrailing = document.getElementById('useTrailingStop').checked;
        const price = series.length ? series[series.length-1].close : 0;
        if (!price) { log('Preço indisponível'); return; }
        if (currentPosition) {
          const close = (currentPosition.side==='BUY' && side==='SELL') || (currentPosition.side==='SELL' && side==='BUY');
          if (close) {
            if (temAPI()) await fecharPosicaoReal();
            else side==='BUY' ? simularCompra(price, currentPosition.qty) : simularVenda(price, currentPosition.qty);
            lastOrderTime = Date.now(); return;
          }
        }
        const qty = STRATEGY.orderQty;
        if (temAPI()) await placeOrderReal(side, qty);
        else side==='BUY' ? simularCompra(price, qty) : simularVenda(price, qty);
        lastOrderTime = Date.now();
      }
      window.executarOrdemManual = (side) => executarOrdem(side);

      function ema(arr,n){ if(arr.length<n) return null; const k=2/(n+1); let e=arr.slice(0,n).reduce((a,b)=>a+b,0)/n; for(let i=n;i<arr.length;i++) e = arr[i]*k + e*(1-k); return e; }
      function rsi(closes,n=14){ if(closes.length<n+1) return null; let gains=0,losses=0; for(let i=closes.length-n;i<closes.length;i++){ const diff=closes[i]-closes[i-1]; if(diff>=0) gains+=diff; else losses-=diff; } const avgGain=gains/n, avgLoss=losses/n; if(avgLoss===0) return 100; return 100-(100/(1+(avgGain/avgLoss))); }

      function analyze() {
        if(series.length < 60) { setText('reasonDisplay','Aguardando dados (min 60 velas)'); return {signal:'AGUARDAR'}; }
        const closes = series.map(c=>c.close), price = closes[closes.length-1];
        const e9 = ema(closes,9), e21 = ema(closes,21), _rsi = rsi(closes,14);
        if(!e9||!e21||_rsi===null) { setText('reasonDisplay','Indicadores incompletos'); return {signal:'AGUARDAR'}; }
        const prev9 = ema(closes.slice(0,-1),9), prev21 = ema(closes.slice(0,-1),21);
        const cruzouCima = (prev9<=prev21) && (e9>e21), cruzouBaixo = (prev9>=prev21) && (e9<e21);
        let signal='AGUARDAR', reason='';
        const rsiOk = _rsi >= STRATEGY.rsiMin && _rsi <= STRATEGY.rsiMax;
        if (!currentPosition) {
          if (cruzouCima && rsiOk) { signal='COMPRAR'; reason=`EMA9↑21 | RSI ${_rsi.toFixed(1)}`; }
          else if (cruzouBaixo && rsiOk) { signal='VENDER'; reason=`EMA9↓21 | RSI ${_rsi.toFixed(1)}`; }
          else reason = `EMA9:${e9.toFixed(2)} EMA21:${e21.toFixed(2)} RSI:${_rsi.toFixed(1)}`;
        } else reason = `Posição aberta (${currentPosition.side})`;
        setText('ema9', e9.toFixed(2)); setText('ema21', e21.toFixed(2)); setText('rsi', _rsi.toFixed(1));
        setText('price', price.toFixed(2)); setText('priceBRL', `R$ ${(price*usdBrlRate).toFixed(2)}`);
        setText('reasonDisplay', reason);
        return {signal, reason, ema9:e9, ema21:e21, _rsi, price};
      }

      function analisarEAtualizarUI() {
        if (!series.length) return;
        const a = analyze();
        const el = document.getElementById('signalDisplay');
        el.className = 'signal-text ' + (a.signal==='COMPRAR'?'buy':a.signal==='VENDER'?'sell':'wait');
        el.innerText = a.signal;
        renderPosition();
        if (autoTradeEnabled && a.price) checkAndExecuteTrade(a);
      }

      async function checkAndExecuteTrade(analise) {
        if (!autoTradeEnabled) return;
        const now = Date.now();
        if (now < pauseUntil || now < cooldownUntil) return;
        const {signal, price, _rsi} = analise;
        if (document.getElementById('safeNewsMode').checked) {
          if (_rsi<30||_rsi>70) return;
          if (series.length>=3) {
            const ult = series.slice(-3);
            if (Math.max(...ult.map(c=>(c.high-c.low)/c.low)) > 0.025) return;
          }
        }
        if (currentPosition) {
          let pnl;
          if (currentPosition.side==='BUY') {
            pnl = (price - currentPosition.entryPrice)/currentPosition.entryPrice;
            if (price>trailingHighest) trailingHighest=price;
            const lucro = (trailingHighest-currentPosition.entryPrice)/currentPosition.entryPrice;
            if (STRATEGY.useTrailing && lucro>=STRATEGY.trailingActivation) trailingActive=true;
            if (trailingActive && price <= trailingHighest*(1-STRATEGY.trailingStop)) { await executarOrdem('SELL'); return; }
          } else {
            pnl = (currentPosition.entryPrice - price)/currentPosition.entryPrice;
            if (price<trailingLowest) trailingLowest=price;
            const lucro = (currentPosition.entryPrice-trailingLowest)/currentPosition.entryPrice;
            if (STRATEGY.useTrailing && lucro>=STRATEGY.trailingActivation) trailingActive=true;
            if (trailingActive && price >= trailingLowest*(1+STRATEGY.trailingStop)) { await executarOrdem('BUY'); return; }
          }
          if (pnl >= STRATEGY.tp) { await executarOrdem(currentPosition.side==='BUY'?'SELL':'BUY'); return; }
          if (pnl <= -STRATEGY.sl) { await executarOrdem(currentPosition.side==='BUY'?'SELL':'BUY'); return; }
          return;
        }
        if (now - lastOrderTime < STRATEGY.minOrderInterval*1000) return;
        if (signal==='COMPRAR') await executarOrdem('BUY');
        else if (signal==='VENDER') await executarOrdem('SELL');
      }

      function renderPosition() {
        const pnlEl = document.getElementById('pnlDisplay'), badge = document.getElementById('pnlPercentDisplay');
        if (currentPosition) {
          setText('position', currentPosition.side==='BUY'?'📈 LONG':'📉 SHORT');
          setText('positionQty', Math.abs(currentPosition.qty).toFixed(6));
          setText('entryPrice', currentPosition.entryPrice.toFixed(2));
          const price = series.length?series[series.length-1].close:0;
          let pnlUSDT=0, pnlPercent=0;
          if (currentPosition.side==='BUY') {
            pnlUSDT = (price-currentPosition.entryPrice)*currentPosition.qty;
            pnlPercent = ((price-currentPosition.entryPrice)/currentPosition.entryPrice)*100;
          } else {
            pnlUSDT = (currentPosition.entryPrice-price)*currentPosition.qty;
            pnlPercent = ((currentPosition.entryPrice-price)/currentPosition.entryPrice)*100;
          }
          const cor = pnlPercent>=0 ? '#22c55e' : '#ef4444';
          pnlEl.style.color = cor; pnlEl.innerText = `${pnlPercent>=0?'+':''}${pnlPercent.toFixed(2)}%`;
          badge.innerText = `${pnlPercent>=0?'+':''}${pnlPercent.toFixed(2)}%`; badge.style.color = cor;
          setText('pnlUSDT', `${pnlUSDT>=0?'+':''}${pnlUSDT.toFixed(2)} USDT`);
          setText('pnlBRL', `R$ ${(pnlUSDT*usdBrlRate).toFixed(2)}`);
        } else {
          setText('position','—'); setText('positionQty','—'); setText('entryPrice','—');
          pnlEl.style.color='#fff'; pnlEl.innerText='—'; badge.innerText='0.00%'; badge.style.color='#fff';
          setText('pnlUSDT','0.00 USDT'); setText('pnlBRL','R$ 0,00');
        }
      }

      function adicionarTradeHistorico(t) { tradeHistory.unshift(t); atualizarPainelHistorico(); salvarEstado(); verificarMetaLucro(); }

      function atualizarPainelHistorico() {
        const cont = document.getElementById('history-log'), totalEl = document.getElementById('totalPnLRealizado');
        if (!tradeHistory.length) { cont.innerHTML='<div class="small">Nenhuma operação fechada.</div>'; totalEl.innerText='R$ 0,00'; return; }
        let total=0, html='';
        tradeHistory.forEach(t=>{
          total+=t.lucro; const cls = t.lucro>=0?'profit':'loss';
          html += `<div class="trade-item"><b>${t.tipo}</b> ${t.qtd.toFixed(6)}<br>Entrada ${t.entrada.toFixed(2)} → Saída ${t.saida.toFixed(2)}<br><span class="${cls}">${t.lucro>=0?'+':''}${t.lucro.toFixed(2)} USDT (${t.lucroPercent.toFixed(2)}%)</span><br><small>BRL: R$ ${(t.lucro*usdBrlRate).toFixed(2)} | ${new Date(t.data).toLocaleString()}</small></div>`;
        });
        cont.innerHTML = html;
        totalEl.innerText = `R$ ${(total*usdBrlRate).toFixed(2)} (${total.toFixed(2)} USDT)`;
      }

      function verificarMetaLucro() {
        const meta = +document.getElementById('profitGoalBRL').value || 200;
        const lucroBRL = tradeHistory.reduce((acc,t)=>acc + t.lucro*usdBrlRate, 0);
        if (lucroBRL >= meta && autoTradeEnabled) {
          document.getElementById('autoTradeEnabled').checked = false;
          autoTradeEnabled = false; salvarEstado();
          log(`🏁 Meta de R$ ${meta.toFixed(2)} atingida! Auto-Trade pausado.`);
          pararConexaoWebSocket(true);
        }
      }

      window.limparHistoricoTrades = () => { tradeHistory=[]; atualizarPainelHistorico(); salvarEstado(); };
      window.calcularBTCfromBRL = () => {
        const brl = +document.getElementById('orderBRL').value;
        const price = series.length?series[series.length-1].close:0;
        if(price) document.getElementById('orderQty').value = (brl/usdBrlRate/price).toFixed(6);
      };

      function pararConexaoWebSocket(manualmente=true) {
        manualDisconnect = manualDisconnect;
        if (wsReconnectTimer) { clearTimeout(wsReconnectTimer); wsReconnectTimer = null; }
        if (ws) { ws.close(); ws = null; }
        reconnectAttempts = 0;
        document.getElementById('reconnectAttemptsDisplay').innerText = '';
        setText('connStatus', '⏹️ Desconectado');
        document.getElementById('connectBtn').innerHTML = '🔌 Conectar';
      }

      window.pararAutoTrade = () => {
        document.getElementById('autoTradeEnabled').checked = false;
        autoTradeEnabled = false; salvarEstado();
        log('⛔ Auto-Trade DESATIVADO manualmente.');
        pararConexaoWebSocket(true);
      };
      window.pausarPorNoticias = () => { pauseUntil = Date.now()+30*60*1000; log('⏸️ Pausado por 30 min'); };

      function getWsUrl() {
        return currentExchange==='bybit'
          ? 'wss://stream.bybit.com/v5/public/spot'
          : `wss://stream.binance.com:9443/ws/${currentSymbol.toLowerCase()}@kline_${currentInterval}`;
      }
      function subscribeWs() {
        if (!ws || ws.readyState!==WebSocket.OPEN) return;
        if (currentExchange==='bybit') ws.send(JSON.stringify({op:'subscribe', args:[`kline.${currentInterval}.${currentSymbol}`]}));
      }

      async function fetchHistoricalKlines() {
        setText('chartStatus','⏳');
        try {
          if (currentExchange==='bybit') {
            const url = `${exchangeBaseUrl()}/v5/market/kline?category=spot&symbol=${currentSymbol}&interval=${currentInterval}&limit=200`;
            const data = await (await fetch(url)).json();
            if(data.retCode===0) series = data.result.list.reverse().map(k=>({ time: Math.floor(+k[0]/1000), open:+k[1], high:+k[2], low:+k[3], close:+k[4] }));
          } else {
            const imap={'1':'1m','3':'3m','5':'5m','15':'15m','30':'30m','60':'1h'};
            const url = `https://api.binance.com/api/v3/klines?symbol=${currentSymbol}&interval=${imap[currentInterval]}&limit=200`;
            series = (await (await fetch(url)).json()).map(k=>({ time:Math.floor(k[0]/1000), open:+k[1], high:+k[2], low:+k[3], close:+k[4] }));
          }
          candleSeries.setData(series); setText('chartStatus','✅');
        } catch(e) { setText('chartStatus','❌'); }
        atualizarLinhasEMA(); analisarEAtualizarUI(); iniciarTimerVela();
      }

      function updateRealtime(kline) {
        const t = Math.floor(+(kline.start||kline.t)/1000);
        const c = { time:t, open:+kline.open, high:+kline.high, low:+kline.low, close:+kline.close };
        candleSeries.update(c);
        const last = series[series.length-1];
        if (last && last.time===t) series[series.length-1] = c;
        else { series.push(c); if(series.length>200) series.shift(); }
        atualizarLinhasEMA(); analisarEAtualizarUI();
      }

      function conectarWebSocket() {
        if (ws) { ws.close(); ws = null; }
        if (wsReconnectTimer) { clearTimeout(wsReconnectTimer); wsReconnectTimer = null; }
        currentExchange = document.getElementById('exchangeSelect').value;
        currentSymbol = document.getElementById('symbolSelect').value;
        currentInterval = document.getElementById('intervalSelect').value;
        document.getElementById('intervalDisplay').innerText = currentInterval + (currentInterval==='60'?'h':'m');
        atualizarUIExchange(); if (!chart) initChart();
        fetchHistoricalKlines(); setText('connStatus','⏳ Conectando...'); manualDisconnect = false;
        ws = new WebSocket(getWsUrl());
        ws.onopen = () => {
          setText('connStatus','🟢 ONLINE'); subscribeWs();
          log(`✅ Conectado (${currentExchange.toUpperCase()})`); reconnectAttempts=0;
          document.getElementById('reconnectAttemptsDisplay').innerText = '';
          document.getElementById('connectBtn').innerHTML = '🔴 Desconectar';
        };
        ws.onmessage = (e) => {
          try {
            const d = JSON.parse(e.data);
            if (currentExchange==='bybit' && d.topic?.startsWith('kline')) updateRealtime(d.data[0]);
            else if (currentExchange==='binance' && d.k) updateRealtime({ start:d.k.t, open:d.k.o, high:d.k.h, low:d.k.l, close:d.k.c });
          } catch(e){}
        };
        ws.onerror = () => setText('connStatus','🔴 ERRO');
        ws.onclose = () => {
          setText('connStatus','🟡 Offline');
          document.getElementById('connectBtn').innerHTML = '🔌 Conectar';
          if (!manualDisconnect && autoTradeEnabled) {
            const delay = Math.min(30000, 1000*Math.pow(1.5, reconnectAttempts));
            reconnectAttempts++;
            document.getElementById('reconnectAttemptsDisplay').innerText = `⏳ Reconectando em ${Math.round(delay/1000)}s`;
            wsReconnectTimer = setTimeout(() => { if(autoTradeEnabled && !manualDisconnect) conectarWebSocket(); }, delay);
          }
        };
      }

      window.toggleConnection = () => {
        if (ws && ws.readyState===WebSocket.OPEN) pararConexaoWebSocket(true);
        else conectarWebSocket();
      };

      let timerInterval;
      function atualizarTimerVela() {
        if (!series.length) { setText('timerDisplay','⏱️ --:--'); return; }
        const ultimo = series[series.length-1], now = Math.floor(Date.now()/1000);
        let intv; switch(currentInterval){ case '1':intv=60;break; case '3':intv=180;break; case '5':intv=300;break; case '15':intv=900;break; case '30':intv=1800;break; case '60':intv=3600;break; default:intv=300; }
        const seg = Math.max(0, ultimo.time + intv - now);
        setText('timerDisplay', `⏱️ ${Math.floor(seg/60).toString().padStart(2,'0')}:${(seg%60).toString().padStart(2,'0')}`);
      }
      function iniciarTimerVela() {
        if(timerInterval) clearInterval(timerInterval);
        atualizarTimerVela(); timerInterval = setInterval(atualizarTimerVela, 500);
      }

      document.getElementById('exchangeSelect').addEventListener('change', function() { currentExchange=this.value; atualizarUIExchange(); salvarEstado(); });
      document.getElementById('autoTradeEnabled').addEventListener('change', e => {
        autoTradeEnabled = e.target.checked; salvarEstado();
        log(autoTradeEnabled ? '🤖 Auto-Trade ATIVADO' : '⏸️ Auto-Trade DESATIVADO');
        if (autoTradeEnabled && (!ws || ws.readyState!==WebSocket.OPEN)) conectarWebSocket();
        else if (!autoTradeEnabled) pararConexaoWebSocket(true);
      });
      ['orderQty','tpInput','slInput','profitGoalBRL','intervalInput','rsiMin','rsiMax','trailingStop','trailingActivation','useTrailingStop','safeNewsMode','testnetMode'].forEach(id => document.getElementById(id).addEventListener('change', salvarEstado));
      document.getElementById('symbolSelect').addEventListener('change', salvarEstado);
      document.getElementById('intervalSelect').addEventListener('change', function() {
        currentInterval = this.value;
        document.getElementById('intervalDisplay').innerText = currentInterval + (currentInterval==='60'?'h':'m');
        salvarEstado(); if(series.length) iniciarTimerVela();
      });
      document.getElementById('refreshChartBtn').onclick = () => fetchHistoricalKlines();

      initChart(); atualizarCotacaoBRL(); carregarEstado();
      STRATEGY = {
        orderQty: +document.getElementById('orderQty').value,
        tp: +document.getElementById('tpInput').value/100,
        sl: +document.getElementById('slInput').value/100,
        minOrderInterval: +document.getElementById('intervalInput').value,
        profitGoalBRL: +document.getElementById('profitGoalBRL').value,
        rsiMin: +document.getElementById('rsiMin').value,
        rsiMax: +document.getElementById('rsiMax').value,
        trailingStop: +document.getElementById('trailingStop').value/100,
        trailingActivation: +document.getElementById('trailingActivation').value/100,
        useTrailing: document.getElementById('useTrailingStop').checked
      };
      autoTradeEnabled = document.getElementById('autoTradeEnabled').checked;
      atualizarSaldoUI(); atualizarPainelHistorico(); renderPosition();
      if (autoTradeEnabled) conectarWebSocket(); else fetchHistoricalKlines();
      if (temAPI()) setTimeout(atualizarSaldoReal, 1000);
      setInterval(atualizarCotacaoBRL, 300000);
    })();
  }
</script>
</body>
</html>
