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
    @media(hover:none){.touch{position:absolute;bottom:10px;left:50%;transform:translateX(-50%);display:flex;gap:10px}.touch button{pointer-events:auto;width:56px;height:56px;border-radius:50%;border:0;background:rgba(125,249,255,.15);color:var(--hud);font-size:1.1rem;font-weight:700;backdrop-filter:blur(6px)}}
  </style>
</head>
<body>
  <header>
    <h1>SPACE INVADERS</h1>
    <p>Each wave changes — single‑file app</p>
  </header>

  <div class="game-wrap">
    <canvas id="game" width="360" height="480"></canvas>
    <div class="hud">
      <div id="score">Score: 0</div>
      <div id="lives" class="right">Lives: 3</div>
    </div>

    <div id="overlay" class="center-overlay">
      <div class="card">
        <h2 id="overlayTitle">READY?</h2>
        <p id="overlayText">Defend the galaxy. Move with A/D or ←/→ and fire with Space. Waves get harder and different.</p>
        <button id="startBtn" class="btn">START GAME</button>
        <div class="controls">
          <div>Move: A / D or ← / →</div>
          <div>Shoot: Space</div>
          <div>Restart: R</div>
          <div>Pause: P</div>
        </div>
      </div>
    </div>

    <div class="touch" id="touchControls" hidden>
      <button id="leftBtn">◀</button>
      <button id="fireBtn">▲</button>
      <button id="rightBtn">▶</button>
    </div>
  </div>

  <footer>Created by student + AI • Single‑file HTML/CSS/JS</footer>

  <script>
// ============================
//  GALAGA CLONE (STABLE)
// ============================

const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const W = canvas.width;
const H = canvas.height;

const scoreEl = document.getElementById('score');
const livesEl = document.getElementById('lives');
const overlay = document.getElementById('overlay');
const overlayTitle = document.getElementById('overlayTitle');
const overlayText = document.getElementById('overlayText');
const startBtn = document.getElementById('startBtn');

let running=false, paused=false;
let score=0, lives=3, wave=1, frame=0;

const keys = new Set();

const player={ x:W/2, y:H-42, w:28, h:24, speed:4, cooldown:0 };
let bullets=[], enemyBullets=[], enemies=[], particles=[];

function clamp(v,a,b){ return Math.max(a,Math.min(b,v)); }

function updateHUD(){
  scoreEl.textContent=`Score: ${score}  |  Wave: ${wave}`;
  livesEl.textContent=`Lives: ${lives}`;
}

function resetGame(){
  score=0; lives=3; wave=1; frame=0;
  bullets=[]; enemyBullets=[]; enemies=[]; particles=[];
  player.x=W/2; player.cooldown=0;
  spawnWave(); updateHUD();
}

function spawnWave(){
  enemies=[];
  const rows=3+Math.min(wave,3);
  const cols=8;
  const gap=34;
  const startX=(W-(cols-1)*gap)/2;
  const startY=70;

  for(let r=0;r<rows;r++){
    for(let c=0;c<cols;c++){
      enemies.push({
        x:startX+c*gap,
        y:startY+r*gap,
        w:24,h:18,
        tx:startX+c*gap,
        ty:startY+r*gap,
        state:'formation',
        diveTimer:120+Math.random()*120,
        angle:0,
        alive:true
      });
    }
  }
}

function firePlayer(){
  if(player.cooldown>0) return;
  bullets.push({x:player.x,y:player.y-10,dy:-7});
  player.cooldown=12;
}

function fireEnemy(e){ enemyBullets.push({x:e.x,y:e.y+8,dy:2}); }

function explode(x,y,color){
  for(let i=0;i<12;i++) particles.push({x,y,dx:(Math.random()-.5)*4,dy:(Math.random()-.5)*4,life:30,color});
}

