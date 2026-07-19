# CMozESP32Board
Code to support the new range of boards CMoz Designed by Pegg Engineering. 

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CMozGlow — live simulator &amp; guide</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,300;9..144,600;9..144,700&family=Space+Grotesk:wght@400;500;600&display=swap" rel="stylesheet">
<style>
/* ───────────────────────── tokens ───────────────────────── */
:root{
  --ink:#0E0B14;            /* velvet night */
  --panel:#171223;
  --panel-2:#1E1830;
  --line:#2C2440;
  --chalk:#EFEAF6;
  --thread:#8E86A3;
  --pink:#FF2A78;           /* TinkerTailor pink — the library default colour */
  --led:#FF2A78;            /* ← updated live by the simulation, every frame */
  --led-soft:rgba(255,42,120,.14);
  --ok:#37D99A; --bad:#FF5C5C; --warn:#FFC94A;
  --disp:"Fraunces","Iowan Old Style",Palatino,Georgia,serif;
  --body:"Space Grotesk",system-ui,-apple-system,"Segoe UI",sans-serif;
  --mono:"SF Mono","Cascadia Code",Consolas,"Roboto Mono",monospace;
  --rad:14px;
}
*{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
@media (prefers-reduced-motion:reduce){html{scroll-behavior:auto}}
body{background:var(--ink);color:var(--chalk);font-family:var(--body);line-height:1.6;
  -webkit-font-smoothing:antialiased;overflow-x:hidden}
::selection{background:var(--led);color:var(--ink)}

/* stitch rule — the thread motif */
.stitch{border:none;height:0;border-top:2px dashed var(--line);margin:0}
.stitch.lit{border-top-color:color-mix(in srgb,var(--led) 45%,var(--line))}

a{color:var(--chalk)}
:focus-visible{outline:2px solid var(--led);outline-offset:3px;border-radius:4px}
button{font-family:var(--body)}

/* ───────────────────────── header ───────────────────────── */
header{position:relative;padding:34px min(6vw,64px) 10px;display:flex;align-items:baseline;
  justify-content:space-between;flex-wrap:wrap;gap:10px}
.brand{display:flex;align-items:baseline;gap:14px}
.brand h1{font-family:var(--disp);font-weight:700;font-size:clamp(28px,4vw,42px);letter-spacing:.5px}
.brand h1 em{font-style:normal;color:var(--led);transition:color .6s}
.brand .ver{font-family:var(--mono);font-size:12px;color:var(--thread);border:1px solid var(--line);
  padding:2px 8px;border-radius:99px}
nav{display:flex;gap:20px;font-size:14px}
nav a{color:var(--thread);text-decoration:none;padding-bottom:2px;border-bottom:2px dashed transparent}
nav a:hover{color:var(--chalk);border-bottom-color:var(--led)}
.eyebrow{font-family:var(--mono);font-size:12px;letter-spacing:.18em;text-transform:uppercase;color:var(--thread)}
.tagline{padding:0 min(6vw,64px) 18px;color:var(--thread);max-width:720px}
.tagline b{color:var(--chalk);font-weight:500}

/* ───────────────────────── simulator ───────────────────────── */
#stage-wrap{position:relative;margin:0 min(6vw,64px);border:1px solid var(--line);border-radius:var(--rad);
  overflow:hidden;background:radial-gradient(1200px 400px at 50% 110%,var(--led-soft),transparent 70%),var(--panel);
  transition:background .5s}
#stage{display:block;width:100%;height:min(56vh,520px);cursor:grab;touch-action:pan-y}
#pix0-label{position:absolute;font-family:var(--mono);font-size:11px;color:var(--thread);
  background:rgba(14,11,20,.75);border:1px solid var(--line);padding:3px 8px;border-radius:99px;
  transform:translate(-50%,0);pointer-events:none;white-space:nowrap;backdrop-filter:blur(2px)}
#pix0-label b{color:var(--led)}
.hud{position:absolute;top:14px;left:16px;display:flex;gap:8px;flex-wrap:wrap}
.hud .tag{font-family:var(--mono);font-size:11px;padding:4px 10px;border-radius:99px;
  border:1px solid var(--line);background:rgba(14,11,20,.7);color:var(--thread);backdrop-filter:blur(2px)}
.hud .tag b{color:var(--chalk);font-weight:500}
#autopilot-tag{display:none;border-color:color-mix(in srgb,var(--ok) 50%,var(--line));color:var(--ok)}
#limited-tag{display:none;border-color:color-mix(in srgb,var(--warn) 55%,var(--line));color:var(--warn)}

/* ───────────────────────── control deck ───────────────────────── */
#deck{margin:18px min(6vw,64px) 0;display:grid;gap:16px;grid-template-columns:1.35fr .9fr}
@media(max-width:960px){#deck{grid-template-columns:1fr}}
.card{background:var(--panel);border:1px solid var(--line);border-radius:var(--rad);padding:18px 20px}
.card h3{font-family:var(--disp);font-weight:600;font-size:17px;margin-bottom:12px;letter-spacing:.02em}
.card h3 .sub{font-family:var(--mono);font-size:11px;color:var(--thread);font-weight:400;margin-left:8px}

.chips{display:flex;flex-wrap:wrap;gap:8px}
.chip{border:1px solid var(--line);background:var(--panel-2);color:var(--chalk);padding:7px 14px;
  border-radius:99px;font-size:13.5px;cursor:pointer;transition:border-color .2s, background .2s}
.chip:hover{border-color:var(--thread)}
.chip[aria-pressed="true"]{border-color:var(--led);background:color-mix(in srgb,var(--led) 16%,var(--panel-2));
  box-shadow:0 0 14px -6px var(--led)}
.chip .n{font-family:var(--mono);font-size:11px;color:var(--thread);margin-right:6px}

.row{display:flex;align-items:center;gap:12px;margin-top:14px;flex-wrap:wrap}
.row label{font-size:13px;color:var(--thread);min-width:96px}
.row output{font-family:var(--mono);font-size:12.5px;color:var(--chalk);min-width:64px;text-align:right}
input[type=range]{flex:1;min-width:120px;accent-color:var(--led);height:22px}
.swatches{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
.sw{width:26px;height:26px;border-radius:8px;border:2px solid transparent;cursor:pointer;padding:0}
.sw[aria-pressed="true"]{border-color:var(--chalk)}
input[type=color]{width:34px;height:30px;border:1px solid var(--line);border-radius:8px;background:var(--panel-2);cursor:pointer;padding:2px}
.toggle{display:flex;align-items:center;gap:10px;cursor:pointer;font-size:13.5px;color:var(--thread);user-select:none}
.toggle input{accent-color:var(--led);width:17px;height:17px;cursor:pointer}
.toggle b{color:var(--chalk);font-weight:500}
#morse-row{display:none}
#morse-row input[type=text]{flex:1;background:var(--panel-2);border:1px solid var(--line);color:var(--chalk);
  border-radius:8px;padding:8px 12px;font-family:var(--mono);font-size:13px;min-width:140px}
#morse-row input:focus{border-color:var(--led);outline:none}
.mini-note{font-size:12px;color:var(--thread);margin-top:10px}
.mini-note code{font-family:var(--mono);color:var(--chalk);font-size:11.5px}

/* telemetry */
.tele-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px 18px;margin-bottom:14px}
.tele .k{font-family:var(--mono);font-size:11px;color:var(--thread);letter-spacing:.08em;text-transform:uppercase}
.tele .v{font-family:var(--mono);font-size:20px;margin-top:2px}
.tele .v small{font-size:12px;color:var(--thread)}
#gauge{height:10px;border-radius:99px;background:var(--panel-2);border:1px solid var(--line);overflow:hidden;position:relative}
#gauge-fill{height:100%;width:0%;background:linear-gradient(90deg,var(--led),#fff3);transition:width .25s,background .3s;border-radius:99px}
#gauge.hot #gauge-fill{background:linear-gradient(90deg,var(--warn),var(--bad))}
.gauge-cap{display:flex;justify-content:space-between;font-family:var(--mono);font-size:11px;color:var(--thread);margin-top:5px}
#laps-line{font-family:var(--mono);font-size:12px;color:var(--thread);margin-top:12px}
#laps-line b{color:var(--ok)}

