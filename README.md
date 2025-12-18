<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>AI x Student Space Invaders (Waves)</title>
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <style>
    :root{--bg:#060b1a;--accent:#7df9ff;--enemy:#ff5f7e;--player:#7cff7a;--hud:#ffffff}
    *{box-sizing:border-box}
    body{margin:0;font-family:system-ui,-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Helvetica,Arial;background:radial-gradient(1200px 600px at 50% 100%,#0c153a,var(--bg));color:var(--hud);display:flex;flex-direction:column;align-items:center;min-height:100vh}
    header{padding:1rem 1.25rem .5rem;text-align:center}
    header h1{margin:0 0 .25rem;letter-spacing:.08em;color:var(--accent)}
    header p{margin:0;font-size:.9rem;opacity:.85}
    .game-wrap{margin-top:.75rem;position:relative;width:360px;max-width:95vw;aspect-ratio:3 / 4;background:linear-gradient(#02050e,#08123a);border-radius:18px;box-shadow:0 20px 60px rgba(0,0,0,.6);overflow:hidden}
    canvas{width:100%;height:100%;display:block}
    .hud{position:absolute;inset:0;pointer-events:none;display:grid;grid-template-columns:1fr 1fr;padding:10px 14px;font-weight:600;text-shadow:0 2px 6px rgba(0,0,0,.6)}
    .hud .right{text-align:right}
    .center-overlay{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;background:linear-gradient(rgba(6,11,26,.75),rgba(6,11,26,.75));text-align:center;padding:1.25rem}
    .card{background:rgba(5,10,30,.85);border:1px solid rgba(125,249,255,.25);border-radius:16px;padding:1.25rem;max-width:320px;box-shadow:0 20px 50px rgba(0,0,0,.6)}
    .card h2{margin:0 0 .4rem;color:var(--accent);letter-spacing:.08em}
    .card p{margin:0 0 .9rem;font-size:.9rem;line-height:1.4;opacity:.95}
    .btn{appearance:none;border:0;border-radius:999px;padding:.6rem 1.2rem;background:linear-gradient(135deg,var(--accent),#3fd6ff);color:#06101f;font-weight:700;letter-spacing:.06em;cursor:pointer;box-shadow:0 10px 28px rgba(63,214,255,.35)}
    .controls{margin-top:.9rem;display:grid;grid-template-columns:1fr 1fr;gap:.5rem .75rem;font-size:.8rem;opacity:.9}
    footer{margin:.9rem 0 1.2rem;font-size:.75rem;opacity:.7}
  </style>
</head>
<body>
  <header>
    <h1>SPACE INVADERS</h1>
    <p>Each wave changes — single-file app</p>
  </header>

  <div class="game-wrap">
    <canvas id="game" width="360" height="480"></canvas>
    <div class="hud">
      <div id="score">Score: 0</div>
      <div id="lives" class="right">Lives: 3</div>
    </div>
  </div>

  <footer>Created by student + AI • Single-file HTML/CSS/JS</footer>

  <script>
    // Game logic here (unchanged)
  </script>
</body>
</html>