function update(){
  if(!running||paused) return;
  frame++;

  // Player
  if(keys.has('left')) player.x-=player.speed;
  if(keys.has('right')) player.x+=player.speed;
  player.x=clamp(player.x,14,W-14);
  if(keys.has('fire')) firePlayer();
  if(player.cooldown>0) player.cooldown--;

  // Bullets
  bullets.forEach(b=>b.y+=b.dy);
  bullets=bullets.filter(b=>b.y>-20);
  enemyBullets.forEach(b=>b.y+=b.dy);
  enemyBullets=enemyBullets.filter(b=>b.y<H+20);

  // Enemy behavior
  enemies.forEach(e=>{
    if(!e.alive) return;

    if(e.state==='formation'){
      e.x += Math.sin(frame*0.01+e.ty*0.01)*0.4;
      e.diveTimer--;
      if(e.diveTimer<=0){ e.state='diving'; e.angle=0; }
    }

    if(e.state==='diving'){
      e.angle+=0.05;
      e.x += Math.sin(e.angle)*3;
      e.y += 3;
      if(frame%80===0) fireEnemy(e);
      if(e.y>H+40){ e.state='return'; }
    }

    if(e.state==='return'){
      e.x += (e.tx-e.x)*0.05;
      e.y += (e.ty-e.y)*0.05;
      if(Math.abs(e.y-e.ty)<1){ e.state='formation'; e.diveTimer=120+Math.random()*120; }
    }
  });

  // Collisions: Player bullets → Enemies
  bullets.forEach(b=>{
    enemies.forEach(e=>{
      if(e.alive && b.x>e.x-e.w/2 && b.x<e.x+e.w/2 && b.y>e.y-e.h/2 && b.y<e.y+e.h/2){
        b.y=-100; e.alive=false; score+=20*wave; explode(e.x,e.y,'#ff5f7e'); updateHUD();
      }
    });
  });

  // Enemy bullets → Player
  enemyBullets.forEach(b=>{
    if(b.x>player.x-player.w/2 && b.x<player.x+player.w/2 && b.y>player.y-player.h/2 && b.y<player.y+player.h/2){
      b.y=H+100; lives--; explode(player.x,player.y,'#7cff7a'); updateHUD(); if(lives<=0) endGame();
    }
  });

  // Enemies → Player
  enemies.forEach(e=>{
    if(e.alive && e.y+e.h/2>=player.y-player.h/2 && Math.abs(e.x-player.x)<(e.w+player.w)/2){
      lives--; e.alive=false; explode(player.x,player.y,'#7cff7a'); updateHUD(); if(lives<=0) endGame();
    }
  });

  // Particles
  particles.forEach(p=>{p.x+=p.dx; p.y+=p.dy; p.life--;});
  particles=particles.filter(p=>p.life>0);

  // Next wave
  if(enemies.every(e=>!e.alive)){ wave++; spawnWave(); updateHUD(); }
}

function draw(){
  ctx.clearRect(0,0,W,H);

  // Stars
  ctx.fillStyle='rgba(255,255,255,.15)';
  for(let i=0;i<40;i++){ const x=(i*87+frame*0.3)%W; const y=(i*61+frame*0.5)%H; ctx.fillRect(x,y,1.2,1.2); }

  // Player
  ctx.fillStyle='#7cff7a';
  ctx.fillRect(player.x-player.w/2,player.y-player.h/2,player.w,player.h);

  // Bullets
  ctx.fillStyle='#fff'; bullets.forEach(b=>ctx.fillRect(b.x-1,b.y-6,2,6));
  ctx.fillStyle='#ff5f7e'; enemyBullets.forEach(b=>ctx.fillRect(b.x-2,b.y,4,6));

  // Enemies
  ctx.fillStyle='#ff5f7e';
  enemies.forEach(e=>{ if(e.alive) ctx.fillRect(e.x-e.w/2,e.y-e.h/2,e.w,e.h); });

  // Particles
  particles.forEach(p=>{ ctx.fillStyle=p.color; ctx.globalAlpha=p.life/40; ctx.fillRect(p.x,p.y,2,2); ctx.globalAlpha=1; });
}

function loop(){ update(); draw(); requestAnimationFrame(loop); }

function startGame(){ resetGame(); overlay.style.display='none'; running=true; paused=false; }
function endGame(){ running=false; overlay.style.display='flex'; overlayTitle.textContent='GAME OVER'; overlayText.textContent=`Final Score: ${score} — Wave ${wave}`; }

startBtn.addEventListener('click',startGame);

window.addEventListener('keydown',e=>{
  if(e.code==='ArrowLeft'||e.key==='a') keys.add('left');
  if(e.code==='ArrowRight'||e.key==='d') keys.add('right');
  if(e.code==='Space') keys.add('fire');
  if(e.key==='p') paused=!paused;
  if(e.key==='r'&&!running) startGame();
});

window.addEventListener('keyup',e=>{
  if(e.code==='ArrowLeft'||e.key==='a') keys.delete('left');
  if(e.code==='ArrowRight'||e.key==='d') keys.delete('right');
  if(e.code==='Space') keys.delete('fire');
});

loop();
</script>
</body>
</html>