/* live sketch */
#sketch-card{grid-column:1/-1}
pre{background:#0B0812;border:1px solid var(--line);border-radius:10px;padding:16px 18px;overflow-x:auto;
  font-family:var(--mono);font-size:12.8px;line-height:1.7;color:#CFC7E4}
pre .c{color:#6E6488}pre .k{color:#7FB3FF}pre .s{color:#8CE0B0}pre .f{color:var(--led)}pre .n{color:#FFC94A}
.copy-btn{float:right;font-family:var(--mono);font-size:11.5px;background:var(--panel-2);color:var(--thread);
  border:1px solid var(--line);border-radius:8px;padding:5px 12px;cursor:pointer}
.copy-btn:hover{color:var(--chalk);border-color:var(--led)}

/* ───────────────────────── sections ───────────────────────── */
section{padding:64px min(6vw,64px) 8px;max-width:1200px;margin:0 auto}
section>.eyebrow{margin-bottom:8px}
section h2{font-family:var(--disp);font-weight:600;font-size:clamp(24px,3vw,34px);margin-bottom:8px}
section .lede{color:var(--thread);max-width:680px;margin-bottom:28px}
.reveal{opacity:0;transform:translateY(16px);transition:opacity .6s,transform .6s}
.reveal.in{opacity:1;transform:none}
@media (prefers-reduced-motion:reduce){.reveal{opacity:1;transform:none;transition:none}}

.feat-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:14px}
.feat{border:2px dashed var(--line);border-radius:var(--rad);padding:18px 20px;transition:border-color .3s}
.feat:hover{border-color:color-mix(in srgb,var(--led) 55%,var(--line))}
.feat .ic{font-size:20px}
.feat h4{font-family:var(--disp);font-size:17px;margin:8px 0 6px}
.feat p{font-size:13.5px;color:var(--thread)}
.feat p code{font-family:var(--mono);font-size:12px;color:var(--chalk)}

/* effects gallery */
.fx-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:12px}
.fx-card{background:var(--panel);border:1px solid var(--line);border-radius:var(--rad);padding:14px 16px;
  cursor:pointer;text-align:left;color:var(--chalk);transition:transform .18s,border-color .2s}
.fx-card:hover{transform:translateY(-3px);border-color:var(--led)}
.fx-card .strip{height:8px;border-radius:99px;margin-bottom:10px}
.fx-card h4{font-size:15px;font-weight:600}
.fx-card h4 span{font-family:var(--mono);font-size:11px;color:var(--thread);margin-right:6px}
.fx-card p{font-size:12.5px;color:var(--thread);margin-top:3px}
.fx-card .try{font-family:var(--mono);font-size:11px;color:var(--led);margin-top:8px;display:inline-block}

/* error playground */
.err-grid{display:grid;grid-template-columns:280px 1fr;gap:16px}
@media(max-width:800px){.err-grid{grid-template-columns:1fr}}
.err-btn{display:block;width:100%;text-align:left;background:var(--panel);border:1px solid var(--line);
  color:var(--chalk);border-radius:10px;padding:11px 14px;font-family:var(--mono);font-size:12.5px;
  cursor:pointer;margin-bottom:8px}
.err-btn:hover{border-color:var(--bad)}
.err-btn.fix:hover{border-color:var(--ok)}
#console{background:#0B0812;border:1px solid var(--line);border-radius:10px;padding:14px 16px;min-height:190px;
  font-family:var(--mono);font-size:12.5px;line-height:1.9;overflow-y:auto;max-height:260px}
#console .line b{color:var(--bad)}
#console .line.good b{color:var(--ok)}
#console .line span{color:var(--thread)}
#console .hint{color:var(--thread);font-style:italic}

/* tables & api */
table{width:100%;border-collapse:collapse;font-size:13.5px}
th,td{text-align:left;padding:9px 12px;border-bottom:1px dashed var(--line)}
th{font-family:var(--mono);font-size:11px;letter-spacing:.1em;text-transform:uppercase;color:var(--thread)}
td code{font-family:var(--mono);font-size:12px;color:var(--led)}
td.dim{color:var(--thread)}
details{border:1px solid var(--line);border-radius:10px;margin-bottom:10px;background:var(--panel)}
summary{cursor:pointer;padding:13px 18px;font-family:var(--disp);font-size:16px;list-style:none;display:flex;justify-content:space-between;align-items:center}
summary::after{content:"+";font-family:var(--mono);color:var(--thread);font-size:18px}
details[open] summary::after{content:"–"}
details .body{padding:0 18px 16px}
details pre{margin-top:4px}

.golden{border:2px dashed color-mix(in srgb,var(--led) 55%,var(--line));border-radius:var(--rad);
  padding:20px 24px;font-size:15px;background:var(--led-soft)}
