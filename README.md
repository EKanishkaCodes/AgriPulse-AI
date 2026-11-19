<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>AgriPulse AI – A crop doctor that speaks your language</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,500;12..96,700;12..96,800&family=Atkinson+Hyperlegible:wght@400;700&display=swap" rel="stylesheet">
<style>
:root{
  --bg:#f6ecd9;--surface:#fffaf0;--surface-glass:rgba(255,250,240,.92);
  --ink:#2e2016;--muted:#6b5846;--leaf:#5c3d1e;--leaf-accent:#3e7a2c;--leaf-ink:#fff8ec;
  --plant:#4f8a3c;--sun:#e8a823;--sun-ink:#2b1c00;--line:#dccaa8;--soft:#eddcb9;
  --warn:#b3402a;--ok:#2f8548;--laser:#39e56b;--shadow:0 18px 42px -12px rgba(46,32,22,.16);
  --ring-glow:rgba(79,138,60,.28);
  box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)
}
@media (prefers-color-scheme:dark){
  :root:not([data-theme="light"]){
    --bg:#19120b;--surface:#251a11;--surface-glass:rgba(37,26,17,.94);
    --ink:#f5e9d4;--muted:#c7b194;--leaf:#e0a84a;--leaf-accent:#7bbf63;--leaf-ink:#19120b;
    --plant:#7bbf63;--line:#43321f;--soft:#33241a;--warn:#ff9a82;--ok:#68d388;--laser:#5cff88;
    --shadow:0 20px 46px -12px rgba(0,0,0,.55);--ring-glow:rgba(123,191,99,.25)
  }
}
:root[data-theme="dark"]{
  --bg:#19120b;--surface:#251a11;--surface-glass:rgba(37,26,17,.94);
  --ink:#f5e9d4;--muted:#c7b194;--leaf:#e0a84a;--leaf-accent:#7bbf63;--leaf-ink:#19120b;
  --plant:#7bbf63;--line:#43321f;--soft:#33241a;--warn:#ff9a82;--ok:#68d388;--laser:#5cff88;
  --shadow:0 20px 46px -12px rgba(0,0,0,.55);--ring-glow:rgba(123,191,99,.25)
}
html{scroll-padding-top:76px;scroll-behavior:smooth}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font:400 18px/1.6 "Atkinson Hyperlegible",system-ui,sans-serif;overflow-x:hidden;padding-bottom:84px}
h1,h2,h3{font-family:"Bricolage Grotesque",Georgia,sans-serif;line-height:1.1;margin:0 0 .5em;letter-spacing:-.02em}
h1{font-size:clamp(2.2rem,6vw,4.1rem);font-weight:800}
h2{font-size:clamp(1.65rem,3.8vw,2.5rem);font-weight:800}
h3{font-size:1.2rem;font-weight:700}
a{color:var(--leaf)}
:focus-visible{outline:3px solid var(--sun);outline-offset:3px}
.wrap{max-width:1120px;margin:0 auto;padding:0 20px;position:relative;z-index:2}