.golden b{font-family:var(--mono)}
.wire{font-family:var(--mono);font-size:12.5px;color:var(--thread);white-space:pre;overflow-x:auto;
  background:#0B0812;border:1px solid var(--line);border-radius:10px;padding:16px 18px;line-height:1.7;margin-top:14px}
.wire b{color:var(--led)}

footer{margin-top:70px;padding:26px min(6vw,64px) 40px;border-top:2px dashed var(--line);
  display:flex;justify-content:space-between;flex-wrap:wrap;gap:12px;color:var(--thread);font-size:13px}
footer b{color:var(--chalk);font-weight:500}
.toast{position:fixed;bottom:22px;left:50%;transform:translate(-50%,80px);background:var(--panel-2);
  border:1px solid var(--led);color:var(--chalk);padding:10px 20px;border-radius:99px;font-size:13.5px;
  transition:transform .35s;z-index:50;box-shadow:0 8px 30px -8px rgba(0,0,0,.6)}
.toast.show{transform:translate(-50%,0)}
</style>
</head>
<body>

<header>
  <div class="brand">
    <h1>CMoz<em>Glow</em></h1>
    <span class="ver">v1.1.0</span>
  </div>
  <nav aria-label="Sections">
    <a href="#deck">Simulator</a><a href="#why">Why</a><a href="#effects">Effects</a>
    <a href="#errors">Errors</a><a href="#api">API</a>
  </nav>
</header>
<p class="tagline">The friendly WS2812B library for the <b>CMoz ESP32-S3 Mini</b> — running live below.
This page executes the library's <b>actual math</b>: same gamma curve, same power budgeting, same effects,
same error messages. Twenty pixels, zero soldering. <span class="eyebrow" style="display:block;margin-top:6px">TinkerTailor.ca · CMozMaker · made in Canada</span></p>

<!-- ═══════════════ SIMULATOR ═══════════════ -->
<div id="stage-wrap">
  <canvas id="stage" aria-label="3D simulation of a 20-LED CMozGlow strip on fabric"></canvas>
  <div id="pix0-label"><b>pixel 0</b> · onboard, GPIO 3</div>
  <div class="hud">
    <span class="tag">effect <b id="hud-fx">Aurora</b></span>
    <span class="tag" id="autopilot-tag">✈ AutoPilot · core 0</span>
    <span class="tag" id="limited-tag">⚠ auto-dimmed to fit budget</span>
  </div>
</div>

<!-- ═══════════════ CONTROL DECK ═══════════════ -->
<div id="deck">
  <div class="card">
    <h3>Controls <span class="sub">every knob maps 1:1 to a library call</span></h3>
    <div class="chips" id="fx-chips" role="group" aria-label="Effect"></div>
    <div class="row" id="morse-row">
      <label for="morse-in">Message</label>
      <input type="text" id="morse-in" value="HELLO WORLD" maxlength="63" spellcheck="false"
             aria-label="Morse message — letters, digits and spaces">
    </div>
    <div class="row">
      <label for="speed">Speed</label>
      <input type="range" id="speed" min="1" max="10" value="5">
      <output id="speed-out">5</output>
    </div>
    <div class="row">
      <label for="bright">Brightness</label>
      <input type="range" id="bright" min="0" max="255" value="180">
      <output id="bright-out">180</output>
    </div>
    <div class="row">
      <label for="budget">Power budget</label>
      <input type="range" id="budget" min="0" max="1500" step="10" value="450">
      <output id="budget-out">450 mA</output>
    </div>
    <div class="row">
      <label>Colour</label>
      <div class="swatches" id="swatches"></div>
      <input type="color" id="color-pick" value="#FF2A78" aria-label="Custom colour">
    </div>
    <div class="row" style="gap:22px">
      <label class="toggle"><input type="checkbox" id="status-tg"> <span><b>Status pixel</b> — pixel 0 shows library health</span></label>
      <label class="toggle"><input type="checkbox" id="auto-tg"> <span><b>AutoPilot</b> — LEDs on their own core</span></label>
      <label class="toggle"><input type="checkbox" id="gamma-tg" checked> <span><b>Gamma</b></span></label>
    </div>
    <p class="mini-note">Note the golden rule at work: <code>NUM_LEDS = 20</code> here means the onboard pixel <b>plus</b> a 19-LED strip, all on GPIO&nbsp;3.</p>
  </div>

  <div class="card tele">
    <h3>Telemetry <span class="sub">straight from show()</span></h3>
    <div class="tele-grid">
      <div><div class="k">estimated draw</div><div class="v" id="ma-out">— <small>mA</small></div></div>
      <div><div class="k">frame interval</div><div class="v" id="iv-out">— <small>ms</small></div></div>
      <div><div class="k">library status</div><div class="v" id="st-out" style="font-size:14px;color:var(--ok)">OK</div></div>
      <div><div class="k">sim fps</div><div class="v" id="fps-out">—</div></div>
    </div>
    <div class="k" style="font-family:var(--mono);font-size:11px;color:var(--thread);letter-spacing:.08em;text-transform:uppercase;margin-bottom:6px">draw vs budget</div>
    <div id="gauge"><div id="gauge-fill"></div></div>
    <div class="gauge-cap"><span>0 mA</span><span id="gauge-max">450 mA budget</span></div>
    <p id="laps-line">loop() laps/sec: <b id="laps">—</b> <span id="laps-note">(manual mode — your loop() shares time with the LEDs)</span></p>
    <p class="mini-note">The current estimate uses the library's model: ~20&nbsp;mA per fully-lit channel + ~1&nbsp;mA idle per chip. Blow the budget and CMozGlow quietly dims the frame — try full-white Solid at max brightness.</p>
  </div>

  <div class="card" id="sketch-card">
    <h3>Your sketch, written live <span class="sub">copy → PlatformIO or Arduino IDE → identical result on hardware</span>
      <button class="copy-btn" id="copy-sketch">copy .ino</button></h3>
    <pre id="sketch" aria-live="off"></pre>
  </div>
</div>

<hr class="stitch lit" style="margin:56px min(6vw,64px) 0">

<!-- ═══════════════ WHY ═══════════════ -->
<section id="why" class="reveal">
  <p class="eyebrow">why it's different</p>
  <h2>Built for wearables, not just walls of pixels</h2>
  <p class="lede">Other LED libraries assume a desk, a bench supply and a serial monitor. CMozGlow assumes a costume that's already sewn shut.</p>
  <div class="feat-grid">
    <div class="feat"><span class="ic">🚑</span><h4>Errors in plain English</h4><p>Every failing call returns <code>false</code> and <code>errorText()</code> tells you exactly what to fix — <em>"pixel index is past the end of the strip."</em> Try it in the playground below.</p></div>
    <div class="feat"><span class="ic">🔋</span><h4>Power budgeting</h4><p><code>setPowerBudgetmA(250)</code> and the library estimates every frame's draw and auto-dims to keep your LiPo safe. Watch the gauge above do it in real time.</p></div>
    <div class="feat"><span class="ic">🟢</span><h4>Status pixel</h4><p>The onboard pixel becomes a live health light — green means happy, red means an error happened. Debugging for garments you can't plug in.</p></div>
    <div class="feat"><span class="ic">✈️</span><h4>AutoPilot</h4><p>One line — <code>autoUpdate(true)</code> — moves the LEDs to the S3's second CPU core. Your loop() stays 100% free for sensors, touch and Bluetooth. Thread-safe throughout.</p></div>
    <div class="feat"><span class="ic">⚡</span><h4>Async hardware engine</h4><p><code>show()</code> hands frames to the RMT peripheral and returns in ~100 µs — even on long strips. The latch gap is encoded in hardware. No busy-waiting anywhere.</p></div>
    <div class="feat"><span class="ic">🎨</span><h4>Gamma, on by default</h4><p>Colours look the way you mixed them. Toggle it above and watch the mids shift — that's the 2.6 curve this page shares with the silicon.</p></div>
  </div>
</section>

<!-- ═══════════════ EFFECTS ═══════════════ -->
<section id="effects" class="reveal">
  <p class="eyebrow">the collection</p>
  <h2>Ten effects from the atelier</h2>
  <p class="lede">Named for fabric, runways and northern skies — not test patterns. Click any card to run it in the simulator.</p>
  <div class="fx-grid" id="fx-gallery"></div>
</section>

<!-- ═══════════════ ERROR PLAYGROUND ═══════════════ -->
<section id="errors" class="reveal">
  <p class="eyebrow">interactive</p>
  <h2>The error playground</h2>
  <p class="lede">Make real mistakes, get the library's real answers — the exact strings compiled into v1.1.0. Turn on the status pixel first and watch pixel 0 go red.</p>
  <div class="err-grid">
    <div>
      <button class="err-btn" data-err="index">glow.setPixel(<span style="color:var(--bad)">999</span>, 255,0,0);</button>
      <button class="err-btn" data-err="effect">glow.setEffect(<span style="color:var(--bad)">42</span>);</button>
      <button class="err-btn" data-err="morse">glow.setMorseMessage(<span style="color:var(--bad)">"HI!"</span>);</button>
      <button class="err-btn" data-err="pin">CMozGlow bad(20, <span style="color:var(--bad)">27</span>); bad.begin();</button>
      <button class="err-btn fix" data-err="clear" style="color:var(--ok)">glow.clearError();</button>
    </div>
    <div id="console" role="log" aria-label="Library responses"><div class="line hint">// responses appear here…</div></div>
  </div>
</section>

<!-- ═══════════════ WIRING / GOLDEN RULE ═══════════════ -->
<section class="reveal">
  <p class="eyebrow">hardware</p>
  <h2>Wiring in one breath</h2>
  <div class="golden">🧵 <b>The golden rule:</b> your board's onboard pixel lives on GPIO 3 and is <b>pixel 0</b>.
    Attach your strip's DIN to GPIO 3 too — it continues as pixel 1 onward. So <b>NUM_LEDS = 1 + strip length</b>.</div>
  <div class="wire">CMoz ESP32-S3 Mini                    WS2812B strip
┌───────────────┐
│  <b>[pixel 0]</b>    │   GPIO 3 ───────▶  DIN → ● ● ● ● ● … (pixels 1…n)
│   onboard      │   GND    ───────▶  GND
└───────────────┘   5V from a supply that matches your power budget

Long run? Add 300–500 Ω in the data line + 500–1000 µF across power.
Conductive thread? Keep data under ~30 cm and sew a ground line alongside.</div>
</section>

<!-- ═══════════════ API ═══════════════ -->
<section id="api" class="reveal">
  <p class="eyebrow">source of truth</p>
  <h2>API reference</h2>
  <p class="lede">Grouped the way you'll reach for it. Everything returns honestly: <code style="font-family:var(--mono)">false</code> means check <code style="font-family:var(--mono)">errorText()</code>.</p>

  <details open><summary>Lifecycle &amp; pixels</summary><div class="body"><pre>CMozGlow glow(numLeds);            <span class="c">// GPIO 3 by default</span>
CMozGlow glow(numLeds, pin);       <span class="c">// or any valid ESP32-S3 output pin</span>
<span class="k">bool</span> begin();                      <span class="c">// once in setup(); false → errorText()</span>
<span class="k">void</span> end();                        <span class="c">// lands AutoPilot, waits for hardware, frees memory</span>
<span class="k">bool</span> setPixel(i, r, g, b);         <span class="c">// bounds-checked, always</span>
<span class="k">bool</span> setPixel(i, color);           <span class="c">// packed 0xRRGGBB</span>
<span class="k">uint32_t</span> getPixel(i);
<span class="k">void</span> fill(color);   <span class="k">void</span> clear();
<span class="k">bool</span> show();                       <span class="c">// async: hands the frame to hardware, returns in ~µs</span>
<span class="k">uint16_t</span> numPixels();</pre></div></details>

  <details><summary>Look, feel &amp; power safety</summary><div class="body"><pre><span class="k">void</span> setBrightness(0-255);         <span class="c">// non-destructive master dimmer</span>
<span class="k">void</span> setGamma(<span class="k">true</span>/<span class="k">false</span>);         <span class="c">// 2.6 curve, on by default</span>
<span class="k">void</span> setPowerBudgetmA(mA);         <span class="c">// 0 = unlimited</span>
<span class="k">uint16_t</span> estimatedCurrentmA();     <span class="c">// what the last frame drew (model)</span>
<span class="k">bool</span> powerLimited();               <span class="c">// did the library auto-dim?</span></pre></div></details>

  <details><summary>Effects engine</summary><div class="body"><pre><span class="k">bool</span> setEffect(0-9);               <span class="c">// or CMOZ_FX_… constants</span>
<span class="k">void</span> setEffectColor(color);
<span class="k">bool</span> setEffectSpeed(1-10);
<span class="k">bool</span> setMorseMessage(<span class="s">"TEXT 123"</span>);  <span class="c">// A-Z, 0-9, spaces, ≤63 chars</span>
<span class="k">bool</span> update();                     <span class="c">// every loop(); true when a frame drew</span>
<span class="k">static const char*</span> effectName(id);</pre></div></details>

  <details><summary>AutoPilot (advanced)</summary><div class="body"><pre><span class="k">bool</span> autoUpdate(<span class="k">true</span>);             <span class="c">// ✈ LEDs move to core 0; loop() is yours</span>
<span class="k">bool</span> autoUpdate(<span class="k">false</span>);            <span class="c">// lands cleanly — never force-kills a task</span>
<span class="k">bool</span> autoRunning();</pre>
  <p style="font-size:13px;color:var(--thread)">All calls stay thread-safe mid-flight: a recursive mutex guards the whole library. Landing waits for the task to confirm exit and for the in-flight frame to finish before any memory moves — no dangling tasks, no use-after-free.</p></div></details>

  <details><summary>Error handling</summary><div class="body"><pre><span class="k">CMozError</span> lastError();   <span class="k">const char*</span> errorText();   <span class="k">void</span> clearError();
<span class="k">void</span> statusPixel(<span class="k">true</span>);            <span class="c">// pixel 0: green = happy, red = error</span></pre>
  <table><tr><th>code</th><th>meaning</th></tr>
  <tr><td><code>CMOZ_OK</code></td><td class="dim">everything is fine</td></tr>
  <tr><td><code>CMOZ_ERR_NOT_BEGUN</code></td><td class="dim">begin() wasn't called or failed</td></tr>
  <tr><td><code>CMOZ_ERR_BAD_PIN</code></td><td class="dim">use GPIO 0–21 or 33–48 (26–32 are the flash pins)</td></tr>
  <tr><td><code>CMOZ_ERR_BAD_COUNT</code></td><td class="dim">LED count must be 1–2000</td></tr>
  <tr><td><code>CMOZ_ERR_ALLOC</code></td><td class="dim">out of memory (very long strip)</td></tr>
  <tr><td><code>CMOZ_ERR_RMT</code></td><td class="dim">the LED peripheral failed or timed out</td></tr>
  <tr><td><code>CMOZ_ERR_INDEX</code></td><td class="dim">pixel index past the end of the strip</td></tr>
  <tr><td><code>CMOZ_ERR_BAD_EFFECT</code></td><td class="dim">effect number doesn't exist (0–9)</td></tr>
  <tr><td><code>CMOZ_ERR_BAD_ARG</code></td><td class="dim">argument out of range</td></tr>
  <tr><td><code>CMOZ_ERR_TASK</code></td><td class="dim">AutoPilot's task couldn't start or stop</td></tr></table></div></details>

  <details><summary>Suggested power budgets</summary><div class="body">
  <table><tr><th>power source</th><th>budget</th></tr>
  <tr><td class="dim">USB port</td><td><code>setPowerBudgetmA(450)</code></td></tr>
  <tr><td class="dim">small LiPo (400 mAh)</td><td><code>setPowerBudgetmA(250)</code></td></tr>
  <tr><td class="dim">dedicated 5 V / 2 A supply</td><td><code>setPowerBudgetmA(1500)</code></td></tr></table>
  <p style="font-size:13px;color:var(--thread);margin-top:10px">The estimate is a model (~20 mA per fully-lit channel + ~1 mA idle per chip) — always leave headroom.</p></div></details>
</section>

<footer>
  <div>Made with 💛 in Canada · <b>TinkerTailor.ca</b> — components &amp; conductive fabrics</div>
  <div>Tutorials on <b>CMozMaker</b> · MIT licensed · CMozGlow v1.1.0</div>
</footer>

<div class="toast" id="toast">Copied!</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
"use strict";
/* ════════════════════════════════════════════════════════════════
   PART 1 — CMozGlowSim: a faithful JavaScript port of the library.
   Same gamma, same power model, same effect math, same error text.
   The 3D scene is just a display attached to sim.out[].
   ════════════════════════════════════════════════════════════════ */
const N = 20;
const now = () => performance.now();

const ERR = { OK:0, INDEX:6, BAD_EFFECT:7, BAD_ARG:8, BAD_PIN:2 };
const ERR_TEXT = {
  0:"OK — everything is fine",
  2:"that GPIO can't be used — pick 0-21 or 33-48 on the ESP32-S3 (the onboard pixel is on 3)",
  6:"pixel index is past the end of the strip — indexes run 0 to numPixels()-1",
  7:"no effect with that number — valid effects are 0 to 9",
  8:"an argument was out of range — check the docs for valid values"
};
const FX_META = [
 ["Solid","steady colour"],
 ["Sequin","random glints, like sequins under stage light"],
 ["Catwalk","a bold sweep back and forth with a fading train"],
 ["Loom","two colour threads weaving past each other"],
 ["Heartbeat","a realistic lub-dub double pulse"],
 ["Firefly","soft lights breathing in and out at random spots"],
 ["Silk","a slow sheen rolling along the strip"],
 ["Ember","warm campfire glow"],
 ["Aurora","northern lights — a little Canada in every project"],
 ["Morse","blinks a real Morse-code message"]
];
const BASE_IV = [200,45,35,120,20,30,30,45,35,10];
const GAMMA = new Uint8Array(256);
for (let i=0;i<256;i++) GAMMA[i] = Math.round(Math.pow(i/255,2.6)*255);

const MORSE = [".-","-...","-.-.","-..",".","..-.","--.","....","..",".---",
 "-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-",
 "..-","...-",".--","-..-","-.--","--..",
 "-----",".----","..---","...--","....-",".....","-....","--...","---..","----."];

class CMozGlowSim {
  constructor(count){
    this.count = count;
    this.pix = new Uint8Array(count*3);   // logical buffer (pre-scale)
    this.out = new Float32Array(count*3); // what "hardware" shows, 0..1
    this.heat = new Uint8Array(count);
    this.brightness = 180; this.gammaOn = true;
    this.budgetmA = 450; this.lastEstmA = 0; this.powerLimited = false;
    this.err = ERR.OK; this.statusOn = false;
    this.fx = 8; this.fxColor = 0xFF2A78; this.speed = 5;
    this.nextFrameAt = 0; this.t0 = now(); this.step = 0;
    this.flies = Array.from({length:8},()=>({alive:false,pos:0,phase:0,rate:0}));
    this.mMsg = "HELLO WORLD"; this.mChar=0; this.mElem=0; this.mOn=false; this.mNextAt=0;
  }
  errorText(){ return ERR_TEXT[this.err] || "unknown error"; }
  clearError(){ this.err = ERR.OK; }
  setPixel(i,r,g,b){ if(i>=this.count){this.err=ERR.INDEX;return false;}
    const p=i*3; this.pix[p]=r; this.pix[p+1]=g; this.pix[p+2]=b; return true; }
  setPixelC(i,c){ return this.setPixel(i,(c>>16)&255,(c>>8)&255,c&255); }
  fill(c){ for(let i=0;i<this.count;i++) this.setPixelC(i,c); }
  clear(){ this.fill(0); }
  setEffect(id){ if(id>9||id<0){this.err=ERR.BAD_EFFECT;return false;}
    this.fx=id; this.t0=now(); this.step=0; this.nextFrameAt=0;
    this.mChar=this.mElem=0; this.mOn=false; this.mNextAt=0;
    this.flies.forEach(f=>f.alive=false); this.clear(); this.heat.fill(0); return true; }
  setEffectSpeed(s){ if(s<1||s>10){this.err=ERR.BAD_ARG;return false;} this.speed=s; return true; }
  setMorseMessage(m){
    if(!m || m.length===0 || m.length>63){ this.err=ERR.BAD_ARG; return false; }
    for(const ch of m.toUpperCase())
      if(!(ch===" "||(ch>="A"&&ch<="Z")||(ch>="0"&&ch<="9"))){ this.err=ERR.BAD_ARG; return false; }
    this.mMsg=m; this.mChar=this.mElem=0; this.mOn=false; this.mNextAt=0; return true; }
  frameInterval(){ return Math.max(5, Math.floor(BASE_IV[this.fx]*(13-this.speed)/8)); }

  /* ── show(): the library's exact scaling pipeline ── */
  show(){
    const bScale = this.brightness+1;
    let sum=0;
    for(let n=0;n<this.count*3;n++) sum += (this.pix[n]*bScale)>>8;
    let est = this.count + Math.floor(sum*20/255);
    this.lastEstmA = est;
    let pScale = 256; this.powerLimited = false;
    if(this.budgetmA && est>this.budgetmA){
      const idle=this.count, room=Math.max(0,this.budgetmA-idle), used=Math.max(1,est-idle);
      pScale=Math.min(256,Math.floor(room*256/used));
      this.powerLimited=true; this.lastEstmA=this.budgetmA;
    }
    const scale=(bScale*pScale)>>8;
    const xf=new Uint8Array(256);
    for(let v=0;v<256;v++){ const s=(v*scale)>>8; xf[v]=this.gammaOn?GAMMA[s]:s; }
    for(let i=0;i<this.count;i++){
      let r=this.pix[i*3], g=this.pix[i*3+1], b=this.pix[i*3+2];
      if(this.statusOn && i===0){                 // status overlay bypasses dimming
        if(this.err===ERR.OK){ r=0; g=24; b=4; } else { r=40; g=0; b=0; }
        this.out[0]=GAMMA[r]/255; this.out[1]=GAMMA[g]/255; this.out[2]=GAMMA[b]/255;
      } else {
        this.out[i*3]=xf[r]/255; this.out[i*3+1]=xf[g]/255; this.out[i*3+2]=xf[b]/255;
      }
    }
  }
  update(){
    const t=now();
    if(t<this.nextFrameAt) return false;
    this.nextFrameAt=t+this.frameInterval();
    [this.fxSolid,this.fxSequin,this.fxCatwalk,this.fxLoom,this.fxHeartbeat,
     this.fxFirefly,this.fxSilk,this.fxEmber,this.fxAurora,this.fxMorse][this.fx].call(this);
    this.step++; this.show(); return true;
  }
  fadeAll(k){ for(let n=0;n<this.count*3;n++) this.pix[n]=(this.pix[n]*k)>>8; }
  rgb(){ return [(this.fxColor>>16)&255,(this.fxColor>>8)&255,this.fxColor&255]; }

  /* ── the ten effects, ported line-for-line ── */
  fxSolid(){ this.fill(this.fxColor); }
  fxSequin(){ this.fadeAll(225);
    const sparks=1+Math.floor(this.count/24); const [r,g,b]=this.rgb();
    for(let s=0;s<sparks;s++) if(Math.random()*100<45){
      const i=(Math.random()*this.count)|0; this.setPixel(i,r|0xB0,g|0xB0,b|0xB0);} }
  fxCatwalk(){ this.fadeAll(200);
    const span=this.count>1?(this.count-1)*2:1, s=this.step%span;
    const head=s<this.count?s:span-s;
    this.setPixelC(head,this.fxColor);
    if(head+1<this.count) this.setPixelC(head+1,(this.fxColor>>1)&0x7F7F7F); }
  fxLoom(){ const a=this.fxColor, [r,g,b]=this.rgb(), rot=(b<<16)|(r<<8)|g;
    for(let i=0;i<this.count;i++) this.setPixelC(i, ((((i+this.step)/3)|0)&1)?a:rot); }
  fxHeartbeat(){ const t=(now()-this.t0)%1500; let e=0;
    if(t<150) e=255-Math.floor(t*255/150);
    else if(t>=250&&t<420) e=170-Math.floor((t-250)*170/170);
    const [r,g,b]=this.rgb();
    for(let i=0;i<this.count;i++) this.setPixel(i,(r*e)>>8,(g*e)>>8,(b*e)>>8); }
  fxFirefly(){ this.clear(); const [r,g,b]=this.rgb();
    for(const f of this.flies){
      if(!f.alive && Math.random()*100<6){ f.alive=true; f.pos=(Math.random()*this.count)|0;
        f.phase=0; f.rate=2+((Math.random()*5)|0); }
      if(f.alive){ f.phase+=f.rate*40;
        if(f.phase>=32768){ f.alive=false; continue; }
        const ph=f.phase>>6, e=ph<256?ph:511-ph;
        this.setPixel(f.pos,(r*e)>>8,(g*e)>>8,(b*e)>>8); } } }
  fxSilk(){ const t=(now()-this.t0)*0.0025; const [r,g,b]=this.rgb();
    for(let i=0;i<this.count;i++){ const s=0.62+0.38*Math.sin(i*0.45+t);
      this.setPixel(i,(r*s)|0,(g*s)|0,(b*s)|0); } }
  fxEmber(){ for(let i=0;i<this.count;i++){
      let h=this.heat[i]+((Math.random()*29)|0)-14;
      h=Math.max(40,Math.min(255,h)); this.heat[i]=h;
      this.setPixel(i,h,(h*78)>>8,h>200?((h-200)/4)|0:0); } }
  fxAurora(){ const t=(now()-this.t0)*0.001;
    for(let i=0;i<this.count;i++){
      const w1=Math.sin(i*0.30+t*1.1), w2=Math.sin(i*0.13-t*0.7+1.7);
      const v=(w1+w2)*0.25+0.5;
      const g=(30+170*v)|0, b=(20+120*(1-v)*v*4*0.6)|0, r=v<0.25?(90*(0.25-v)*4)|0:0;
      this.setPixel(i,r,g,b); } }
  fxMorse(){ const t=now(); if(t<this.mNextAt) return;
    const unit=60+(10-this.speed)*30;
    let c=this.mMsg[this.mChar];
    if(c===undefined){ this.fill(0); this.mOn=false; this.mChar=0; this.mElem=0;
      this.mNextAt=t+unit*10; return; }
    c=c.toUpperCase();
    if(c===" "){ this.fill(0); this.mChar++; this.mElem=0; this.mNextAt=t+unit*7; return; }
    const pat=c>="A"?MORSE[c.charCodeAt(0)-65]:MORSE[26+(c.charCodeAt(0)-48)];
    if(!this.mOn){ this.fill(this.fxColor); this.mOn=true;
      this.mNextAt=t+unit*(pat[this.mElem]==="-"?3:1); }
    else{ this.fill(0); this.mOn=false; this.mElem++;
      if(pat[this.mElem]===undefined){ this.mElem=0; this.mChar++; this.mNextAt=t+unit*3; }
      else this.mNextAt=t+unit; } }
}
const sim = new CMozGlowSim(N);

/* ════════════════════════════════════════════════════════════════
   PART 2 — the view layer. Preferred: a three.js atelier (fabric
   ribbon, 20 LEDs, glow halos, roving colour lights, camera drift).
   Fallback: a 2D glow canvas, so the simulator, telemetry and live
   sketch keep working even if WebGL or the CDN is unavailable.
   Both read the same source of truth: sim.out[].
   ════════════════════════════════════════════════════════════════ */
const canvas = document.getElementById("stage");
const pix0Label = document.getElementById("pix0-label");
const reduceMotion = matchMedia("(prefers-reduced-motion: reduce)").matches;

let px=0, py=0, dragging=false, lastPX=0;
canvas.addEventListener("pointermove", e=>{
  const r=canvas.getBoundingClientRect();
  const nx=(e.clientX-r.left)/r.width-0.5, ny=(e.clientY-r.top)/r.height-0.5;
  if(dragging){ px += (e.clientX-lastPX)*0.01; lastPX=e.clientX; }
  else { px = px*0.9 + nx*0.6*0.1; }
  py = py*0.9 + ny*0.1;
});
canvas.addEventListener("pointerdown", e=>{dragging=true; lastPX=e.clientX; canvas.style.cursor="grabbing";});
addEventListener("pointerup", ()=>{dragging=false; canvas.style.cursor="grab";});

const STRIP_LEN=17.2, X0=-STRIP_LEN/2;
const ledX = i => X0 + (i/(N-1))*STRIP_LEN;
const ledY = x => 0.55 + 0.22*Math.sin(x*0.42);

function build3D(){
  const renderer = new THREE.WebGLRenderer({canvas, antialias:true, alpha:false});
  renderer.setPixelRatio(Math.min(devicePixelRatio,2));
  renderer.setClearColor(0x0E0B14);
  const scene = new THREE.Scene();
  scene.fog = new THREE.Fog(0x0E0B14, 16, 34);
  const camera = new THREE.PerspectiveCamera(38, 2, 0.1, 100);

  /* table */
  const table = new THREE.Mesh(
    new THREE.PlaneGeometry(80,50),
    new THREE.MeshStandardMaterial({color:0x141021, roughness:0.35, metalness:0.55}));
  table.rotation.x = -Math.PI/2; scene.add(table);

  /* fabric ribbon with woven texture + stitched edges (canvas texture) */
  function fabricTexture(){
    const c=document.createElement("canvas"); c.width=512; c.height=128;
    const g=c.getContext("2d");
    g.fillStyle="#241a33"; g.fillRect(0,0,512,128);
    for(let i=0;i<2600;i++){ g.fillStyle=`rgba(255,255,255,${Math.random()*0.05})`;
      g.fillRect(Math.random()*512, Math.random()*128, 2, 1); }
    for(let i=0;i<1400;i++){ g.fillStyle=`rgba(0,0,0,${Math.random()*0.18})`;
      g.fillRect(Math.random()*512, Math.random()*128, 1, 2); }
    g.strokeStyle="rgba(214,204,235,0.5)"; g.lineWidth=3; g.setLineDash([14,10]);
    g.beginPath(); g.moveTo(0,12); g.lineTo(512,12); g.stroke();      // stitched edges —
    g.beginPath(); g.moveTo(0,116); g.lineTo(512,116); g.stroke();    // the thread motif
    const t=new THREE.CanvasTexture(c); t.wrapS=THREE.RepeatWrapping; t.repeat.set(3,1); return t;
  }
  const ribbonGeo = new THREE.BoxGeometry(STRIP_LEN+3.4, 0.14, 1.7, 64, 1, 1);
  { const pos=ribbonGeo.attributes.position;                 // gentle drape
    for(let i=0;i<pos.count;i++){ const x=pos.getX(i);
      pos.setY(i, pos.getY(i)+0.22*Math.sin(x*0.42)+0.34); } pos.needsUpdate=true;
    ribbonGeo.computeVertexNormals(); }
  scene.add(new THREE.Mesh(ribbonGeo,
    new THREE.MeshStandardMaterial({map:fabricTexture(), roughness:0.95, metalness:0})));

  /* onboard PCB under pixel 0 */
  const pcb = new THREE.Mesh(new THREE.BoxGeometry(1.7,0.16,1.5),
    new THREE.MeshStandardMaterial({color:0x231043, roughness:0.5, metalness:0.3}));
  pcb.position.set(ledX(0), ledY(ledX(0))+0.05, 0); scene.add(pcb);

  /* glow sprite texture */
  function haloTexture(){
    const c=document.createElement("canvas"); c.width=c.height=128;
    const g=c.getContext("2d");
    const r=g.createRadialGradient(64,64,2,64,64,64);
    r.addColorStop(0,"rgba(255,255,255,1)"); r.addColorStop(0.25,"rgba(255,255,255,0.55)");
    r.addColorStop(0.6,"rgba(255,255,255,0.12)"); r.addColorStop(1,"rgba(255,255,255,0)");
    g.fillStyle=r; g.fillRect(0,0,128,128);
    return new THREE.CanvasTexture(c);
  }
  const haloTex = haloTexture();
  const pkgMat = new THREE.MeshStandardMaterial({color:0xE8E4EF, roughness:0.35, metalness:0.15});
  const leds=[];
  for(let i=0;i<N;i++){
    const x=ledX(i), y=ledY(x)+0.16;
    const pkg=new THREE.Mesh(new THREE.BoxGeometry(0.44,0.12,0.44), pkgMat);
    pkg.position.set(x,y,0); scene.add(pkg);
    const domeMat=new THREE.MeshBasicMaterial({color:0x000000});
    const dome=new THREE.Mesh(new THREE.SphereGeometry(0.155,18,14), domeMat);
    dome.position.set(x,y+0.09,0); scene.add(dome);
    const sprMat=new THREE.SpriteMaterial({map:haloTex, color:0x000000,
      blending:THREE.AdditiveBlending, depthWrite:false, transparent:true, opacity:0.95});
    const halo=new THREE.Sprite(sprMat); halo.position.set(x,y+0.12,0.02); halo.scale.setScalar(0.001);
    scene.add(halo);
    leds.push({domeMat, halo});
  }

  /* roving lights: 4 point lights coloured by their local LEDs → fabric & table respond */
  scene.add(new THREE.AmbientLight(0x2A2140, 1.1));
  const key=new THREE.DirectionalLight(0x9C90C8, 0.25); key.position.set(-4,8,6); scene.add(key);
  const zone=[];
  for(let z=0;z<4;z++){ const L=new THREE.PointLight(0x000000, 0, 11, 2);
    L.position.set(X0+STRIP_LEN*(z+0.5)/4, 1.6, 0.7); scene.add(L); zone.push(L); }

  const v3=new THREE.Vector3();
  function resize(){
    const w=canvas.clientWidth, h=canvas.clientHeight;
    if(canvas.width!==Math.floor(w*renderer.getPixelRatio())){ renderer.setSize(w,h,false);
      camera.aspect=w/h; camera.updateProjectionMatrix(); }
  }

  return { frame(){
    resize();
    let ar=0, ag=0, ab=0;
    const zc=[[0,0,0,0],[0,0,0,0],[0,0,0,0],[0,0,0,0]];
    for(let i=0;i<N;i++){
      const r=sim.out[i*3], g=sim.out[i*3+1], b=sim.out[i*3+2];
      const {domeMat, halo}=leds[i];
      domeMat.color.setRGB(r,g,b);
      halo.material.color.setRGB(r,g,b);
      const lum=Math.max(r,g,b);
      halo.scale.setScalar(0.35+lum*3.1);
      ar+=r; ag+=g; ab+=b;
      const z=Math.min(3,(i/N*4)|0); zc[z][0]+=r; zc[z][1]+=g; zc[z][2]+=b; zc[z][3]++;
    }
    zone.forEach((L,z)=>{ const [r,g,b,n]=zc[z];
      L.color.setRGB(r/n,g/n,b/n); L.intensity=Math.min(2.4,(r+g+b)/n*2.6); });

    const t=now()*0.001;
    const sway=reduceMotion?0:Math.sin(t*0.16)*0.7;
    camera.position.set(sway+px*3.2, 5.4-py*1.6, 10.4);
    camera.lookAt(px*1.4, 0.55, 0);

    v3.set(ledX(0), ledY(ledX(0))+0.75, 0).project(camera);
    pix0Label.style.left=((v3.x*0.5+0.5)*canvas.clientWidth)+"px";
    pix0Label.style.top =((-v3.y*0.5+0.5)*canvas.clientHeight+8)+"px";
    pix0Label.style.opacity = v3.z<1 ? 1 : 0;

    renderer.render(scene,camera);
    return [ar/N, ag/N, ab/N];
  }};
}

function build2D(){
  const ctx = canvas.getContext("2d");
  return { frame(){
    const dpr=Math.min(devicePixelRatio,2);
    const w=canvas.clientWidth, h=canvas.clientHeight;
    if(canvas.width!==Math.floor(w*dpr)){ canvas.width=w*dpr; canvas.height=h*dpr; }
    ctx.setTransform(dpr,0,0,dpr,0,0);
    ctx.fillStyle="#0E0B14"; ctx.fillRect(0,0,w,h);
    const y0=h*0.55;
    ctx.fillStyle="#241a33"; ctx.fillRect(w*0.06,y0-14,w*0.88,28);       // ribbon
    ctx.strokeStyle="rgba(214,204,235,.5)"; ctx.setLineDash([10,8]); ctx.lineWidth=2;
    ctx.strokeRect(w*0.06+3,y0-11,w*0.88-6,22); ctx.setLineDash([]);     // stitches
    ctx.fillStyle="#231043"; ctx.fillRect(w*0.08-22,y0-20,44,40);        // pcb
    let ar=0, ag=0, ab=0;
    for(let i=0;i<N;i++){
      const r=sim.out[i*3], g=sim.out[i*3+1], b=sim.out[i*3+2];
      ar+=r; ag+=g; ab+=b;
      const x=w*0.08+(i/(N-1))*w*0.84;
      const lum=Math.max(r,g,b);
      const R=(r*255)|0, G=(g*255)|0, B=(b*255)|0;
      const rad=10+lum*28;
      const grad=ctx.createRadialGradient(x,y0,1,x,y0,rad);
      grad.addColorStop(0,`rgba(255,255,255,${0.9*lum})`);
      grad.addColorStop(0.3,`rgba(${R},${G},${B},${0.85*lum})`);
      grad.addColorStop(1,`rgba(${R},${G},${B},0)`);
      ctx.fillStyle=grad; ctx.beginPath(); ctx.arc(x,y0,rad,0,6.2832); ctx.fill();
      ctx.fillStyle=`rgb(${Math.max(R,26)},${Math.max(G,22)},${Math.max(B,34)})`;
      ctx.beginPath(); ctx.arc(x,y0,4.2,0,6.2832); ctx.fill();
    }
    pix0Label.style.left=(w*0.08)+"px";
    pix0Label.style.top =(y0+28)+"px";
    pix0Label.style.opacity=1;
    return [ar/N, ag/N, ab/N];
  }};
}

let view=null;
if(typeof THREE!=="undefined"){ try{ view=build3D(); }catch(e){ view=null; } }
if(!view) view=build2D();

/* ════════════════════════════════════════════════════════════════
   PART 3 — the signature: the page is lit by the LEDs.
   Average frame colour → CSS custom properties, every frame.
   ════════════════════════════════════════════════════════════════ */
const rootStyle=document.documentElement.style;
let fpsCount=0, fpsLast=now(), fpsVal=0;

function render(){
  requestAnimationFrame(render);
  if(document.hidden) return;
  sim.update();
  const [ar,ag,ab]=view.frame();

  const boost=1/Math.max(0.22,Math.max(ar,ag,ab));   // keep the accent readable
  const R=Math.min(255,ar*boost*255)|0, G=Math.min(255,ag*boost*255)|0, B=Math.min(255,ab*boost*255)|0;
  rootStyle.setProperty("--led",`rgb(${R},${G},${B})`);
  rootStyle.setProperty("--led-soft",`rgba(${R},${G},${B},0.14)`);

  fpsCount++;
  if(now()-fpsLast>1000){ fpsVal=fpsCount; fpsCount=0; fpsLast=now(); tickSecond(); }
  updateTelemetry();
}

/* ════════════════════════════════════════════════════════════════
   PART 4 — UI wiring. Every control maps to a library call by name.
   ════════════════════════════════════════════════════════════════ */
const $=id=>document.getElementById(id);
const SWATCHES=["#FF2A78","#FFB300","#00C8A0","#0A90FF","#9B5CFF","#FFFFFF"];
const chipsEl=$("fx-chips"), galleryEl=$("fx-gallery");
const FX_GRAD=["linear-gradient(90deg,#FF2A78,#FF2A78)",
 "linear-gradient(90deg,#332,#fff 30%,#332 34%,#332 60%,#fff 64%,#332)",
 "linear-gradient(90deg,transparent,#FF2A78 45%,#fff 50%,#FF2A78 55%,transparent)",
 "repeating-linear-gradient(90deg,#FF2A78 0 14px,#00C8A0 14px 28px)",
 "radial-gradient(circle at 50% 50%,#FF2A78,#400 70%)",
 "radial-gradient(circle at 20% 50%,#CFFF6A 4%,#111 22%),radial-gradient(circle at 70% 50%,#CFFF6A 3%,transparent 20%)",
 "linear-gradient(90deg,#802,#FF2A78,#802,#FF2A78,#802)",
 "linear-gradient(90deg,#FF7A00,#FFC94A,#FF5500,#FFB347)",
 "linear-gradient(90deg,#0d3,#0cc,#63f,#0d3)",
 "repeating-linear-gradient(90deg,#0A90FF 0 10px,#111 10px 16px,#0A90FF 16px 40px,#111 40px 52px)"];

FX_META.forEach(([name,desc],i)=>{
  const c=document.createElement("button"); c.className="chip"; c.type="button";
  c.innerHTML=`<span class="n">${i}</span>${name}`;
  c.setAttribute("aria-pressed", i===sim.fx);
  c.onclick=()=>applyEffect(i);
  chipsEl.appendChild(c);
  const card=document.createElement("button"); card.className="fx-card"; card.type="button";
  card.innerHTML=`<div class="strip" style="background:${FX_GRAD[i]}"></div>
    <h4><span>${String(i).padStart(2,"0")}</span>${name}</h4><p>${desc}</p><span class="try">run it ↑</span>`;
  card.onclick=()=>{applyEffect(i); $("stage-wrap").scrollIntoView({behavior:"smooth",block:"center"});};
  galleryEl.appendChild(card);
});
function applyEffect(i){
  sim.setEffect(i);
  chipsEl.querySelectorAll(".chip").forEach((c,k)=>c.setAttribute("aria-pressed",k===i));
  $("hud-fx").textContent=FX_META[i][0];
  $("morse-row").style.display = i===9 ? "flex" : "none";
  writeSketch();
}
SWATCHES.forEach((hex,k)=>{
  const b=document.createElement("button"); b.className="sw"; b.type="button";
  b.style.background=hex; b.setAttribute("aria-label","colour "+hex);
  b.setAttribute("aria-pressed", k===0);
  b.onclick=()=>{ setColor(hex);
    document.querySelectorAll(".sw").forEach(s=>s.setAttribute("aria-pressed",s===b)); };
  $("swatches").appendChild(b);
});
function setColor(hex){ sim.fxColor=parseInt(hex.slice(1),16); $("color-pick").value=hex; writeSketch(); }
$("color-pick").addEventListener("input",e=>{ setColor(e.target.value);
  document.querySelectorAll(".sw").forEach(s=>s.setAttribute("aria-pressed",false)); });

$("speed").oninput=e=>{ sim.setEffectSpeed(+e.target.value); $("speed-out").value=e.target.value; writeSketch(); };
$("bright").oninput=e=>{ sim.brightness=+e.target.value; $("bright-out").value=e.target.value; writeSketch(); };
$("budget").oninput=e=>{ sim.budgetmA=+e.target.value;
  $("budget-out").value=+e.target.value ? e.target.value+" mA" : "off";
  $("gauge-max").textContent=+e.target.value ? e.target.value+" mA budget" : "no budget set";
  writeSketch(); };
$("gamma-tg").onchange=e=>{ sim.gammaOn=e.target.checked; };
$("status-tg").onchange=e=>{ sim.statusOn=e.target.checked; writeSketch(); };
$("auto-tg").onchange=e=>{ autopilot=e.target.checked;
  $("autopilot-tag").style.display=autopilot?"inline-block":"none";
  $("laps-note").textContent=autopilot
    ? "(AutoPilot — LEDs render on core 0, loop() is all yours)"
    : "(manual mode — your loop() shares time with the LEDs)";
  writeSketch(); };
$("morse-in").addEventListener("input",e=>{
  const ok=sim.setMorseMessage(e.target.value);
  e.target.style.borderColor = ok ? "var(--line)" : "var(--bad)";
  if(!ok) logConsole(`setMorseMessage("${e.target.value}")`, sim.errorText());
  writeSketch(); });

/* AutoPilot laps demo — same numbers the real example prints */
let autopilot=false;
function tickSecond(){
  const laps = autopilot
    ? (420000+Math.random()*60000)|0
    : (52000+Math.random()*9000)|0;
  $("laps").textContent = laps.toLocaleString();
}

/* telemetry */
function updateTelemetry(){
  $("ma-out").innerHTML = sim.lastEstmA+" <small>mA</small>";
  $("iv-out").innerHTML = sim.frameInterval()+" <small>ms</small>";
  $("fps-out").textContent = fpsVal||"—";
  const st=$("st-out");
  if(sim.err===ERR.OK){ st.textContent="OK"; st.style.color="var(--ok)"; }
  else { st.textContent="ERR — "+Object.keys(ERR).find(k=>ERR[k]===sim.err); st.style.color="var(--bad)"; }
  const cap = sim.budgetmA || 1500;
  const pct = Math.min(100, sim.lastEstmA/cap*100);
  $("gauge-fill").style.width=pct+"%";
  $("gauge").classList.toggle("hot", sim.powerLimited);
  $("limited-tag").style.display = sim.powerLimited ? "inline-block" : "none";
}

/* error playground — the library's exact strings */
const consoleEl=$("console");
function logConsole(call, answer, good){
  const d=document.createElement("div"); d.className="line"+(good?" good":"");
  d.innerHTML=`<span>&gt; ${call}</span><br><b>${good?"✓":"✗"}</b> ${answer}`;
  consoleEl.prepend(d);
  while(consoleEl.children.length>7) consoleEl.lastChild.remove();
}
document.querySelectorAll(".err-btn").forEach(b=>b.onclick=()=>{
  const kind=b.dataset.err;
  if(kind==="index"){ sim.setPixel(999,255,0,0); logConsole("glow.setPixel(999, 255,0,0)", sim.errorText()); }
  if(kind==="effect"){ sim.setEffect(42); logConsole("glow.setEffect(42)", sim.errorText()); }
  if(kind==="morse"){ sim.setMorseMessage("HI!"); logConsole('glow.setMorseMessage("HI!")', sim.errorText()); }
  if(kind==="pin"){ sim.err=ERR.BAD_PIN; logConsole("CMozGlow bad(20, 27); bad.begin()", ERR_TEXT[2]); }
  if(kind==="clear"){ sim.clearError(); logConsole("glow.clearError()", "OK — everything is fine", true); }
  if(kind!=="clear" && !sim.statusOn){
    logConsole("hint","turn on the status pixel above to see pixel 0 go red", true); }
});

/* live sketch generator */
function hex6(c){ return "0x"+c.toString(16).toUpperCase().padStart(6,"0"); }
function writeSketch(){
  const [r,g,b]=sim.rgb();
  const fxConst=["CMOZ_FX_SOLID","CMOZ_FX_SEQUIN","CMOZ_FX_CATWALK","CMOZ_FX_LOOM","CMOZ_FX_HEARTBEAT",
    "CMOZ_FX_FIREFLY","CMOZ_FX_SILK","CMOZ_FX_EMBER","CMOZ_FX_AURORA","CMOZ_FX_MORSE"][sim.fx];
  const lines=[
`<span class="c">// Generated by the CMozGlow simulator — flash it, get exactly this.</span>`,
`<span class="k">#include</span> <span class="s">&lt;CMozGlow.h&gt;</span>`,``,
`<span class="k">const uint8_t</span>  EFFECT   = <span class="n">${sim.fx}</span>;   <span class="c">// ${FX_META[sim.fx][0]}</span>`,
`<span class="k">const uint16_t</span> NUM_LEDS = <span class="n">${N}</span>;  <span class="c">// onboard + 19-LED strip on GPIO 3</span>`,
`<span class="k">const uint8_t</span>  SPEED    = <span class="n">${sim.speed}</span>;`,
`<span class="k">const uint32_t</span> COLOR    = <span class="f">CMozGlow::Color</span>(<span class="n">${r}</span>, <span class="n">${g}</span>, <span class="n">${b}</span>);`,``,
`CMozGlow glow(NUM_LEDS);`,``,
`<span class="k">void</span> <span class="f">setup</span>() {`,
`  Serial.begin(<span class="n">115200</span>);`,
`  <span class="k">if</span> (!glow.<span class="f">begin</span>()) { Serial.println(glow.<span class="f">errorText</span>()); <span class="k">while</span>(<span class="n">1</span>) delay(<span class="n">1000</span>); }`,
`  glow.<span class="f">setPowerBudgetmA</span>(<span class="n">${sim.budgetmA}</span>);${sim.budgetmA===0?'  <span class="c">// unlimited — be careful!</span>':""}`,
`  glow.<span class="f">setBrightness</span>(<span class="n">${sim.brightness}</span>);`];
  if(sim.statusOn) lines.push(`  glow.<span class="f">statusPixel</span>(<span class="k">true</span>);`);
  lines.push(`  glow.<span class="f">setEffect</span>(EFFECT);`,
    `  glow.<span class="f">setEffectColor</span>(COLOR);`,
    `  glow.<span class="f">setEffectSpeed</span>(SPEED);`);
  if(sim.fx===9) lines.push(`  glow.<span class="f">setMorseMessage</span>(<span class="s">"${sim.mMsg}"</span>);`);
  if(autopilot) lines.push(`  glow.<span class="f">autoUpdate</span>(<span class="k">true</span>);   <span class="c">// ✈ LEDs move to core 0</span>`);
  lines.push(`}`,``,`<span class="k">void</span> <span class="f">loop</span>() {`,
    autopilot?`  <span class="c">// nothing! AutoPilot renders on core 0 — loop() is all yours</span>`
             :`  glow.<span class="f">update</span>();`, `}`);
  $("sketch").innerHTML=lines.join("\n");
}
$("copy-sketch").onclick=()=>{
  navigator.clipboard.writeText($("sketch").innerText).then(()=>{
    const t=$("toast"); t.classList.add("show"); setTimeout(()=>t.classList.remove("show"),1600);
  }).catch(()=>{});
};

/* scroll reveal */
const io=new IntersectionObserver(es=>es.forEach(e=>{
  if(e.isIntersecting){ e.target.classList.add("in"); io.unobserve(e.target); }
}),{threshold:0.12});
document.querySelectorAll(".reveal").forEach(el=>io.observe(el));

/* boot */
applyEffect(8);
writeSketch();
render();
</script>
</body>
</html>