/* Top Navbar */
.topbar{position:sticky;top:0;z-index:40;background:var(--surface-glass);backdrop-filter:blur(12px);-webkit-backdrop-filter:blur(12px);border-bottom:2px solid var(--line)}
nav{display:flex;justify-content:space-between;align-items:center;padding:12px 0;gap:12px;flex-wrap:wrap}
.logo{font:800 1.35rem "Bricolage Grotesque",sans-serif;color:var(--leaf);text-decoration:none;display:inline-flex;align-items:center;gap:8px}
.nr{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
.btn{background:var(--leaf);color:var(--leaf-ink);padding:11px 18px;border-radius:999px;font-weight:700;text-decoration:none;border:2px solid transparent;font-size:.98rem;font-family:inherit;cursor:pointer;display:inline-flex;align-items:center;justify-content:center;gap:8px;min-height:46px;transition:transform .15s,box-shadow .15s,background .2s}
.btn:hover{transform:translateY(-1px);box-shadow:0 6px 16px rgba(0,0,0,.12)}
.btn:active{transform:translateY(0)}
.btn:disabled{opacity:.6;cursor:not-allowed;transform:none}
.btn.alt{background:var(--surface);color:var(--ink);border:2px solid var(--line)}
.btn.primary-green{background:var(--leaf-accent);color:#fff}

/* Smooth blink/pulse animation on click */
@keyframes clickBlink{
  0%{transform:scale(1);box-shadow:0 0 0 0 rgba(232,168,35,.85);filter:brightness(1)}
  25%{transform:scale(1.04);box-shadow:0 0 0 10px rgba(232,168,35,.45);filter:brightness(1.18)}
  50%{transform:scale(.97);box-shadow:0 0 0 4px rgba(57,229,107,.65);filter:brightness(.92)}
  75%{transform:scale(1.03);box-shadow:0 0 0 12px rgba(57,229,107,.25);filter:brightness(1.12)}
  100%{transform:scale(1);box-shadow:0 0 0 0 rgba(57,229,107,0);filter:brightness(1)}
}
.blink-active{
  animation:clickBlink .55s cubic-bezier(.22,1,.36,1);
  z-index:10;
}
.section-reveal-flash{
  animation:sectionFlash .75s ease-out;
}
@keyframes sectionFlash{
  0%{background-color:rgba(232,168,35,.18)}
  100%{background-color:transparent}
}
@media (prefers-reduced-motion:reduce){
  .blink-active,.section-reveal-flash{animation:none!important}
}

#lang{font:700 1rem inherit;font-family:inherit;padding:10px 14px;border-radius:999px;border:2px solid var(--line);background:var(--surface);color:var(--ink);min-height:46px;cursor:pointer}

/* Hero Section */
.hero-wrap{position:relative;overflow:hidden;padding:28px 0 56px;border-bottom:2px solid var(--line);background:radial-gradient(circle at 82% 18%,rgba(232,168,35,.22),transparent 46%),radial-gradient(circle at 12% 75%,rgba(79,138,60,.16),transparent 50%)}
.hero-hills{position:absolute;left:0;right:0;bottom:-2px;width:100%;height:120px;pointer-events:none;z-index:1;opacity:.42}
.hero{display:grid;grid-template-columns:1.08fr .92fr;gap:32px;align-items:center}
.eyebrow{display:inline-flex;align-items:center;gap:8px;background:var(--surface);border:2px solid var(--line);border-radius:999px;padding:6px 14px;font-weight:700;font-size:.9rem;color:var(--leaf-accent);margin-bottom:14px}
.pulse-dot{width:10px;height:10px;border-radius:50%;background:var(--laser);box-shadow:0 0 0 4px var(--ring-glow);animation:pulseDot 2s infinite}
@keyframes pulseDot{0%,100%{transform:scale(1);opacity:1}50%{transform:scale(1.25);opacity:.7}}
.hero p.lead{font-size:1.15rem;color:var(--muted);max-width:44ch;margin:0 0 16px}
.chips{display:flex;gap:10px;flex-wrap:wrap;margin-top:14px}
.chips span{background:var(--surface-glass);border:2px solid var(--line);border-radius:999px;padding:8px 14px;font-weight:700;font-size:.94rem}
.row{display:flex;gap:12px;flex-wrap:wrap;margin-top:18px}

/* Always-Visible Hero Phone + Active Leaf Scan Preview */
.phone{background:var(--surface-glass);backdrop-filter:blur(14px);border:2px solid var(--line);border-radius:34px;padding:18px;max-width:420px;margin-inline:auto;width:100%;box-shadow:var(--shadow)}
.phone-top{display:flex;justify-content:space-between;align-items:center;gap:8px;margin-bottom:10px;flex-wrap:wrap}
.scan-badge{display:inline-flex;align-items:center;gap:6px;background:var(--soft);border:1.5px solid var(--line);border-radius:999px;padding:4px 12px;font-size:.84rem;font-weight:700;color:var(--ink)}

/* Live Leaf Scan Viewport inside Hero Phone */
.hero-scan-box{position:relative;height:152px;border-radius:20px;overflow:hidden;border:2px solid var(--line);margin:8px 0 12px;background:#1d2e1e}
.hero-scan-box img{width:100%;height:100%;object-fit:cover;display:block}
.scan-frame-reticle{position:absolute;inset:10px;border:2px dashed rgba(92,255,136,.85);border-radius:14px;pointer-events:none;animation:reticlePulse 2.2s ease-in-out infinite}
@keyframes reticlePulse{0%,100%{opacity:.55;transform:scale(1)}50%{opacity:1;transform:scale(.985)}}
.scan-beam-line{position:absolute;left:0;right:0;height:4px;background:linear-gradient(90deg,transparent,var(--laser),var(--sun),var(--laser),transparent);box-shadow:0 0 14px 3px rgba(57,229,107,.8);top:0;pointer-events:none;animation:beamSweep 2.6s ease-in-out infinite}
.scan-beam-glow{position:absolute;left:0;right:0;height:36px;background:linear-gradient(180deg,rgba(57,229,107,.28),transparent);top:0;pointer-events:none;animation:beamSweep 2.6s ease-in-out infinite}
@keyframes beamSweep{
  0%{transform:translateY(0)}
  50%{transform:translateY(114px)}
  100%{transform:translateY(0)}
}
@media (prefers-reduced-motion:reduce){
  .scan-beam-line,.scan-beam-glow,.scan-frame-reticle,.pulse-dot{animation:none!important}
}
.hero-scan-hud{position:absolute;bottom:8px;left:8px;right:8px;display:flex;justify-content:space-between;align-items:center;gap:6px;pointer-events:none}
.hud-pill{background:rgba(22,17,12,.82);color:#fff;font-size:.76rem;font-weight:700;padding:4px 10px;border-radius:999px;backdrop-filter:blur(4px)}

.diag{margin-top:10px}
.diag b{font:800 1.25rem "Bricolage Grotesque",sans-serif;display:block}
.bar{height:10px;background:var(--soft);border-radius:6px;margin:8px 0;overflow:hidden}
.bar i{display:block;height:100%;width:91%;background:var(--leaf-accent);border-radius:6px;transition:width .4s ease,background .3s ease}
.say{background:var(--soft);border-radius:16px;padding:12px 14px;margin:10px 0;font-size:.98rem;min-height:4.6em}
.play{width:100%;background:var(--sun);color:var(--sun-ink);font-size:1.15rem;padding:12px;border-radius:16px;border:0;font-weight:700;cursor:pointer;min-height:48px}

/* Sections */
.band{background:color-mix(in srgb,var(--soft) 72%,transparent);border-block:1px solid var(--line)}
section{padding:44px 0}
.flow{display:grid;gap:14px;margin-top:18px}
.fl{display:flex;align-items:center;gap:10px;flex-wrap:wrap}
.fl>b{flex:0 0 132px;font:800 1.15rem "Bricolage Grotesque",sans-serif}
.fl .sp{background:var(--surface);border:2px solid var(--line);border-radius:16px;padding:11px 16px;font-weight:700;font-size:1.05rem}
.fl.aft .sp{border-color:var(--leaf-accent)}
.fl .ar{font-size:1.4rem;color:var(--muted)}

/* 4 Steps */
.steps{list-style:none;padding:0;margin:22px 0 0;display:grid;grid-template-columns:repeat(4,1fr);gap:16px;counter-reset:s}
.steps li{counter-increment:s;background:var(--surface);border:2px solid var(--line);border-top:5px solid var(--leaf);border-radius:18px;padding:14px 16px;cursor:pointer;transition:transform .2s,border-color .2s,box-shadow .2s}
.steps li::before{content:counter(s);font:800 1.6rem "Bricolage Grotesque",sans-serif;color:var(--leaf);display:inline-block;margin-right:8px}
.steps li.on{border-top-color:var(--sun);background:var(--soft);transform:translateY(-2px);box-shadow:var(--shadow)}
.steps li.done{border-top-color:var(--leaf-accent)}
.steps li.done::before{content:"✓ ";color:var(--leaf-accent)}

/* Main Interactive Scan Lab (Always Open & Visible) */
.lab{margin-top:24px;background:var(--surface);border:2px solid var(--line);border-radius:24px;padding:22px;box-shadow:var(--shadow)}
.lab-grid{display:grid;grid-template-columns:1.05fr .95fr;gap:24px;align-items:start;margin-top:16px}
.main-scan-stage{position:relative;border-radius:20px;overflow:hidden;border:2px solid var(--line);background:#1b281c;aspect-ratio:4/3;max-height:340px;width:100%}
.main-scan-stage img{width:100%;height:100%;object-fit:cover;display:block}
.main-scan-box{position:absolute;inset:14px;border:2.5px solid var(--laser);border-radius:14px;box-shadow:inset 0 0 18px rgba(57,229,107,.25);pointer-events:none;animation:reticlePulse 2s ease-in-out infinite}
.main-laser-beam{position:absolute;left:0;right:0;height:5px;background:linear-gradient(90deg,transparent,var(--laser),#fff3a1,var(--laser),transparent);box-shadow:0 0 18px 4px rgba(57,229,107,.85);top:0;pointer-events:none;animation:mainBeamSweep 2.5s ease-in-out infinite}
.main-laser-shade{position:absolute;left:0;right:0;height:56px;background:linear-gradient(180deg,rgba(57,229,107,.28),transparent);top:0;pointer-events:none;animation:mainBeamSweep 2.5s ease-in-out infinite}
@keyframes mainBeamSweep{
  0%{transform:translateY(0)}
  50%{transform:translateY(275px)}
  100%{transform:translateY(0)}
}
@media (prefers-reduced-motion:reduce){
  .main-laser-beam,.main-laser-shade,.main-scan-box{animation:none!important}
}
.stage-top-bar{position:absolute;top:10px;left:10px;right:10px;display:flex;justify-content:space-between;gap:8px;pointer-events:none;flex-wrap:wrap}
.stage-tag{background:rgba(20,16,11,.84);color:#fff;font-size:.8rem;font-weight:700;padding:5px 11px;border-radius:999px;backdrop-filter:blur(4px)}
.stage-tag.active-scan{background:rgba(30,115,45,.92);color:#eaffef}

.sw{display:inline-flex;gap:8px;align-items:center;font-weight:700;padding:8px 12px;background:var(--soft);border-radius:999px;cursor:pointer}
.sw input{width:20px;height:20px;accent-color:var(--leaf-accent)}
.grow{margin-bottom:14px}
.grow p{font-weight:700;margin:0 0 8px}
.gb{display:block;text-align:start;font:inherit;font-family:inherit;padding:12px 14px;border-radius:16px;border:2.5px solid var(--line);background:var(--surface);color:var(--ink);cursor:pointer;min-width:190px;flex:1}
.gb[aria-pressed="true"]{border-color:var(--leaf-accent);background:var(--soft)}
.gb span{display:block;font-weight:700;font-size:1.05rem}
.gb .ex{font-weight:400;font-size:.9rem;color:var(--muted)}

#log{margin:12px 0 0;padding-inline-start:20px}
#log li{margin-bottom:6px;font-size:.98rem}
.ai-result-card{margin-top:14px;padding:14px 16px;border-radius:16px;background:var(--soft);border-left:5px solid var(--leaf-accent);font-weight:700}

/* Database Status & Sync History Panel */
.db-panel{margin-top:18px;background:var(--bg);border:2px solid var(--line);border-radius:20px;padding:16px}
.db-top{display:flex;justify-content:space-between;align-items:center;gap:10px;flex-wrap:wrap;margin-bottom:10px}
.db-status-pill{display:inline-flex;align-items:center;gap:6px;background:var(--surface);border:1.5px solid var(--line);border-radius:999px;padding:5px 12px;font-size:.86rem;font-weight:700}
.db-status-pill.synced{border-color:var(--leaf-accent);color:var(--leaf-accent)}
.db-status-pill.queued{border-color:var(--sun);color:var(--leaf)}
.db-list{list-style:none;margin:8px 0 0;padding:0;display:grid;gap:8px;max-height:220px;overflow-y:auto}
.db-item{background:var(--surface);border:1.5px solid var(--line);border-radius:14px;padding:10px 12px;display:flex;justify-content:space-between;align-items:flex-start;gap:10px;font-size:.9rem}
.db-item b{display:block;font-size:.96rem}
.db-item small{color:var(--muted);display:block}
.db-tag{font-size:.76rem;font-weight:700;padding:3px 9px;border-radius:999px;background:var(--soft);white-space:nowrap}
.db-tag.cloud{background:rgba(62,122,44,.14);color:var(--leaf-accent)}
.db-tag.offline-q{background:rgba(232,168,35,.22);color:var(--ink)}

/* Cards & Keypad */
.cards{display:grid;grid-template-columns:repeat(auto-fit,minmax(210px,1fr));gap:16px;margin-top:20px}
.card{background:var(--surface);border:2px solid var(--line);border-radius:20px;padding:20px;text-align:center;box-shadow:0 8px 20px -10px rgba(0,0,0,.08)}
.card i{font-style:normal;font-size:2.6rem;display:block}
.card h3{margin:.4em 0 .2em}
.card p{margin:0;color:var(--muted);font-size:.96rem}
.pill{display:inline-block;background:var(--soft);border:1.5px solid var(--line);border-radius:999px;padding:10px 18px;font-weight:700;margin-top:18px}
.note{border-inline-start:5px solid var(--sun);padding:6px 16px;max-width:60ch;color:var(--muted)}

.kp{max-width:310px;margin-inline:auto;width:100%;background:var(--surface);border:2px solid var(--line);border-radius:24px;padding:16px;box-shadow:var(--shadow)}
.kd{min-height:9.5em;max-height:15em;overflow-y:auto;white-space:pre-line;background:var(--soft);border-radius:14px;padding:12px;font-size:.95rem}
.kk{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin-top:12px}
.kk button{padding:13px;font:800 1.25rem inherit;font-family:inherit;border-radius:12px;border:2px solid var(--line);background:var(--surface);color:var(--ink);cursor:pointer;min-height:48px}
.kk button:hover{background:var(--soft)}

/* AgriBot Mascot */
.bot-wrap{display:flex;align-items:center;gap:14px;background:var(--soft);border:1.5px solid var(--line);border-radius:22px;padding:10px 14px;margin-bottom:10px}
.bot svg{width:84px;height:84px;flex-shrink:0;animation:bob 3.4s ease-in-out infinite}
.bm{font-weight:700;font-size:1rem;line-height:1.35}
.bot .hd{fill:var(--surface);stroke:var(--leaf);stroke-width:6}
.bot .sc{fill:var(--bg)}
.bot .ey{fill:var(--ink)}
.bot .ln{fill:none;stroke:var(--ink);stroke-width:5;stroke-linecap:round}
.bot .bd,.bot .arm{fill:var(--leaf)}
.bot .an{fill:var(--sun)}
.bot .lf2{fill:var(--plant)}
.bot .ch{fill:#f08a7a;opacity:.75}
.bot .tr{fill:#6ab0e0}
.bot svg g[class]{display:none}
.bot:not(.ok):not(.mid):not(.bad) .eN,.bot:not(.ok):not(.mid):not(.bad) .mN{display:inline}
.bot.ok .eH,.bot.ok .mH,.bot.ok .ch{display:inline}
.bot.mid .eW,.bot.mid .mW{display:inline}
.bot.bad .eS,.bot.bad .mS{display:inline}
.bot.ok svg{animation:hop .9s ease-in-out infinite}
.bot.mid svg{animation:sway2 2s ease-in-out infinite}
.bot.bad svg{animation:none;transform:rotate(-4deg)}
@keyframes bob{50%{transform:translateY(-4px)}}
@keyframes hop{50%{transform:translateY(-8px)}}
@keyframes sway2{50%{transform:rotate(3deg)}}

/* Background Plants & Soil */
.bgp{position:fixed;inset:0;z-index:-1;pointer-events:none;overflow:hidden}
.pl{position:absolute;bottom:0;width:auto;opacity:.26;transform-origin:50% 100%;transform:scaleX(var(--f,1)) rotate(calc(var(--sw,0) * 1deg));transition:transform .6s ease-out}
.pl .st{fill:none;stroke:var(--plant);stroke-width:5;stroke-linecap:round;stroke-dasharray:1;stroke-dashoffset:clamp(0,calc(1 - var(--g,0) * 1.15),1);transition:stroke-dashoffset .5s ease-out}
.pl .lf{fill:var(--plant);transform-box:fill-box;transform-origin:0 50%;transform:scale(clamp(0,calc((var(--g,0) - var(--t,0)) * 8),1));transition:transform .5s ease-out}
.pl .fr{fill:#e5533d;transform-box:fill-box;transform-origin:center;transform:scale(clamp(0,calc((var(--g,0) - .85) * 12),1));transition:transform .5s ease-out}
.sun{position:absolute;width:64px;height:64px;border-radius:50%;background:var(--sun);opacity:.32;left:calc(var(--p,0) * 85%);top:calc(10% + (var(--p,0) - .5) * (var(--p,0) - .5) * 40%);transition:left .5s ease-out,top .5s ease-out}
.bgp.ok .pl .lf{fill:#2f9e4f}
.bgp.mid .pl .lf{fill:#c9a227}
.bgp.bad .pl .lf{fill:#8a5a2b}
.gd{position:absolute;left:0;right:0;bottom:0;height:8vh;opacity:0;transition:opacity .6s}
.bgp.dry .gd{opacity:.5;background:repeating-linear-gradient(115deg,#d9c08a 0 14px,#c8a96b 14px 16px)}
.bgp.good .gd{opacity:.5;background:linear-gradient(#5a3d28,#3b2a1c)}
.bgp.wet .gd{opacity:.5;background:linear-gradient(#5f7480,#3e5560)}

/* Presentation Deck */
#deck{position:fixed;inset:0;z-index:100;background:var(--bg);display:none;flex-direction:column}
#deck.on{display:flex}
.dk-bar{height:6px;background:var(--line)}.dk-bar i{display:block;height:100%;width:0;background:var(--leaf);transition:width .4s}
.dk-stage{flex:1;position:relative;overflow:hidden;cursor:pointer}
.dk-s{position:absolute;inset:0;display:flex;flex-direction:column;justify-content:center;padding:4vh 8vw;opacity:0;transform:translateX(8vw);transition:opacity .45s,transform .45s;pointer-events:none}
.dk-s.cur{opacity:1;transform:none}
.dk-s h2{font-size:clamp(2rem,6vw,4rem);margin-bottom:.5em}
.dk-s ul{list-style:none;padding:0;margin:0;max-width:30em}
.dk-s li{font-size:clamp(1.1rem,2.6vw,1.75rem);margin:.5em 0;padding-inline-start:14px;border-inline-start:5px solid var(--sun);opacity:0;transform:translateY(12px);transition:opacity .4s,transform .4s}
.dk-s li.show{opacity:1;transform:none}
.dk-ft{display:flex;justify-content:space-between;align-items:center;gap:10px;flex-wrap:wrap;padding:12px 20px;border-top:1px solid var(--line)}
.dk-ft div{display:flex;gap:8px;align-items:center}

/* Quick Bottom Dock */
.quick-dock{position:fixed;bottom:12px;left:50%;transform:translateX(-50%);z-index:45;display:flex;align-items:center;gap:6px;background:var(--surface-glass);backdrop-filter:blur(14px);border:2px solid var(--line);border-radius:999px;padding:6px 10px;box-shadow:var(--shadow);max-width:calc(100vw - 20px);overflow-x:auto}
.quick-dock a,.quick-dock button{background:transparent;color:var(--ink);border:0;border-radius:999px;padding:8px 12px;font:700 .88rem inherit;font-family:inherit;text-decoration:none;white-space:nowrap;cursor:pointer;min-height:38px;display:inline-flex;align-items:center;gap:5px}
.quick-dock a:hover,.quick-dock button:hover{background:var(--soft)}
.quick-dock .dock-scan{background:var(--leaf-accent);color:#fff}

footer{padding:32px 0;color:var(--muted);border-top:2px solid var(--line);font-size:.95rem}

@media (max-width:860px){
  .hero,.lab-grid{grid-template-columns:1fr}
  .steps{grid-template-columns:1fr 1fr}
}
@media (max-width:520px){
  .steps{grid-template-columns:1fr}
}
</style>
</head>
<body>
<div class="bgp" id="bgp" aria-hidden="true"></div>

<div class="topbar">
  <div class="wrap">
    <nav>
      <a class="logo" href="#top">🌱 AgriPulse AI</a>
      <span class="nr">
        <button class="btn alt" id="pres" type="button">▶ Present</button>
        <select id="lang" aria-label="Language"></select>
        <a class="btn primary-green" href="#scan-lab">📷 Scan Leaf</a>
      </span>
    </nav>
  </div>
</div>

<div class="hero-wrap" id="top">
  <div class="wrap">
    <header class="hero">
      <div>
        <div class="eyebrow"><span class="pulse-dot"></span><span>Offline-First Crop Vision &amp; Voice Agronomist</span></div>
        <h1>A crop doctor that talks like your neighbor.</h1>
        <p class="lead">Point your camera at a plant leaf to see live AI scanning, check root or fruit health, and hear remedies in your language.</p>
        <div class="chips">
          <span>📴 Works offline</span>
          <span>🗣️ Speaks your language</span>
          <span>📱 Any phone</span>
        </div>
        <div class="row">
          <a class="btn primary-green" href="#scan-lab">📷 Scan a Leaf Now</a>
          <a class="btn alt" href="#anyphone">📞 Keypad Helpline</a>
        </div>
      </div>

      <!-- Always-Visible Hero Phone with Live Scanning Animation -->
      <div class="phone" aria-label="Live leaf scan preview card">
        <div class="phone-top">
          <span class="scan-badge" id="modeBadge">✨ Live Leaf Scanner</span>
          <span class="scan-badge" id="heroStatusChip">🔍 Ready</span>
        </div>

        <div class="hero-scan-box" id="heroScanBox">
          <img id="heroLeafImg" src="" alt="Scanned plant leaf preview">
          <div class="scan-frame-reticle"></div>
          <div class="scan-beam-glow"></div>
          <div class="scan-beam-line"></div>
          <div class="hero-scan-hud">
            <span class="hud-pill" id="heroScanTag">⚡ Scanning Leaf...</span>
            <span class="hud-pill" id="heroCropTag">🍅 Above Soil</span>
          </div>
        </div>

        <div class="bot-wrap">
          <div class="bot" id="bot">
            <svg viewBox="0 0 200 200" role="img" aria-label="AgriBot mascot">
              <line class="ln" x1="100" y1="34" x2="100" y2="16"/>
              <circle class="an" cx="100" cy="12" r="7"/>
              <path class="lf2" d="M104 14 C116 4 128 8 130 18 C120 24 108 22 104 14Z"/>
              <rect class="hd" x="30" y="34" width="140" height="112" rx="38"/>
              <rect class="sc" x="46" y="52" width="108" height="76" rx="26"/>
              <g class="eN"><circle class="ey" cx="80" cy="88" r="8"/><circle class="ey" cx="120" cy="88" r="8"/></g>
              <g class="eH"><path class="ln" d="M70 92 Q80 76 90 92"/><path class="ln" d="M110 92 Q120 76 130 92"/></g>
              <g class="eW"><circle class="ey" cx="80" cy="90" r="8"/><circle class="ey" cx="120" cy="90" r="8"/><path class="ln" d="M68 74 L90 80"/><path class="ln" d="M132 74 L110 80"/></g>
              <g class="eS"><circle class="ey" cx="80" cy="92" r="8"/><circle class="ey" cx="120" cy="92" r="8"/><path class="ln" d="M68 80 L90 74"/><path class="ln" d="M132 80 L110 74"/><path class="tr" d="M78 104 q-5 9 0 13 q5 -4 0 -13z"/></g>
              <g class="mN"><path class="ln" d="M86 108 Q100 118 114 108"/></g>
              <g class="mH"><path class="ln" d="M76 104 Q100 128 124 104"/></g>
              <g class="mW"><path class="ln" d="M84 114 L116 110"/></g>
              <g class="mS"><path class="ln" d="M80 118 Q100 100 120 118"/></g>
              <g class="ch"><circle cx="62" cy="108" r="7"/><circle cx="138" cy="108" r="7"/></g>
              <rect class="bd" x="64" y="150" width="72" height="38" rx="16"/>
              <path class="lf2" d="M100 160 c-10 -2 -14 6 -14 14 c8 2 16 -2 14 -14z"/>
              <rect class="arm" x="138" y="156" width="34" height="12" rx="6"/>
            </svg>
          </div>
          <div class="bm" id="bm">Show me a leaf!</div>
        </div>

        <div class="diag">
          <b id="diagTitle">Tomato Early Blight</b>
          <span id="diagMeta">Severity: Moderate · Confidence 91%</span>
          <div class="bar"><i id="diagBar"></i></div>
        </div>
        <div class="say" id="say" aria-live="polite"></div>
        <button class="play" id="play" type="button" aria-label="Play advice aloud">🔊 Listen</button>
      </div>
    </header>
  </div>

  <svg class="hero-hills" viewBox="0 0 1440 120" preserveAspectRatio="none" aria-hidden="true">
    <path fill="var(--plant)" fill-opacity="0.22" d="M0,64 C280,15 560,105 880,58 C1140,20 1300,72 1440,48 L1440,120 L0,120 Z"/>
    <path fill="var(--leaf)" fill-opacity="0.16" d="M0,84 C360,42 720,115 1120,68 C1280,48 1370,76 1440,66 L1440,120 L0,120 Z"/>
  </svg>
</div>

<div class="band">
  <div class="wrap">
    <section>
      <h2>What changes for a farmer</h2>
      <div class="flow">
        <div class="fl"><b>Before</b><span class="sp">🍅 Spots appear</span><span class="ar">→</span><span class="sp">🚌 Advisor is a day away</span><span class="ar">→</span><span class="sp">🤷 Guess or wait</span></div>
        <div class="fl aft"><b>With AgriPulse</b><span class="sp">📷 Take a photo</span><span class="ar">→</span><span class="sp">🔊 Hear the fix</span><span class="ar">→</span><span class="sp">✅ Treat today</span></div>
      </div>
    </section>
  </div>
</div>

<div class="wrap">
  <section id="scan-lab">
    <h2>From leaf to advice in four steps</h2>
    <ol class="steps" id="steps">
      <li><h3>📷 Take a photo</h3></li>
      <li><h3>⚡ Instant guess</h3></li>
      <li><h3>🔎 Online check</h3></li>
      <li><h3>🔊 Hear the plan</h3></li>
    </ol>

    <!-- Always-Visible Scan Lab with Live Leaf Animation & Controls -->
    <div class="lab">
      <div class="grow" role="group" aria-label="Where does your crop grow?">
        <p>Where does your crop grow?</p>
        <div class="row" style="margin-top:0">
          <button class="gb" data-g="under" type="button" aria-pressed="false">
            <span>🥔 Under the soil</span>
            <span class="ex">Potato, carrot, groundnut, onion</span>
          </button>
          <button class="gb" data-g="above" type="button" aria-pressed="true">
            <span>🍅 Above the soil</span>
            <span class="ex">Tomato, mango, chilli, banana</span>
          </button>
        </div>
      </div>

      <div class="row" style="margin-top:0;align-items:center">
        <button class="btn" id="cam" type="button">📷 Take photo</button>
        <label class="btn alt" for="pic">🖼️ Choose photo</label>
        <input id="pic" type="file" accept="image/*" hidden>
        <button class="btn primary-green" id="submit" type="button">Submit</button>
        <label class="sw"><input type="checkbox" id="net" checked> Phone has signal</label>
        <button class="btn alt" id="run" type="button">▶ Run the four steps</button>
      </div>

      <div class="grow" style="margin-top:14px">
        <p>See an example</p>
        <div class="row" style="margin-top:0">
          <button class="btn alt exb" data-s="ok" type="button">✅ Healthy</button>
          <button class="btn alt exb" data-s="mid" type="button">⚠️ Needs care</button>
          <button class="btn alt exb" data-s="bad" type="button">❗ Serious</button>
          <button class="btn alt exb" data-so="dry" type="button">🏜️ Dry soil</button>
          <button class="btn alt exb" data-so="good" type="button">🌱 Good soil</button>
          <button class="btn alt exb" data-so="wet" type="button">🌊 Too wet</button>
        </div>
      </div>

      <div id="camwrap" hidden>
        <video id="vid" autoplay playsinline muted style="width:100%;max-width:420px;border-radius:16px;margin-top:14px;background:#000"></video>
        <div class="row" style="margin-top:10px">
          <button class="btn primary-green" id="snap" type="button">Capture</button>
          <button class="btn alt" id="camx" type="button">Cancel</button>
        </div>
      </div>

      <div class="lab-grid">
        <div>
          <div class="main-scan-stage" id="mainScanStage">
            <img id="prev" src="" alt="Plant leaf scan preview">
            <div class="main-scan-box"></div>
            <div class="main-laser-shade"></div>
            <div class="main-laser-beam"></div>
            <div class="stage-top-bar">
              <span class="stage-tag active-scan" id="stageScanState">🟢 AI SCANNER ACTIVE</span>
              <span class="stage-tag" id="stageMetricsTag">🌿 Leaf &amp; Spot Scan</span>
            </div>
          </div>
          <div class="ai-result-card" id="aiDiagnosisBox">
            Ready to scan: pick a photo, click Submit, or run the four steps.
          </div>
        </div>

        <div>
          <h3 style="margin-bottom:6px">Step-by-step log</h3>
          <ol id="log" aria-live="polite"></ol>

          <div class="db-panel" id="dbPanel">
            <div class="db-top">
              <span class="db-status-pill synced" id="dbStatusBadge">☁️ Database: Ready</span>
              <button class="btn alt" id="syncNowBtn" type="button" style="min-height:36px;padding:6px 12px;font-size:.86rem">🔄 Sync Queue</button>
            </div>
            <small id="dbSummaryText" style="color:var(--muted);display:block">Cloud Firestore + Offline IndexedDB queue connected.</small>
            <ul class="db-list" id="dbHistoryList"></ul>
          </div>
        </div>
      </div>

      <p class="pill">📴 No signal? The photo and scan log save locally and sync automatically when signal returns.</p>
    </div>
  </section>

  <section id="features">
    <h2>Built for sunlight, gloves and low-end phones</h2>
    <div class="cards">
      <div class="card"><i>🔍</i><h3>Leaf and pest scanner</h3><p>Names the disease with a % score</p></div>
      <div class="card"><i>🗣️</i><h3>Voice, not typing</h3><p>Talk and listen, no reading</p></div>
      <div class="card"><i>🌿</i><h3>Affordable remedies first</h3><p>Neem, compost, water, before chemicals</p></div>
      <div class="card"><i>📴</i><h3>Works offline, improves online</h3><p>Photos wait and send later</p></div>
    </div>
  </section>

  <section id="anyphone">
    <div class="hero" style="padding:0">
      <div>
        <h2>No smartphone? Use any phone.</h2>
        <div class="cards">
          <div class="card"><i>📞</i><h3>Call</h3><p>Press the disease number</p></div>
          <div class="card"><i>💬</i><h3>Text</h3><p>Send the problem</p></div>
          <div class="card"><i>🧑‍🌾</i><h3>Village helper</h3><p>Scans the leaf for you</p></div>
        </div>
        <p class="note" style="margin-top:20px">Keypad phones work from symptoms, not photos. The keypad here is a simulation.</p>
      </div>
      <div class="kp">
        <div class="kd" id="kd" aria-live="polite"></div>
        <div class="kk" id="kk"></div>
      </div>
    </div>
  </section>
</div>

<div class="wrap">
  <footer>🌱 AgriPulse AI · Offline-first crop protection for smallholder farmers. Agriculture and Rural Development project.</footer>
</div>

<!-- Quick Bottom Dock for Easy Access Below Fold -->
<div class="quick-dock" role="navigation" aria-label="Quick access bar">
  <a href="#top">🤖 AgriBot</a>
  <a href="#scan-lab" class="dock-scan">📷 Scanner</a>
  <a href="#features">🌿 Features</a>
  <a href="#anyphone">📞 Keypad</a>
  <button type="button" id="dockListen">🔊 Voice</button>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
import { getFirestore, doc, getDoc, setDoc, collection, onSnapshot, addDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

(function(){
  /* Default SVG Leaf Scenes (Healthy, Needs Care, Serious) */
  function makeLeafSvgDataUrl(mode){
    var bg1 = mode === "ok" ? "#2e7d32" : mode === "bad" ? "#4a3219" : "#2f5e2e";
    var bg2 = mode === "ok" ? "#1b4d20" : mode === "bad" ? "#291a0c" : "#1c3b1d";
    var leafFill = mode === "ok" ? "#57c252" : mode === "bad" ? "#a67638" : "#69b347";
    var veinCol = mode === "ok" ? "#b9f6ca" : mode === "bad" ? "#d7b88a" : "#dcedc8";
    var spots = "";
    if(mode === "mid"){
      spots = '<circle cx="215" cy="145" r="14" fill="#c77d24" opacity=".85"/><circle cx="265" cy="185" r="10" fill="#a85a1c" opacity=".85"/><circle cx="185" cy="190" r="8" fill="#d99b26" opacity=".8"/>';
    } else if(mode === "bad"){
      spots = '<circle cx="205" cy="145" r="22" fill="#5c2b16" opacity=".92"/><circle cx="265" cy="175" r="19" fill="#471f0e" opacity=".92"/><circle cx="230" cy="215" r="16" fill="#6e3519" opacity=".88"/><circle cx="175" cy="185" r="14" fill="#4f2411" opacity=".9"/>';
    }
    var svg = '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 440 300">' +
      '<defs><linearGradient id="g" x1="0" y1="0" x2="1" y2="1"><stop offset="0%" stop-color="' + bg1 + '"/><stop offset="100%" stop-color="' + bg2 + '"/></linearGradient></defs>' +
      '<rect width="440" height="300" fill="url(#g)"/>' +
      '<circle cx="360" cy="65" r="42" fill="#e8a823" opacity=".18"/>' +
      '<path d="M95 235 C120 115 245 65 355 82 C335 190 235 255 95 235 Z" fill="' + leafFill + '" stroke="#1b331c" stroke-width="4"/>' +
      '<path d="M82 246 C175 185 265 135 345 88" fill="none" stroke="' + veinCol + '" stroke-width="5" stroke-linecap="round"/>' +
      '<path d="M180 182 L168 132 M225 154 L218 106 M268 128 L262 92 M195 172 L248 188 M242 144 L292 156" fill="none" stroke="' + veinCol + '" stroke-width="3" stroke-linecap="round" opacity=".85"/>' +
      spots +
      '</svg>';
    return "data:image/svg+xml;charset=utf-8," + encodeURIComponent(svg);
  }

  var LEAF_IMGS = {
    def: makeLeafSvgDataUrl("mid"),
    ok: makeLeafSvgDataUrl("ok"),
    mid: makeLeafSvgDataUrl("mid"),
    bad: makeLeafSvgDataUrl("bad")
  };

  var heroImg = document.getElementById("heroLeafImg");
  var prevImg = document.getElementById("prev");
  heroImg.src = LEAF_IMGS.def;
  prevImg.src = LEAF_IMGS.def;

  function setPreviewImage(src){
    if(!src) return;
    heroImg.src = src;
    prevImg.src = src;
    prevImg.hidden = false;
  }

  /* Blink/Pulse Animation on Click + Smooth Section Reveal */
  document.addEventListener("click", function(e){
    var target = e.target.closest("button, a.btn, .steps li, .gb");
    if(!target) return;
    target.classList.remove("blink-active");
    void target.offsetWidth;
    target.classList.add("blink-active");
    setTimeout(function(){ target.classList.remove("blink-active"); }, 600);

    var href = target.getAttribute("href");
    if(href && href.startsWith("#") && href.length > 1){
      var dest = document.querySelector(href);
      if(dest){
        dest.classList.remove("section-reveal-flash");
        void dest.offsetWidth;
        dest.classList.add("section-reveal-flash");
        setTimeout(function(){ dest.classList.remove("section-reveal-flash"); }, 800);
      }
    }
  });

  /* Language & Voice Advice */
  var D = {
    en: {
      n: "English",
      c: "en-IN",
      t: "Your tomato plants have early blight. Mix neem oil into a bucket of water and spray in the morning. Remove sick leaves and keep them away from healthy plants."
    },
    ta: {
      n: "தமிழ்",
      c: "ta-IN",
      t: "உங்கள் தக்காளிச் செடிகளுக்கு ஆரம்பகால கருகல் நோய் உள்ளது. ஒரு வாளி தண்ணீரில் வேப்ப எண்ணெய் கலந்து காலையில் தெளிக்கவும். நோயுற்ற இலைகளை அகற்றி ஆரோக்கியமான செடிகளிடமிருந்து தள்ளி வையுங்கள்."
    }
  };
  var say = document.getElementById("say"), cur = "en";
  function speakText(text, langCode){
    try{
      if(!("speechSynthesis" in window)) return;
      speechSynthesis.cancel();
      var u = new SpeechSynthesisUtterance(text);
      u.lang = langCode || D[cur].c;
      u.rate = 0.92;
      speechSynthesis.speak(u);
    }catch(e){}
  }

  function showLang(k){
    cur = k;
    tr(k);
    document.title = k === "ta" ? "AgriPulse AI – உங்கள் மொழியில் பேசும் பயிர் மருத்துவர்" : "AgriPulse AI – A crop doctor that speaks your language";
    document.getElementById("lang").value = k;
    document.documentElement.lang = k;
    document.documentElement.dir = "ltr";
    say.textContent = D[k].t;
    say.lang = D[k].c;
    savePrefsToCloud();
  }

  document.getElementById("play").onclick = function(){ speakText(say.textContent || D[cur].t, D[cur].c); };
  document.getElementById("dockListen").onclick = function(){ speakText(say.textContent || D[cur].t, D[cur].c); };

  var LK = ["ta"], orig = new WeakMap(), T = {};
  var TTA = {
    "🌱 AgriPulse AI": "🌱 AgriPulse AI",
    "▶ Present": "▶ விளக்கக்காட்சி",
    "📷 Scan Leaf": "📷 இலை ஸ்கேன்",
    "Offline-First Crop Vision & Voice Agronomist": "இணையம் இல்லாமலும் இயங்கும் பயிர் பார்வை & குரல் ஆலோசகர்",
    "A crop doctor that talks like your neighbor.": "உங்கள் அண்டை வீட்டாரைப் போல பேசும் பயிர் மருத்துவர்.",
    "Point your camera at a plant leaf to see live AI scanning, check root or fruit health, and hear remedies in your language.": "இலையில் கேமராவை வைத்து நேரடி AI ஸ்கேனிங், வேர் அல்லது காய் நலம் மற்றும் உங்கள் மொழியில் தீர்வுகளைக் கேளுங்கள்.",
    "📷 Scan a Leaf Now": "📷 இப்போதே இலையை ஸ்கேன் செய்",
    "📞 Keypad Helpline": "📞 கீபேட் உதவி எண்",
    "✨ Live Leaf Scanner": "✨ நேரடி இலை ஸ்கேனர்",
    "🔍 Ready": "🔍 தயார்",
    "⚡ Scanning Leaf...": "⚡ இலை ஸ்கேன் ஆகிறது...",
    "🔊 Listen": "🔊 கேளுங்கள்",
    "What changes for a farmer": "விவசாயிக்கு என்ன மாறுகிறது",
    "From leaf to advice in four steps": "இலையிலிருந்து ஆலோசனை வரை நான்கு படிகள்",
    "Take a photo": "புகைப்படம் எடுங்கள்",
    "Hear the plan": "திட்டத்தைக் கேளுங்கள்",
    "Where does your crop grow?": "உங்கள் பயிர் எங்கே விளைகிறது?",
    "🥔 Under the soil": "🥔 மண்ணுக்கு அடியில்",
    "Potato, carrot, groundnut, onion": "உருளைக்கிழங்கு, கேரட், நிலக்கடலை, வெங்காயம்",
    "🍅 Above the soil": "🍅 மண்ணுக்கு மேல்",
    "Tomato, mango, chilli, banana": "தக்காளி, மாம்பழம், மிளகாய், வாழை",
    "📷 Take photo": "📷 புகைப்படம் எடு",
    "🖼️ Choose photo": "🖼️ புகைப்படம் தேர்வு",
    "Submit": "சமர்ப்பி",
    "Phone has signal": "போனில் சிக்னல் உள்ளது",
    "▶ Run the four steps": "▶ நான்கு படிகளையும் இயக்கு",
    "See an example": "ஒரு எடுத்துக்காட்டைப் பாருங்கள்",
    "✅ Healthy": "✅ ஆரோக்கியம்",
    "⚠️ Needs care": "⚠️ கவனிப்பு தேவை",
    "❗ Serious": "❗ தீவிரம்",
    "🏜️ Dry soil": "🏜️ வறண்ட மண்",
    "🌱 Good soil": "🌱 நல்ல மண்",
    "🌊 Too wet": "🌊 அதிக ஈரம்",
    "Capture": "படம் பிடி",
    "Cancel": "ரத்து",
    "Built for sunlight, gloves and low-end phones": "வெயில், கையுறை, குறைந்த விலை போன்களுக்காக உருவாக்கப்பட்டது",
    "Leaf and pest scanner": "இலை மற்றும் பூச்சி ஸ்கேனர்",
    "Names the disease with a % score": "நோயின் பெயரை % மதிப்பெண்ணுடன் சொல்லும்",
    "Voice, not typing": "குரல், தட்டச்சு அல்ல",
    "Talk and listen, no reading": "பேசுங்கள், கேளுங்கள்; படிக்க வேண்டாம்",
    "Affordable remedies first": "மலிவான தீர்வுகள் முதலில்",
    "Neem, compost, water, before chemicals": "ரசாயனத்துக்கு முன் வேம்பு, உரம், நீர்",
    "Works offline, improves online": "இணையம் இல்லாமலும் வேலை செய்யும், இணையத்தில் மேம்படும்",
    "Photos wait and send later": "புகைப்படங்கள் காத்திருந்து பின்னர் அனுப்பப்படும்",
    "No smartphone? Use any phone.": "ஸ்மார்ட்போன் இல்லையா? எந்த போனிலும் பயன்படுத்தலாம்.",
    "🌱 AgriPulse AI · Offline-first crop protection for smallholder farmers. Agriculture and Rural Development project.": "🌱 AgriPulse AI · சிறு விவசாயிகளுக்கான இணையம் இல்லாமலும் வேலை செய்யும் பயிர் பாதுகாப்பு. வேளாண்மை மற்றும் ஊரக மேம்பாட்டுத் திட்டம்.",
    "📴 Works offline": "📴 இணையம் இல்லாமலும் வேலை செய்யும்",
    "🗣️ Speaks your language": "🗣️ உங்கள் மொழியில் பேசும்",
    "📱 Any phone": "📱 எந்த போனிலும்",
    "Show me a leaf!": "ஒரு இலையைக் காட்டுங்கள்!",
    "Before": "முன்பு",
    "With AgriPulse": "AgriPulse உடன்",
    "🍅 Spots appear": "🍅 புள்ளிகள் தோன்றும்",
    "🚌 Advisor is a day away": "🚌 ஆலோசகர் ஒரு நாள் தூரத்தில்",
    "🤷 Guess or wait": "🤷 ஊகிக்கவோ காத்திருக்கவோ வேண்டும்",
    "📷 Take a photo": "📷 புகைப்படம் எடுங்கள்",
    "🔊 Hear the fix": "🔊 தீர்வைக் கேளுங்கள்",
    "✅ Treat today": "✅ இன்றே சிகிச்சை",
    "⚡ Instant guess": "⚡ உடனடி கணிப்பு",
    "🔎 Online check": "🔎 இணையச் சோதனை",
    "🔊 Hear the plan": "🔊 திட்டத்தைக் கேளுங்கள்",
    "Call": "அழைப்பு",
    "Press the disease number": "நோய் எண்ணை அழுத்துங்கள்",
    "Text": "குறுஞ்செய்தி",
    "Send the problem": "பிரச்சனையை அனுப்புங்கள்",
    "Village helper": "கிராம உதவியாளர்",
    "Scans the leaf for you": "உங்களுக்காக இலையை ஸ்கேன் செய்வார்",
    "Keypad phones work from symptoms, not photos. The keypad here is a simulation.": "கீபேட் போன்கள் புகைப்படத்தால் அல்ல, அறிகுறிகளால் வேலை செய்கின்றன. இங்குள்ள கீபேட் ஒரு மாதிரி."
  };
  Object.keys(TTA).forEach(function(k){ T[k] = [TTA[k]]; });

  var sel = document.getElementById("lang");
  Object.keys(D).forEach(function(k){
    var o = document.createElement("option");
    o.value = k;
    o.textContent = D[k].n;
    sel.appendChild(o);
  });
  sel.onchange = function(){ showLang(sel.value); };

  function tr(k){
    var idx = LK.indexOf(k);
    var w = document.createTreeWalker(document.body, NodeFilter.SHOW_TEXT, null);
    var n, ns = [];
    while((n = w.nextNode())) ns.push(n);
    ns.forEach(function(n){
      var p = n.parentNode.nodeName;
      if(p === "SCRIPT" || p === "STYLE" || p === "OPTION") return;
      if(!orig.has(n)){
        var t = n.nodeValue.trim();
        if(!T[t]) return;
        orig.set(n, n.nodeValue);
      }
      var o = orig.get(n);
      var m = o.match(/^(\s*)([\s\S]*?)(\s*)$/);
      n.nodeValue = idx < 0 ? o : m[1] + T[m[2]][idx] + m[3];
    });
  }
  showLang("en");

  /* =========================================================================
     DATABASE CONNECTION LAYER: Cloud Firestore + Offline IndexedDB Queue
     ========================================================================= */
  var appId = typeof __app_id !== "undefined" && __app_id ? __app_id : "agripulse-ai-prod";
  var fbApp = null, auth = null, db = null, currentUser = null;
  var cloudRecords = [];
  var offlineQueue = [];
  var grow = "above", soil = "", curSt = "";

  function openIndexedQueue(){
    return new Promise(function(resolve, reject){
      if(!("indexedDB" in window)){ resolve(null); return; }
      var req = indexedDB.open("AgriPulseOfflineDB", 1);
      req.onupgradeneeded = function(ev){
        var idb = ev.target.result;
        if(!idb.objectStoreNames.contains("sync_queue")){
          idb.createObjectStore("sync_queue", { keyPath: "queueId" });
        }
      };
      req.onsuccess = function(ev){ resolve(ev.target.result); };
      req.onerror = function(){ reject(req.error); };
    });
  }

  async function loadOfflineQueue(){
    try{
      var idb = await openIndexedQueue();
      if(!idb) return [];
      return await new Promise(function(resolve){
        var tx = idb.transaction("sync_queue", "readonly");
        var st = tx.objectStore("sync_queue");
        var r = st.getAll();
        r.onsuccess = function(){ resolve(r.result || []); };
        r.onerror = function(){ resolve([]); };
      });
    }catch(e){ return []; }
  }

  async function putOfflineQueueItem(item){
    try{
      var idb = await openIndexedQueue();
      if(!idb) return;
      await new Promise(function(resolve){
        var tx = idb.transaction("sync_queue", "readwrite");
        tx.objectStore("sync_queue").put(item);
        tx.oncomplete = function(){ resolve(); };
      });
    }catch(e){}
  }

  async function removeOfflineQueueItem(queueId){
    try{
      var idb = await openIndexedQueue();
      if(!idb) return;
      await new Promise(function(resolve){
        var tx = idb.transaction("sync_queue", "readwrite");
        tx.objectStore("sync_queue").delete(queueId);
        tx.oncomplete = function(){ resolve(); };
      });
    }catch(e){}
  }

  var dbStatusBadge = document.getElementById("dbStatusBadge");
  var dbSummaryText = document.getElementById("dbSummaryText");
  var dbHistoryList = document.getElementById("dbHistoryList");

  function updateDbStatusUI(){
    var isOnline = !!(net && net.checked && navigator.onLine !== false);
    var hasCloud = !!(db && currentUser);
    var qCount = offlineQueue.length;

    if(qCount > 0){
      dbStatusBadge.className = "db-status-pill queued";
      dbStatusBadge.textContent = "⏳ Offline Queue: " + qCount + " waiting";
    } else if(hasCloud && isOnline){
      dbStatusBadge.className = "db-status-pill synced";
      dbStatusBadge.textContent = "☁️ Cloud DB Connected";
    } else {
      dbStatusBadge.className = "db-status-pill queued";
      dbStatusBadge.textContent = "💾 Local Offline DB Ready";
    }

    dbSummaryText.textContent = hasCloud
      ? ("User: " + currentUser.uid + " · Cloud: " + cloudRecords.length + " · Queued: " + qCount)
      : ("Offline IndexedDB active · Queued items: " + qCount);

    var combined = [];
    offlineQueue.forEach(function(q){ combined.push(Object.assign({ _sync: "queued" }, q)); });
    cloudRecords.slice(0, 8).forEach(function(c){ combined.push(Object.assign({ _sync: "cloud" }, c)); });

    dbHistoryList.innerHTML = "";
    if(combined.length === 0){
      var emptyLi = document.createElement("li");
      emptyLi.className = "db-item";
      emptyLi.innerHTML = "<div><b>No scan records yet</b><small>Run a scan or press a key on the keypad to save a record.</small></div>";
      dbHistoryList.appendChild(emptyLi);
      return;
    }

    combined.slice(0, 8).forEach(function(rec){
      var li = document.createElement("li");
      li.className = "db-item";
      var isQ = rec._sync === "queued";
      li.innerHTML =
        "<div><b>" + escapeHtml(rec.title || rec.disease || "Leaf Scan") + "</b>" +
        "<small>" + escapeHtml((rec.cropZone || "above") + " · " + (rec.severity || "Advisory") + " · " + (rec.createdAt || "")) + "</small></div>" +
        "<span class=\"db-tag " + (isQ ? "offline-q" : "cloud") + "\">" + (isQ ? "⏳ Queued" : "☁️ Saved") + "</span>";
      dbHistoryList.appendChild(li);
    });
  }

  function escapeHtml(str){
    return String(str || "").replace(/[&<>"']/g, function(m){
      return ({ "&": "&amp;", "<": "&lt;", ">": "&gt;", "\"": "&quot;", "'": "&#39;" })[m];
    });
  }

  async function persistRecord(record){
    var stamped = Object.assign({}, record, {
      createdAt: record.createdAt || (new Date().toLocaleTimeString([], { hour: "2-digit", minute: "2-digit" }) + " · " + new Date().toISOString().slice(0, 10)),
      userId: currentUser ? currentUser.uid : "local-farmer"
    });

    var canWriteCloud = !!(db && currentUser && net && net.checked && navigator.onLine !== false);
    if(canWriteCloud){
      try{
        await addDoc(collection(db, "artifacts", appId, "public", "data", "crop_scans"), stamped);
        updateDbStatusUI();
        return true;
      }catch(e){}
    }

    var qId = "q_" + Date.now() + "_" + Math.random().toString(36).slice(2, 7);
    var queuedItem = Object.assign({}, stamped, { queueId: qId });
    offlineQueue.unshift(queuedItem);
    await putOfflineQueueItem(queuedItem);
    updateDbStatusUI();
    return false;
  }

  async function flushOfflineQueue(){
    if(!offlineQueue.length) { updateDbStatusUI(); return; }
    var canWriteCloud = !!(db && currentUser && net && net.checked && navigator.onLine !== false);
    if(!canWriteCloud){ updateDbStatusUI(); return; }

    var remaining = [];
    for(var i = 0; i < offlineQueue.length; i++){
      var item = offlineQueue[i];
      try{
        var copy = Object.assign({}, item);
        delete copy.queueId;
        await addDoc(collection(db, "artifacts", appId, "public", "data", "crop_scans"), copy);
        await removeOfflineQueueItem(item.queueId);
      }catch(e){
        remaining.push(item);
      }
    }
    offlineQueue = remaining;
    updateDbStatusUI();
  }

  async function savePrefsToCloud(){
    if(!db || !currentUser) return;
    try{
      var prefDoc = doc(db, "artifacts", appId, "users", currentUser.uid, "farmer_prefs", "profile");
      await setDoc(prefDoc, { lang: cur, growZone: grow, soilState: soil || "none", updatedAt: new Date().toISOString() }, { merge: true });
    }catch(e){}
  }

  async function loadPrefsFromCloud(){
    if(!db || !currentUser) return;
    try{
      var prefDoc = doc(db, "artifacts", appId, "users", currentUser.uid, "farmer_prefs", "profile");
      var snap = await getDoc(prefDoc);
      if(snap.exists()){
        var data = snap.data();
        if(data.lang && D[data.lang]) showLang(data.lang);
        if(data.growZone) setGrowZone(data.growZone, false);
        if(data.soilState && data.soilState !== "none") soil = data.soilState;
      }
    }catch(e){}
  }

  function subscribeCloudRecords(){
    if(!db || !currentUser) return;
    var colRef = collection(db, "artifacts", appId, "public", "data", "crop_scans");
    onSnapshot(colRef, function(snap){
      var rows = [];
      snap.forEach(function(d){ rows.push(Object.assign({ id: d.id }, d.data())); });
      rows.sort(function(a, b){ return String(b.createdAt || "").localeCompare(String(a.createdAt || "")); });
      cloudRecords = rows;
      updateDbStatusUI();
    }, function(){ updateDbStatusUI(); });
  }

  async function initDatabaseLayer(){
    offlineQueue = await loadOfflineQueue();
    updateDbStatusUI();

    if(typeof __firebase_config !== "undefined" && __firebase_config){
      try{
        var firebaseConfig = typeof __firebase_config === "string" ? JSON.parse(__firebase_config) : __firebase_config;
        fbApp = initializeApp(firebaseConfig);
        auth = getAuth(fbApp);
        db = getFirestore(fbApp);

        if(typeof __initial_auth_token !== "undefined" && __initial_auth_token){
          await signInWithCustomToken(auth, __initial_auth_token);
        } else {
          await signInAnonymously(auth);
        }

        onAuthStateChanged(auth, async function(u){
          currentUser = u;
          if(u){
            await loadPrefsFromCloud();
            subscribeCloudRecords();
            await flushOfflineQueue();
          }
          updateDbStatusUI();
        });
      }catch(e){
        updateDbStatusUI();
      }
    }
  }

  /* =========================================================================
     LEAF SCANNER, CAMERA, FOUR STEPS & AGRIBOT STATE LOGIC
     ========================================================================= */
  var items = document.querySelectorAll("#steps li");
  var log = document.getElementById("log");
  var pic = document.getElementById("pic");
  var run = document.getElementById("run");
  var net = document.getElementById("net");
  var aiDiagnosisBox = document.getElementById("aiDiagnosisBox");
  var stageScanState = document.getElementById("stageScanState");
  var stageMetricsTag = document.getElementById("stageMetricsTag");
  var heroStatusChip = document.getElementById("heroStatusChip");
  var heroScanTag = document.getElementById("heroScanTag");
  var heroCropTag = document.getElementById("heroCropTag");

  var busy = false, photo = false, info = null, uploadedDataUrl = "";
  var wait = function(ms){ return new Promise(function(r){ setTimeout(r, ms); }); };

  function X(en, ta){ return cur === "ta" ? ta : en; }

  function mark(i){
    items.forEach(function(li, j){
      li.classList.toggle("on", j === i);
      li.classList.toggle("done", j < i);
    });
  }

  function add(t){
    var li = document.createElement("li");
    li.textContent = t;
    log.appendChild(li);
  }

  function setGrowZone(zone, syncPref){
    grow = zone;
    document.querySelectorAll(".gb").forEach(function(b){
      b.setAttribute("aria-pressed", b.dataset.g === zone ? "true" : "false");
    });
    heroCropTag.textContent = zone === "under" ? X("🥔 Under Soil", "🥔 மண்ணுக்கு கீழ்") : X("🍅 Above Soil", "🍅 மண்ணுக்கு மேல்");
    if(syncPref !== false) savePrefsToCloud();
  }

  document.querySelectorAll(".gb").forEach(function(b){
    b.onclick = function(){ setGrowZone(b.dataset.g, true); };
  });

  function analyzeImageElement(img){
    var c = document.createElement("canvas");
    var W = 120;
    c.width = W;
    c.height = Math.max(60, Math.round(W * (img.naturalHeight || 3) / (img.naturalWidth || 4)));
    var ctx = c.getContext("2d");
    ctx.drawImage(img, 0, 0, c.width, c.height);
    var d = ctx.getImageData(0, 0, c.width, c.height).data;
    var plantPx = 0, spotPx = 0;
    for(var i = 0; i < d.length; i += 4){
      var r = d[i] / 255, g = d[i + 1] / 255, b = d[i + 2] / 255;
      var mx = Math.max(r, g, b), mn = Math.min(r, g, b);
      var sat = mx ? (mx - mn) / mx : 0;
      var hue = 0;
      if(mx !== mn){
        var dl = mx - mn;
        hue = mx === r ? ((g - b) / dl) % 6 : mx === g ? (b - r) / dl + 2 : (r - g) / dl + 4;
        hue *= 60;
        if(hue < 0) hue += 360;
      }
      if(sat > 0.22 && mx > 0.14){
        if(hue >= 65 && hue <= 175) plantPx++;
        else if(hue >= 12 && hue < 65) spotPx++;
      }
    }
    var totalPlant = plantPx + spotPx;
    return {
      w: img.naturalWidth || 440,
      h: img.naturalHeight || 300,
      plantPct: Math.round(100 * totalPlant / (d.length / 4)),
      spotPct: totalPlant ? Math.round(100 * spotPx / totalPlant) : 0
    };
  }

  function handleSelectedFile(file){
    if(!file || !file.type.startsWith("image/")) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      var dataUrl = ev.target.result;
      uploadedDataUrl = dataUrl;
      setPreviewImage(dataUrl);
      photo = true;
      setState("");
      stageScanState.textContent = X("🟢 IMAGE LOADED · READY TO SUBMIT", "🟢 படம் ஏற்றப்பட்டது · சமர்ப்பிக்கத் தயார்");
      heroScanTag.textContent = X("📷 Photo Ready", "📷 படம் தயார்");
      heroStatusChip.textContent = X("📸 Loaded", "📸 ஏற்றப்பட்டது");

      var im = new Image();
      im.onload = function(){
        try{ info = analyzeImageElement(im); }
        catch(e){ info = { w: im.naturalWidth || 400, h: im.naturalHeight || 300, plantPct: 65, spotPct: 8 }; }
        stageMetricsTag.textContent = X("🌿 Leaf ~" + info.plantPct + "% · Spots ~" + info.spotPct + "%", "🌿 இலை ~" + info.plantPct + "% · புள்ளி ~" + info.spotPct + "%");
        log.innerHTML = "";
        add(X("Photo loaded (" + info.w + "×" + info.h + "). Press Submit to analyze and save.", "புகைப்படம் ஏற்றப்பட்டது (" + info.w + "×" + info.h + "). பகுப்பாய்வு செய்ய சமர்ப்பி அழுத்தவும்."));
        aiDiagnosisBox.textContent = X("Photo ready (" + info.plantPct + "% leaf area). Click Submit to run diagnosis.", "படம் தயார் (~" + info.plantPct + "% இலை). சமர்ப்பி என்பதைக் கிளிக் செய்யவும்.");
      };
      im.src = dataUrl;
    };
    reader.readAsDataURL(file);
  }

  pic.onchange = function(){
    if(pic.files && pic.files[0]) handleSelectedFile(pic.files[0]);
  };

  var stream = null, camwrap = document.getElementById("camwrap"), vid = document.getElementById("vid");
  function stopCam(){
    if(stream){
      stream.getTracks().forEach(function(t){ t.stop(); });
      stream = null;
    }
    camwrap.hidden = true;
  }

  document.getElementById("cam").onclick = async function(){
    try{
      if(!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) throw new Error("no-cam");
      stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
      vid.srcObject = stream;
      camwrap.hidden = false;
      log.innerHTML = "";
      add(X("Camera opened. Point at a leaf and click Capture.", "கேமரா திறக்கப்பட்டது. இலையில் வைத்து படம் பிடி அழுத்தவும்."));
    }catch(e){
      log.innerHTML = "";
      add(X("Camera not available here. Use Choose photo or Run the four steps.", "இங்கே கேமரா கிடைக்கவில்லை. புகைப்படம் தேர்வு அல்லது நான்கு படிகளையும் இயக்கு பயன்படுத்தவும்."));
    }
  };

  document.getElementById("snap").onclick = function(){
    if(!vid.videoWidth) return;
    var c = document.createElement("canvas");
    c.width = Math.min(vid.videoWidth, 1024);
    c.height = Math.round(c.width * vid.videoHeight / vid.videoWidth);
    c.getContext("2d").drawImage(vid, 0, 0, c.width, c.height);
    var dataUrl = c.toDataURL("image/jpeg", 0.85);
    uploadedDataUrl = dataUrl;
    setPreviewImage(dataUrl);
    photo = true;
    setState("");
    stopCam();
    var im = new Image();
    im.onload = function(){
      try{ info = analyzeImageElement(im); }
      catch(e){ info = { w: im.naturalWidth, h: im.naturalHeight, plantPct: 60, spotPct: 9 }; }
      stageMetricsTag.textContent = X("🌿 Leaf ~" + info.plantPct + "% · Spots ~" + info.spotPct + "%", "🌿 இலை ~" + info.plantPct + "% · புள்ளி ~" + info.spotPct + "%");
      log.innerHTML = "";
      add(X("Photo captured. Press Submit to run diagnosis.", "படம் பிடிக்கப்பட்டது. சமர்ப்பி அழுத்தவும்."));
    };
    im.src = dataUrl;
  };

  document.getElementById("camx").onclick = stopCam;
  run.onclick = function(){ go(false); };
  document.getElementById("submit").onclick = function(){
    if(!photo){
      log.innerHTML = "";
      add(X("Choose or take a photo first, or click Run the four steps.", "முதலில் புகைப்படம் எடுங்கள்/தேர்வு செய்யுங்கள் அல்லது நான்கு படிகளையும் இயக்கு அழுத்தவும்."));
      return;
    }
    go(true);
  };

  async function go(useRealPhoto){
    if(busy) return;
    busy = true;
    run.disabled = true;
    log.innerHTML = "";

    var hasPhoto = !!(useRealPhoto && photo && info);
    var noLeaf = hasPhoto && info.plantPct < 8;
    var st = noLeaf ? "" : !hasPhoto ? "mid" : info.spotPct < 4 ? "ok" : info.spotPct < 15 ? "mid" : "bad";
    var sv = hasPhoto ? (info.spotPct < 4 ? "Low" : info.spotPct < 15 ? "Moderate" : "High") : "Moderate";
    var under = grow === "under";
    var cropName = cur === "ta" ? (under ? "உருளைக்கிழங்கு" : "தக்காளி") : (under ? "Potato" : "Tomato");
    var diseaseTitle = noLeaf
      ? X("No clear leaf found", "தெளிவான இலை தெரியவில்லை")
      : hasPhoto
        ? (info.spotPct < 4 ? X("Healthy Leaf", "ஆரோக்கியமான இலை") : X(cropName + " Leaf Patches", cropName + " இலைப் புள்ளிகள்"))
        : X(cropName + " Early Blight", cropName + " ஆரம்பகால கருகல்");

    stageScanState.textContent = X("⚡ SCANNING IN PROGRESS...", "⚡ ஸ்கேன் நடக்கிறது...");
    heroScanTag.textContent = X("⚡ Scanning...", "⚡ ஸ்கேன்...");
    heroStatusChip.textContent = X("⚡ Scanning", "⚡ ஸ்கேன்");

    mark(0);
    add(hasPhoto
      ? X("Step 1: Photo " + info.w + "×" + info.h + " · Leaf ~" + info.plantPct + "% · Patches ~" + info.spotPct + "%.", "படி 1: படம் " + info.w + "×" + info.h + " · இலை ~" + info.plantPct + "% · புள்ளி ~" + info.spotPct + "%.")
      : X("Step 1: Sample leaf active for " + cropName + ".", "படி 1: " + cropName + " மாதிரி இலை ஸ்கேன் ஆகிறது."));
    await wait(650);

    mark(1);
    add(under
      ? X("Step 2 (Offline): Under-soil crop — check roots/tubers and prevent waterlogging.", "படி 2 (ஆஃப்லைன்): மண்ணுக்கு அடியில் விளையும் பயிர் — வேர்/கிழங்கைச் சோதித்து நீர் தேங்காமல் பாருங்கள்.")
      : X("Step 2 (Offline): Above-soil crop — check fruits, pods, and stems.", "படி 2 (ஆஃப்லைன்): மண்ணுக்கு மேல் விளையும் பயிர் — காய், பழம், தண்டு ஆகியவற்றைச் சோதியுங்கள்."));
    await wait(650);

    mark(2);
    if(st) setState(st);
    var isOnline = !!(net.checked && navigator.onLine !== false);
    add(isOnline
      ? X("Step 3 (Online): " + diseaseTitle + " · Severity: " + sv + ".", "படி 3 (ஆன்லைன்): " + diseaseTitle + " · தீவிரம்: " + sv + ".")
      : X("Step 3 (Offline): Saved to local queue; will sync automatically when signal returns.", "படி 3 (ஆஃப்லைன்): உள்ளூர் வரிசையில் சேமிக்கப்பட்டது; சிக்னல் வந்ததும் தானாக அனுப்பப்படும்."));
    await wait(650);

    mark(3);
    var adviceText = "";
    if(noLeaf){
      adviceText = X("Move closer to one leaf and capture a clear photo.", "ஒரு இலைக்கு அருகில் சென்று தெளிவாகப் படம் எடுங்கள்.");
    } else if(hasPhoto && info.spotPct < 4){
      adviceText = X("Leaf looks healthy. Keep regular watering and check nearby plants in a few days.", "இலை ஆரோக்கியமாக உள்ளது. வழக்கமான நீர் பாய்ச்சலைத் தொடருங்கள்.");
    } else {
      adviceText = D[cur].t + (under
        ? X(" Also dig gently to check roots or tubers and avoid standing water.", " வேர் அல்லது கிழங்குகளையும் மெதுவாகத் தோண்டிப் பாருங்கள்; நீர் தேங்க விடாதீர்கள்.")
        : X(" Also inspect fruits and stems for spots.", " காய்கள் மற்றும் தண்டுகளிலும் புள்ளிகள் உள்ளதா எனப் பாருங்கள்."));
    }

    say.textContent = adviceText;
    say.lang = D[cur].c;
    document.getElementById("diagTitle").textContent = diseaseTitle;
    var pct = hasPhoto ? Math.max(6, Math.min(98, info.spotPct || 12)) : 91;
    document.getElementById("diagMeta").textContent = X("Severity: " + sv + " · Confidence " + (hasPhoto ? (100 - Math.min(25, pct)) : 91) + "%", "தீவிரம்: " + sv + " · நம்பிக்கை " + (hasPhoto ? (100 - Math.min(25, pct)) : 91) + "%");
    document.getElementById("diagBar").style.width = (hasPhoto ? pct : 91) + "%";
    aiDiagnosisBox.textContent = diseaseTitle + " (" + sv + ") — " + adviceText;

    stageScanState.textContent = X("✅ SCAN COMPLETE", "✅ ஸ்கேன் முடிந்தது");
    heroScanTag.textContent = X("✅ Done", "✅ முடிந்தது");
    heroStatusChip.textContent = st === "ok" ? "✅ Healthy" : st === "bad" ? "❗ Serious" : "⚠️ Needs care";

    add(X("Step 4: Speaking plan in " + D[cur].n + " and saving to database.", "படி 4: " + D[cur].n + " மொழியில் ஆலோசனை ஒலிக்கிறது; தரவுத்தளத்தில் சேமிக்கப்படுகிறது."));
    speakText(adviceText, D[cur].c);

    var savedToCloud = await persistRecord({
      type: "leaf_scan",
      title: diseaseTitle,
      severity: sv,
      cropZone: grow,
      soilState: soil || "default",
      lang: cur,
      advice: adviceText
    });
    add(savedToCloud
      ? X("Saved to Cloud Database (crop_scans).", "கிளவுட் தரவுத்தளத்தில் சேமிக்கப்பட்டது (crop_scans).")
      : X("Saved to Offline Queue (will sync when online).", "ஆஃப்லைன் வரிசையில் சேமிக்கப்பட்டது (இணையம் வந்ததும் ஒத்திசைக்கப்படும்)."));

    items.forEach(function(li){ li.classList.remove("on"); li.classList.add("done"); });
    busy = false;
    run.disabled = false;
  }

  items.forEach(function(li, i){ li.onclick = function(){ if(!busy) mark(i); }; });

  /* Example State & Soil Buttons */
  var ET = {
    ok: "Example: leaf looks healthy and green. State: Healthy ✅. No spray needed today.",
    mid: "Example: brown patches around 8% of leaf. State: Needs care ⚠️. Spray neem water in the morning.",
    bad: "Example: heavy spots over 15% of leaf. State: Serious ❗. Remove affected leaves today."
  };
  var ETA = {
    ok: "எடுத்துக்காட்டு: இலை ஆரோக்கியமாகப் பச்சையாக உள்ளது. நிலை: ஆரோக்கியம் ✅.",
    mid: "எடுத்துக்காட்டு: சுமார் 8% பழுப்பு திட்டுகள். நிலை: கவனிப்பு தேவை ⚠️. காலையில் வேப்ப நீர் தெளியுங்கள்.",
    bad: "எடுத்துக்காட்டு: 15%-க்கு மேல் பாதிப்பு. நிலை: தீவிரம் ❗. பாதித்த இலைகளை இன்றே அகற்றுங்கள்."
  };
  var ST = {
    dry: "Soil: Dry. Water early in the morning and add mulch.",
    good: "Soil: Good moisture. Keep steady watering.",
    wet: "Soil: Too wet. Stop watering and drain excess water to protect roots."
  };
  var STA = {
    dry: "மண்: வறண்டது. அதிகாலையில் நீர் பாய்ச்சி மூடாக்கு இடுங்கள்.",
    good: "மண்: நல்ல ஈரம். சீரான நீர் பாய்ச்சலைத் தொடருங்கள்.",
    wet: "மண்: அதிக ஈரம். நீர் பாய்ச்சுவதை நிறுத்தி வேர்களைப் பாதுகாக்க நீர் வடிய விடுங்கள்."
  };

  function setState(st){
    curSt = st;
    var bt = document.getElementById("bot");
    bt.className = "bot" + (st ? " " + st : "");
    document.getElementById("bm").textContent =
      st === "ok" ? X("I’m happy! The plant is healthy.", "மகிழ்ச்சி! செடி ஆரோக்கியமாக உள்ளது.") :
      st === "mid" ? X("Hmm, it needs some care.", "ம்ம்… கொஞ்சம் கவனிப்பு தேவை.") :
      st === "bad" ? X("Oh no! It needs help now.", "ஐயோ! உடனே உதவி தேவை.") :
      X("Show me a leaf!", "ஒரு இலையைக் காட்டுங்கள்!");

    var bg = document.getElementById("bgp");
    bg.className = "bgp" + (st ? " " + st : "") + (soil ? " " + soil : "");
    if(st && LEAF_IMGS[st] && !uploadedDataUrl){
      setPreviewImage(LEAF_IMGS[st]);
    }
  }

  document.querySelectorAll(".exb").forEach(function(b){
    b.onclick = function(){
      log.innerHTML = "";
      if(b.dataset.s){
        setState(b.dataset.s);
        add(X(ET[b.dataset.s], ETA[b.dataset.s]));
        aiDiagnosisBox.textContent = X(ET[b.dataset.s], ETA[b.dataset.s]);
      } else if(b.dataset.so){
        soil = b.dataset.so;
        setState(curSt);
        add(X(ST[soil], STA[soil]));
        aiDiagnosisBox.textContent = X(ST[soil], STA[soil]);
        savePrefsToCloud();
      }
    };
  });

  net.addEventListener("change", async function(){
    if(net.checked){
      await flushOfflineQueue();
    }
    updateDbStatusUI();
  });
  window.addEventListener("online", async function(){
    await flushOfflineQueue();
    updateDbStatusUI();
  });
  window.addEventListener("offline", updateDbStatusUI);
  document.getElementById("syncNowBtn").onclick = async function(){
    await flushOfflineQueue();
    updateDbStatusUI();
  };

  /* Keypad Helpline Simulator */
  var DZ = [
    { n: "Tomato Early Blight", t: "தக்காளி ஆரம்பகால கருகல்", u: 0, e: "Remove spotted lower leaves and spray neem oil mixed in water in the morning.", a: "புள்ளி விழுந்த கீழ் இலைகளை அகற்றி, காலையில் வேப்ப எண்ணெய் கலந்த நீரைத் தெளியுங்கள்." },
    { n: "Tomato Late Blight", t: "தக்காளி தாமத கருகல்", u: 0, e: "Remove infected plants and avoid evening watering.", a: "பாதித்த செடிகளை அகற்றி, மாலையில் நீர் பாய்ச்ச வேண்டாம்." },
    { n: "Chilli Leaf Curl", t: "மிளகாய் இலை சுருட்டை", u: 0, e: "Remove curled leaves, use yellow sticky traps, and spray neem water.", a: "சுருண்ட இலைகளை அகற்றி, மஞ்சள் ஒட்டுப் பொறி வைத்து வேப்ப நீர் தெளியுங்கள்." },
    { n: "Banana Sigatoka Leaf Spot", t: "வாழை இலைப்புள்ளி", u: 0, e: "Cut spotted leaves, keep field weed-free, and drain excess water.", a: "புள்ளி இலைகளை வெட்டி, வயலில் களை இல்லாமலும் நீர் தேங்காமலும் வையுங்கள்." },
    { n: "Maize Fall Armyworm", t: "மக்காச்சோளம் படைப்புழு", u: 0, e: "Check whorl in the morning, hand-pick worms, and apply neem or ash.", a: "காலையில் குருத்தைப் பார்த்து புழுக்களை அகற்றி வேம்பு அல்லது சாம்பல் இடுங்கள்." },
    { n: "Wheat Rust", t: "கோதுமை துரு", u: 0, e: "Remove infected plants early and avoid excess nitrogen.", a: "பாதித்த செடிகளை ஆரம்பத்தில் அகற்றி அதிக தழைச்சத்தைத் தவிர்க்கவும்." },
    { n: "Potato Late Blight", t: "உருளைக்கிழங்கு தாமத கருகல்", u: 1, e: "Remove blackened leaves and hill up soil over tubers.", a: "கருகிய இலைகளை அகற்றி கிழங்குகளின் மேல் மண் அணையுங்கள்." },
    { n: "Potato Common Scab", t: "உருளைக்கிழங்கு சொறி", u: 1, e: "Keep soil evenly moist and rotate crops.", a: "மண்ணை சீரான ஈரத்துடன் வைத்து பயிர் சுழற்சி செய்யுங்கள்." },
    { n: "Groundnut Leaf Spot", t: "நிலக்கடலை இலைப்புள்ளி", u: 1, e: "Remove spotted leaves and rotate crops next season.", a: "புள்ளி இலைகளை அகற்றி அடுத்த பருவத்தில் பயிர் சுழற்சி செய்யுங்கள்." },
    { n: "Onion Purple Blotch", t: "வெங்காய ஊதா கருகல்", u: 1, e: "Remove infected leaves and avoid overhead watering.", a: "பாதித்த இலைகளை அகற்றி மேலிருந்து நீர் தெளிப்பதைத் தவிர்க்கவும்." },
    { n: "Cassava Mosaic", t: "மரவள்ளி மொசைக் நோய்", u: 1, e: "Uproot mosaic plants and use healthy stem cuttings.", a: "மொசைக் செடிகளைப் பிடுங்கி ஆரோக்கியமான குச்சிகளை நடவும்." }
  ];

  var KL = {
    1: { c: "en-IN", i: "Press disease number (1-11), then #.", u: "Not sure. Ask your local extension worker.", b: "Number not on list. Try again.", h: "* = start again", tl: { 0: "Also check fruits and stems.", 1: "Also dig gently to check roots or tubers." }, nm: function(z){ return z.n; }, ad: function(z){ return z.e; } },
    2: { c: "ta-IN", i: "நோய் எண்ணை அழுத்தி (1-11), பிறகு # அழுத்தவும்.", u: "உறுதியாகத் தெரியவில்லை. உள்ளூர் வேளாண் அலுவலரிடம் கேளுங்கள்.", b: "எண் பட்டியலில் இல்லை. மீண்டும் முயலுங்கள்.", h: "* = மீண்டும் தொடங்க", tl: { 0: "காய்கள் மற்றும் தண்டுகளையும் பாருங்கள்.", 1: "மெதுவாகத் தோண்டி வேர் அல்லது கிழங்குகளையும் பாருங்கள்." }, nm: function(z){ return z.t; }, ad: function(z){ return z.a; } }
  };

  var kd = document.getElementById("kd"), kk = document.getElementById("kk");
  var ks = 0, kl = null, kb = "", kbase = "";

  function say3(show, speak, code){
    kd.textContent = show;
    kd.scrollTop = kd.scrollHeight;
    speakText(speak, code);
  }

  function kreset(){
    ks = 0; kl = null; kb = "";
    kd.textContent = "Dial the helpline.\nPress 1 for English, 2 for தமிழ்.";
  }

  function kask(pre){
    var show = kl.i + "\n" + DZ.map(function(z, i){ return (i + 1) + "  " + kl.nm(z); }).join("\n");
    var sp = kl.i + " " + DZ.map(function(z, i){ return (i + 1) + ", " + kl.nm(z); }).join(". ");
    kbase = show;
    say3(show + "\n\nNumber: ", (pre ? pre + " " : "") + sp, kl.c);
  }

  function key(d){
    if(d === "*"){ kreset(); return; }
    if(ks === 0){
      if(KL[d]){ kl = KL[d]; ks = 1; kb = ""; kask(""); }
      return;
    }
    if(ks !== 1) return;
    if(d === "#"){
      if(!kb) return;
      var n = parseInt(kb, 10);
      kb = "";
      if(n === 0){ say3(kl.u + "\n\n" + kl.h, kl.u, kl.c); ks = 2; return; }
      var z = DZ[n - 1];
      if(!z){ kask(kl.b); return; }
      var tx = kl.ad(z) + " " + kl.tl[z.u];
      say3(n + ": " + kl.nm(z) + "\n" + tx + "\n\n" + kl.h, kl.nm(z) + ". " + tx, kl.c);
      persistRecord({
        type: "keypad_helpline",
        title: kl.nm(z),
        severity: "Helpline",
        cropZone: z.u ? "under" : "above",
        lang: kl.c.startsWith("ta") ? "ta" : "en",
        advice: tx
      });
      ks = 2;
      return;
    }
    if(kb.length < 2){
      kb += d;
      kd.textContent = kbase + "\n\nNumber: " + kb;
      kd.scrollTop = kd.scrollHeight;
    }
  }

  ["1","2","3","4","5","6","7","8","9","*","0","#"].forEach(function(d){
    var b = document.createElement("button");
    b.type = "button";
    b.textContent = d;
    b.setAttribute("aria-label", "Key " + d);
    b.onclick = function(){ key(d); };
    kk.appendChild(b);
  });
  kreset();

  /* Presentation Deck */
  var SLDS = {
    en: [
      ["AgriPulse AI", ["A crop doctor that talks like your neighbor", "Works offline with local queue & cloud sync", "Speaks English and Tamil", "Works on any phone"]],
      ["How It Works", ["1. Capture or choose a leaf photo", "2. Instant offline & crop-zone check", "3. Online analysis & advice", "4. Spoken guidance & database log"]],
      ["Database & Sync", ["Cloud Firestore stores scan records & preferences", "Offline IndexedDB queues scans when signal is off", "Automatic sync when connectivity returns"]]
    ],
    ta: [
      ["AgriPulse AI", ["உங்கள் அண்டை வீட்டாரைப் போல பேசும் பயிர் மருத்துவர்", "இணையம் இல்லாமலும் இயங்கும் & தானாக ஒத்திசைக்கும்", "தமிழ் மற்றும் ஆங்கிலத்தில் பேசும்", "எந்த போனிலும் பயன்படுத்தலாம்"]],
      ["எப்படி வேலை செய்கிறது", ["1. இலையைப் படம் எடுங்கள் அல்லது தேர்வு செய்யுங்கள்", "2. உடனடி ஆஃப்லைன் & பயிர் இடம் சோதனை", "3. ஆலோசனை மற்றும் தீர்வு", "4. குரல் வழிகாட்டுதல் & தரவுத்தளப் பதிவு"]],
      ["தரவுத்தளம் & ஒத்திசைவு", ["Cloud Firestore பதிவுகள் மற்றும் விருப்பங்களைச் சேமிக்கும்", "சிக்னல் இல்லாதபோது IndexedDB வரிசையில் சேமிக்கும்", "இணையம் திரும்பும்போது தானாக ஒத்திசைக்கும்"]]
    ]
  };

  var dk = document.createElement("div");
  dk.id = "deck";
  dk.setAttribute("role", "dialog");
  dk.setAttribute("aria-label", "Presentation");
  dk.innerHTML = '<div class="dk-bar"><i></i></div><div class="dk-stage"></div><div class="dk-ft"><span id="dkn"></span><div><button class="btn alt" id="dkb" type="button">◀ Back</button><button class="btn alt" id="dka" type="button">Auto</button><button class="btn" id="dkx" type="button">Next ▶</button><button class="btn alt" id="dkc" type="button" aria-label="Close">✕</button></div></div>';
  document.body.appendChild(dk);

  var stg = dk.querySelector(".dk-stage"), pbar = dk.querySelector(".dk-bar i"), di = 0, dbSlide = 0, dt = null, DS = SLDS.en;

  function dbuild(){
    DS = SLDS[cur === "ta" ? "ta" : "en"];
    stg.innerHTML = "";
    DS.forEach(function(s){
      var e = document.createElement("div");
      e.className = "dk-s";
      e.innerHTML = "<h2></h2><ul></ul>";
      e.firstChild.textContent = s[0];
      s[1].forEach(function(t){
        var li = document.createElement("li");
        li.textContent = t;
        e.lastChild.appendChild(li);
      });
      stg.appendChild(e);
    });
  }

  function dshow(n, full){
    di = n;
    var dsl = stg.children;
    dbSlide = full ? DS[n][1].length : 0;
    Array.prototype.forEach.call(dsl, function(e, i){
      e.className = "dk-s" + (i === n ? " cur" : "");
      Array.prototype.forEach.call(e.querySelectorAll("li"), function(li, j){
        li.classList.toggle("show", i < n || (i === n && j < dbSlide));
      });
    });
    pbar.style.width = ((n + 1) / DS.length * 100) + "%";
    document.getElementById("dkn").textContent = (n + 1) + " / " + DS.length;
  }

  function dnext(){
    var dsl = stg.children;
    if(dbSlide < DS[di][1].length){
      dsl[di].querySelectorAll("li")[dbSlide].classList.add("show");
      dbSlide++;
    } else if(di < DS.length - 1){
      dshow(di + 1, false);
    } else {
      clearInterval(dt); dt = null;
    }
  }

  function dprev(){
    if(dbSlide > 0){
      dbSlide--;
      stg.children[di].querySelectorAll("li")[dbSlide].classList.remove("show");
    } else if(di > 0){
      dshow(di - 1, true);
    }
  }

  document.getElementById("pres").onclick = function(){
    dbuild();
    dk.classList.add("on");
    dshow(0, false);
  };
  document.getElementById("dkc").onclick = function(){
    clearInterval(dt); dt = null;
    dk.classList.remove("on");
  };
  document.getElementById("dkx").onclick = dnext;
  document.getElementById("dkb").onclick = dprev;
  document.getElementById("dka").onclick = function(){
    if(dt){ clearInterval(dt); dt = null; }
    else { dt = setInterval(dnext, 2000); }
  };
  stg.onclick = dnext;

  /* Background Growing Plants on Scroll */
  var bg = document.getElementById("bgp");
  var lv = [[92,330,-25],[97,290,205],[100,240,-35],[100,195,215],[104,150,-40],[100,110,220],[100,60,-70]];
  var ph = '<svg class="pl" viewBox="0 0 200 400" style="STYLE"><path class="st" pathLength="1" d="M100 400 C100 330 85 280 100 220 C112 170 98 110 100 45"/>';
  lv.forEach(function(l){
    ph += '<g transform="translate(' + l[0] + ' ' + l[1] + ') rotate(' + l[2] + ')"><path class="lf" style="--t:' + ((400 - l[1]) / 355 * 0.8).toFixed(2) + '" d="M0 0 C20 -22 52 -22 68 0 C52 20 20 20 0 0Z"/></g>';
  });
  ph += '<circle class="fr" cx="116" cy="212" r="9"/></svg>';
  var G = "--g:calc(var(--p,0) * ";
  bg.innerHTML = '<div class="gd"></div><div class="sun"></div>' +
    ["left:-10px;height:52vh;" + G + "1.15)", "right:-10px;height:60vh;--f:-1;" + G + "1.05)", "left:22%;height:30vh;" + G + "1.25)"]
      .map(function(s){ return ph.replace("STYLE", s); }).join("");

  function updateScrollGrowth(){
    var h = document.documentElement.scrollHeight - window.innerHeight;
    var p = h > 0 ? Math.min(1, Math.max(0, window.scrollY / h)) : 0;
    bg.style.setProperty("--p", (0.08 + 0.92 * p).toFixed(3));
  }
  updateScrollGrowth();
  window.addEventListener("scroll", updateScrollGrowth, { passive: true });
  window.addEventListener("resize", updateScrollGrowth);

  initDatabaseLayer();
})();
</script>
</body>
</html>
