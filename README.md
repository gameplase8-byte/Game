<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Gaming Hub — App</title>
<style>
  :root{
    --bg:#0b0b0c;
    --panel: rgba(255,255,255,0.04);
    --muted:#bdbdbd;
    --accent:#ddd;
    --glass: rgba(255,255,255,0.03);
    --radius:12px;
    --soft: 0 6px 18px rgba(0,0,0,0.6);
    --glass-2: rgba(255,255,255,0.02);
  }
  *{box-sizing:border-box}
  html,body{height:100%}
  body{
    margin:0;
    font-family: Inter, Arial, Helvetica, sans-serif;
    background:var(--bg);
    color:var(--accent);
    -webkit-font-smoothing:antialiased;
    -moz-osx-font-smoothing:grayscale;
    overflow:hidden;
  }

  /* BACKGROUND GIF / VIDEO */
  #bg {
    position:fixed; inset:0; z-index:-2;
    filter:brightness(0.28) blur(0.2px);
    object-fit:cover; width:100%; height:100%;
  }

  /* LOADING SCREEN */
  #loading {
    position:fixed; inset:0; z-index:2000;
    display:flex; align-items:center; justify-content:center;
    background:linear-gradient(180deg, rgba(0,0,0,0.6), rgba(0,0,0,0.75));
    color:var(--accent);
    transition:opacity 300ms ease;
  }
  #loading.hide{opacity:0; pointer-events:none}
  .spinner{
    width:84px; height:84px; border-radius:50%;
    background: conic-gradient(#666, transparent 40%);
    mask: radial-gradient(farthest-side,#0000 calc(100% - 8px), #000 0);
    animation: spin 1.2s linear infinite;
    box-shadow:0 4px 18px rgba(0,0,0,0.6);
  }
  @keyframes spin {to{transform:rotate(360deg)}}

  /* APP FRAME */
  .app {
    position:relative;
    width:100%;
    height:100%;
    display:flex;
    flex-direction:column;
    overflow:hidden;
  }

  /* TOP BAR */
  header.topbar{
    position:fixed; left:12px; right:12px; top:12px;
    display:flex; align-items:center; gap:12px;
    padding:10px 14px;
    background:var(--glass);
    border-radius:14px;
    box-shadow:var(--soft);
    backdrop-filter: blur(8px) saturate(120%);
    z-index:1000;
    border:1px solid rgba(255,255,255,0.02);
  }
  .brand {
    display:flex; align-items:center; gap:12px;
  }
  .avatar {
    width:46px; height:46px; border-radius:10px;
    display:grid;place-items:center;
    background:linear-gradient(180deg,#1b1b1b,#111);
    font-weight:700; color:var(--accent); font-size:18px;
    border:1px solid rgba(255,255,255,0.03);
  }
  .brand h1{font-size:16px;margin:0}
  .brand p{font-size:12px;color:var(--muted);margin:0}

  .top-actions{margin-left:auto; display:flex; gap:8px; align-items:center}
  .icon-btn{
    background:var(--glass-2); border-radius:10px; padding:8px; display:inline-grid;
    place-items:center; cursor:pointer; border:1px solid rgba(255,255,255,0.02);
  }
  .icon-btn:hover{transform:translateY(-3px); transition:transform 160ms ease}

  /* MAIN VIEWS */
  .views {
    margin-top:84px; margin-bottom:84px;
    height: calc(100% - 168px);
    overflow:auto;
    padding:24px;
    display:grid;
    grid-template-columns: 1fr min(980px, 100%);
    justify-content:center;
    gap:20px;
  }

  /* center column card */
  .panel {
    grid-column:2;
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    padding:20px; border-radius:var(--radius); box-shadow:var(--soft);
    border:1px solid rgba(255,255,255,0.02);
    min-height:120px;
    transition:transform 220ms ease, box-shadow 220ms ease;
  }
  .panel.lite{background:transparent; box-shadow:none; border:none; padding:0}

  /* Pages */
  .page{display:none; opacity:0; transform:translateY(8px); transition:all 280ms ease}
  .page.active{display:block; opacity:1; transform:none}

  h2{margin:0 0 10px 0; font-size:20px}
  p.lead{color:var(--muted); margin:6px 0 18px 0; font-size:14px}

  /* GRID LAYOUTS */
  .row{display:flex; gap:16px; flex-wrap:wrap}
  .col{flex:1; min-width:200px}

  /* LIST BOX */
  .card {
    background:var(--panel); padding:14px; border-radius:12px;
    border:1px solid rgba(255,255,255,0.02); cursor:default;
  }
  .clickable{cursor:pointer}
  .card:hover{transform:translateY(-6px); transition:transform 180ms ease}

  /* game tiles */
  .game-tile{display:flex; gap:12px; align-items:center; padding:12px; border-radius:10px}
  .game-icon{
    width:56px;height:56px;border-radius:10px; display:grid;place-items:center;
    font-weight:700;font-size:20px;background:linear-gradient(180deg,#111,#0f0f10);
    border:1px solid rgba(255,255,255,0.02);
  }
  .game-info h3{margin:0;font-size:16px}
  .game-info p{margin:4px 0 0 0; color:var(--muted); font-size:13px}

  /* details lists */
  ul.clean{padding-left:18px;margin:8px 0 0 0}
  li{margin:6px 0;color:var(--accent)}

  /* bottom navigation */
  nav.bottom {
    position:fixed; left:12px; right:12px; bottom:12px;
    display:flex; justify-content:space-between; align-items:center;
    background:var(--glass); border-radius:14px; padding:10px 14px;
    z-index:1000; box-shadow:var(--soft); border:1px solid rgba(255,255,255,0.02);
  }
  .nav-item{display:flex; flex-direction:column; align-items:center; gap:6px; color:var(--muted); font-size:12px; cursor:pointer}
  .nav-item.active{color:var(--accent)}
  .nav-item .dot{width:8px;height:8px;border-radius:50%; background:transparent}
  .nav-item.active .dot{background:var(--accent); box-shadow:0 4px 10px rgba(0,0,0,0.6)}

  /* profile form */
  .field{display:flex; flex-direction:column; gap:6px; margin-bottom:12px}
  input[type="text"], select, textarea{
    background:#0f0f10;border:1px solid rgba(255,255,255,0.03); color:var(--accent);
    padding:10px;border-radius:8px;outline:none;
  }

  .muted {color:var(--muted); font-size:13px}

  /* leaderboard table */
  table{width:100%; border-collapse:collapse}
  th,td{padding:10px; text-align:left; border-bottom:1px dashed rgba(255,255,255,0.02)}
  th{color:var(--muted); font-size:13px}

  /* tiny animated icons (CSS-only) */
  .anim-icon{
    width:36px;height:36px;border-radius:8px;display:grid;place-items:center;
    background:linear-gradient(180deg,#0f0f10,#0b0b0c); border:1px solid rgba(255,255,255,0.02);
    transform-origin:center; transition:transform 220ms;
  }
  .anim-icon.pulse{animation:pulse 2s infinite}
  @keyframes pulse{0%{transform:scale(1)}50%{transform:scale(1.06)}100%{transform:scale(1)}}

  /* responsive adjustments */
  @media(max-width:980px){
    .views{grid-template-columns:1fr; padding:18px; margin-top:80px; margin-bottom:100px}
    .panel{grid-column:1}
  }
  @media(max-width:600px){
    header.topbar{left:8px; right:8px; top:8px;}
    nav.bottom{left:8px;right:8px;bottom:8px}
    .avatar{width:40px;height:40px}
  }

  /* small helper */
  .small{font-size:13px;color:var(--muted)}
  .btn {background:#1b1b1c;padding:10px 12px;border-radius:9px;border:1px solid rgba(255,255,255,0.03); cursor:pointer}
  .btn.ghost{background:transparent;border:1px dashed rgba(255,255,255,0.03)}
  .center{display:flex;align-items:center;gap:8px}
</style>
</head>
<body>

<!-- Background video/gif -->
<video id="bg" autoplay loop muted playsinline>
  <!-- fallback to MP4 looping background. Replace URL with any GIF/MP4 you prefer -->
  <source src="https://assets.mixkit.co/videos/preview/mixkit-neon-gaming-background-20428-large.mp4" type="video/mp4">
</video>

<!-- Loading overlay -->
<div id="loading">
  <div style="text-align:center">
    <div class="spinner" aria-hidden="true"></div>
    <div style="margin-top:12px;color:var(--muted)">Loading Gaming Hub…</div>
  </div>
</div>

<div class="app">

  <!-- top bar -->
  <header class="topbar">
    <div class="brand">
      <div class="avatar" id="topAvatar">G</div>
      <div>
        <h1 id="topName">Guest</h1>
        <p class="muted" id="topSub">Simple Dark Gaming Hub</p>
      </div>
    </div>

    <div class="top-actions">
      <div class="icon-btn" id="soundToggle" title="Toggle sound">
        <div class="anim-icon" id="soundIcon">🔊</div>
      </div>
      <div class="icon-btn" id="notificationsBtn" title="Notifications">
        <div class="anim-icon pulse">🔔</div>
      </div>
    </div>
  </header>

  <!-- main content -->
  <div class="views">

    <!-- placeholder left column for future widgets (kept empty for small screens) -->
    <div style="display:none"></div>

    <main class="panel" id="mainPanel">

      <!-- LOGIN PAGE -->
      <section id="page-login" class="page">
        <h2>Login</h2>
        <p class="muted">Enter a username to create your local profile. This is stored in your browser only.</p>

        <div class="field">
          <input id="loginName" type="text" placeholder="Enter username" />
        </div>
        <div class="row">
          <div class="col">
            <button class="btn" onclick="doLogin()">Continue</button>
            <button class="btn ghost" onclick="randomGuest()">Random Guest</button>
          </div>
        </div>
      </section>

      <!-- HOME PAGE -->
      <section id="page-home" class="page active">
        <h2 id="homeTitle">Welcome</h2>
        <p class="lead" id="homeSub">Choose a game below or view your profile and leaderboard.</p>

        <div class="row" style="margin-top:12px">
          <div class="col card clickable" onclick="openPage('games')">
            <div class="game-tile">
              <div class="game-icon">PG</div>
              <div class="game-info">
                <h3>Games</h3>
                <p class="muted">Select PUBG, Free Fire, CODM, Valorant, CS2</p>
              </div>
            </div>
          </div>

          <div class="col card clickable" onclick="openPage('leaderboard')">
            <div class="game-tile">
              <div class="game-icon">LB</div>
              <div class="game-info">
                <h3>Leaderboard</h3>
                <p class="muted">See top scorers and your rank</p>
              </div>
            </div>
          </div>

          <div class="col card clickable" onclick="openPage('profile')">
            <div class="game-tile">
              <div class="game-icon">PR</div>
              <div class="game-info">
                <h3>Profile</h3>
                <p class="muted">Edit your username or avatar initials</p>
              </div>
            </div>
          </div>

        </div>

        <div style="margin-top:18px" class="card">
          <h3>Quick Tips</h3>
          <ul class="clean">
            <li>Use headphones for positional audio.</li>
            <li>Practice recoil control in training mode.</li>
            <li>Watch pro players and copy movement patterns.</li>
          </ul>
        </div>

      </section>

      <!-- GAMES PAGE -->
      <section id="page-games" class="page">
        <button class="btn ghost" onclick="openPage('home')">← Back</button>
        <h2>Games</h2>
        <p class="muted">Tap a game to see weapons, top players and tips</p>

        <div style="margin-top:12px" class="row">
          <div class="col card clickable" onclick="openGame('pubg')">
            <div class="game-tile"><div class="game-icon">P</div><div class="game-info"><h3>PUBG Mobile</h3><p class="muted">Battle Royale</p></div></div>
          </div>

          <div class="col card clickable" onclick="openGame('freefire')">
            <div class="game-tile"><div class="game-icon">F</div><div class="game-info"><h3>Free Fire</h3><p class="muted">Arcade BR</p></div></div>
          </div>

          <div class="col card clickable" onclick="openGame('codm')">
            <div class="game-tile"><div class="game-icon">C</div><div class="game-info"><h3>COD Mobile</h3><p class="muted">MP & BR</p></div></div>
          </div>

          <div class="col card clickable" onclick="openGame('valorant')">
            <div class="game-tile"><div class="game-icon">V</div><div class="game-info"><h3>Valorant</h3><p class="muted">Tactical FPS</p></div></div>
          </div>

          <div class="col card clickable" onclick="openGame('cs2')">
            <div class="game-tile"><div class="game-icon">S</div><div class="game-info"><h3>CS2</h3><p class="muted">Classic Shooter</p></div></div>
          </div>
        </div>

      </section>

      <!-- GAME DETAILS PAGE -->
      <section id="page-game-details" class="page">
        <button class="btn ghost" onclick="openPage('games')">← Back</button>
        <h2 id="gdTitle">Game</h2>
        <p class="muted" id="gdSub">Weapons, best players and tips</p>

        <div style="margin-top:12px" class="row">
          <div class="col card">
            <h3>Best Weapons</h3>
            <ul id="gdWeapons" class="clean"></ul>
          </div>

          <div class="col card">
            <h3>Top Players</h3>
            <ul id="gdPlayers" class="clean"></ul>
          </div>

          <div class="col card">
            <h3>Pro Tips</h3>
            <ul id="gdTips" class="clean"></ul>
          </div>
        </div>
      </section>

      <!-- PROFILE PAGE -->
      <section id="page-profile" class="page">
        <button class="btn ghost" onclick="openPage('home')">← Back</button>
        <h2>Profile</h2>
        <p class="muted">Update your display name and avatar initial</p>

        <div style="margin-top:12px" class="card">
          <div class="field">
            <label class="small">Display name</label>
            <input id="profileName" type="text" />
          </div>

          <div class="field">
            <label class="small">Avatar initial</label>
            <input id="profileInitial" type="text" maxlength="2" />
          </div>

          <div class="row">
            <div class="col">
              <button class="btn" onclick="saveProfile()">Save</button>
              <button class="btn ghost" onclick="resetProfile()">Reset</button>
            </div>
          </div>
        </div>

        <div style="margin-top:12px" class="card">
          <h3>Local Stats</h3>
          <p class="muted">These are stored locally in your browser</p>
          <table id="localStats">
            <thead><tr><th>Stat</th><th>Value</th></tr></thead>
            <tbody>
              <tr><td>Play sessions</td><td id="statSessions">0</td></tr>
              <tr><td>Top score</td><td id="statTop">0</td></tr>
            </tbody>
          </table>
        </div>
      </section>

      <!-- LEADERBOARD PAGE -->
      <section id="page-leaderboard" class="page">
        <button class="btn ghost" onclick="openPage('home')">← Back</button>
        <h2>Leaderboard</h2>
        <p class="muted">Local leaderboard (you can add your score)</p>

        <div class="card" style="margin-top:12px">
          <div class="row" style="align-items:center">
            <div class="col">
              <input id="scoreInput" type="text" placeholder="Enter your score (number)" />
            </div>
            <div style="display:flex;gap:8px">
              <button class="btn" onclick="submitScore()">Submit</button>
              <button class="btn ghost" onclick="clearLeaderboard()">Clear</button>
            </div>
          </div>

          <div style="margin-top:12px; overflow:auto; max-height:280px">
            <table id="leaderTable">
              <thead><tr><th>#</th><th>Name</th><th>Score</th></tr></thead>
              <tbody></tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- SETTINGS PAGE -->
      <section id="page-settings" class="page">
        <button class="btn ghost" onclick="openPage('home')">← Back</button>
        <h2>Settings</h2>
        <p class="muted">Toggle features and preferences</p>

        <div style="margin-top:12px" class="card">
          <div class="row" style="align-items:center">
            <div class="col small">Sound effects</div>
            <div><button class="btn" id="soundBtn" onclick="toggleSound()">Toggle</button></div>
          </div>

          <div style="margin-top:10px" class="row" style="align-items:center">
            <div class="col small">Auto play loading animation (on load)</div>
            <div><button class="btn" id="loadBtn" onclick="toggleAutoLoad()">Toggle</button></div>
          </div>
        </div>

      </section>

    </main>
  </div>

  <!-- bottom nav -->
  <nav class="bottom" role="navigation" aria-label="Main">
    <div class="nav-item" data-page="home" onclick="openPage('home')">
      <div class="anim-icon">🏠</div>
      <div class="small">Home</div>
      <div class="dot"></div>
    </div>

    <div class="nav-item" data-page="games" onclick="openPage('games')">
      <div class="anim-icon">🎮</div>
      <div class="small">Games</div>
      <div class="dot"></div>
    </div>

    <div class="nav-item" data-page="leaderboard" onclick="openPage('leaderboard')">
      <div class="anim-icon">🏆</div>
      <div class="small">Leaderboard</div>
      <div class="dot"></div>
    </div>

    <div class="nav-item" data-page="profile" onclick="openPage('profile')">
      <div class="anim-icon">👤</div>
      <div class="small">Profile</div>
      <div class="dot"></div>
    </div>

    <div class="nav-item" data-page="settings" onclick="openPage('settings')">
      <div class="anim-icon">⚙️</div>
      <div class="small">Settings</div>
      <div class="dot"></div>
    </div>
  </nav>

</div>

<script>
/* ---------------------------
   App data + defaults
   --------------------------- */
const DEFAULT_USER = {name:'Guest', initial:'G', sessions:0, topScore:0};
const DEFAULT_LEAD = [
  {name:'s1mple', score:9800},
  {name:'ZywOo', score:9400},
  {name:'Tenz', score:9100},
  {name:'Scout', score:8900},
  {name:'Paraboy', score:8600}
];

let state = {
  user: {...DEFAULT_USER},
  soundOn: true,
  autoLoad: true,
  leaderboard: []
};

/* ---------------------------
   Utilities: storage + sounds
   --------------------------- */
function loadState(){
  try{
    const raw = localStorage.getItem('gaminghub_state');
    if(raw) {
      const parsed = JSON.parse(raw);
      state = {...state, ...parsed};
    } else {
      state.leaderboard = DEFAULT_LEAD.slice();
    }
  }catch(e){
    console.error(e);
    state.leaderboard = DEFAULT_LEAD.slice();
  }
}
function saveState(){ localStorage.setItem('gaminghub_state', JSON.stringify(state)); }

/* small webaudio click */
const AudioCtx = window.AudioContext || window.webkitAudioContext;
let audioCtx = null;
function playClick(type='click'){
  if(!state.soundOn) return;
  if(!audioCtx) audioCtx = new AudioCtx();
  const o = audioCtx.createOscillator();
  const g = audioCtx.createGain();
  o.type = type === 'click' ? 'square' : 'sine';
  o.frequency.value = type === 'click' ? 880 : 440;
  g.gain.value = 0.0001;
  o.connect(g); g.connect(audioCtx.destination);
  o.start();
  g.gain.exponentialRampToValueAtTime(0.06, audioCtx.currentTime + 0.002);
  g.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.12);
  o.stop(audioCtx.currentTime + 0.15);
}

/* ---------------------------
   UI wiring
   --------------------------- */
function setUserToUI(){
  document.getElementById('topName').innerText = state.user.name || 'Guest';
  document.getElementById('topAvatar').innerText = (state.user.initial||'G').toUpperCase();
  document.getElementById('homeTitle').innerText = `Welcome, ${state.user.name}`;
  document.getElementById('homeSub').innerText = `Ready to play? Sessions: ${state.user.sessions}`;
  document.getElementById('profileName').value = state.user.name;
  document.getElementById('profileInitial').value = state.user.initial;
  document.getElementById('statSessions').innerText = state.user.sessions;
  document.getElementById('statTop').innerText = state.user.topScore;
  // leader render
  renderLeaderboard();
}
function renderLeaderboard(){
  const tbody = document.querySelector('#leaderTable tbody');
  tbody.innerHTML = '';
  state.leaderboard.sort((a,b)=>b.score-a.score);
  state.leaderboard.forEach((p,i)=>{
    const tr = document.createElement('tr');
    tr.innerHTML = `<td>${i+1}</td><td>${escapeHtml(p.name)}</td><td>${p.score}</td>`;
    tbody.appendChild(tr);
  });
}

/* simple html escape */
function escapeHtml(s){ return String(s).replace(/[&<>"']/g, c=>({ '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;' }[c])); }

/* pages */
function openPage(page){
  playClick('click');
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  const el = document.getElementById('page-'+page);
  if(el) el.classList.add('active');
  // bottom nav highlight
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  const nav = document.querySelector(`.nav-item[data-page="${page}"]`);
  if(nav) nav.classList.add('active');
  // update title/sub as needed
}

/* login */
function doLogin(){
  const name = document.getElementById('loginName').value.trim();
  if(!name){ alert('Please enter a username'); return; }
  state.user.name = name;
  state.user.initial = (name[0]||'G').toUpperCase();
  saveState(); setUserToUI();
  playClick('sine');
  openPage('home');
}

/* random guest */
function randomGuest(){
  const names = ['Raven','Nova','Kite','Echo','Frost','Blitz','Viper'];
  const n = names[Math.floor(Math.random()*names.length)] + Math.floor(Math.random()*90);
  document.getElementById('loginName').value = n;
  doLogin();
}

/* profile */
function saveProfile(){
  const name = document.getElementById('profileName').value.trim();
  const initial = document.getElementById('profileInitial').value.trim().slice(0,2).toUpperCase()|| (name[0]||'G').toUpperCase();
  if(name) state.user.name = name;
  state.user.initial = initial;
  saveState(); setUserToUI(); playClick('click');
  alert('Profile saved locally');
}
function resetProfile(){
  if(confirm('Reset profile to default?')) {
    state.user = {...DEFAULT_USER};
    saveState(); setUserToUI(); playClick('sine');
  }
}

/* leaderboard functions */
function submitScore(){
  const raw = document.getElementById('scoreInput').value.trim();
  const n = Number(raw);
  if(!raw || isNaN(n)){ alert('Enter a numeric score'); return; }
  state.leaderboard.push({name:state.user.name || 'Guest', score:n});
  // update local stats
  state.user.sessions = (state.user.sessions||0) + 1;
  if(!state.user.topScore || n > state.user.topScore) state.user.topScore = n;
  saveState(); setUserToUI(); playClick('click');
  document.getElementById('scoreInput').value = '';
}
function clearLeaderboard(){
  if(!confirm('Clear leaderboard? This is local only.')) return;
  state.leaderboard = [];
  saveState(); renderLeaderboard(); playClick('sine');
}

/* settings toggles */
function toggleSound(){
  state.soundOn = !state.soundOn; saveState();
  document.getElementById('soundIcon').innerText = state.soundOn ? '🔊' : '🔈';
  playClick('sine');
}
function toggleAutoLoad(){
  state.autoLoad = !state.autoLoad; saveState();
  alert('Auto load ' + (state.autoLoad ? 'enabled' : 'disabled'));
}

/* game data */
const gameData = {
  pubg: {
    title:'PUBG Mobile',
    weapons:["M416","AKM","AWM","DP-28","UMP45"],
    players:["Paraboy","Jonathan","Order","Scout"],
    tips:["Use cover","Master recoil","Use sound cues"]
  },
  freefire:{
    title:'Free Fire',
    weapons:["MP40","M1887","SCAR","AWM"],
    players:["RUOK","TSG Jash","Nayeem"],
    tips:["Use gloo walls","Peek & shoot","Be unpredictable"]
  },
  codm:{
    title:'COD Mobile',
    weapons:["AK117","QXR","DL Q33","KRM-262"],
    players:["Jokesta","Godzly","Nero"],
    tips:["Use movement","Optimize loadout","Map control"]
  },
  valorant:{
    title:'Valorant',
    weapons:["Vandal","Phantom","Operator","Ghost"],
    players:["Tenz","ScreaM","Derke"],
    tips:["Crosshair placement","Use utility","Callouts & trade"]
  },
  cs2:{
    title:'CS2',
    weapons:["AK-47","M4A4","AWP","Deagle"],
    players:["s1mple","ZywOo","NiKo"],
    tips:["Spray control","Map knowledge","Team coordination"]
  }
};

function openGame(key){
  const gd = gameData[key];
  if(!gd) return;
  document.getElementById('gdTitle').innerText = gd.title;
  document.getElementById('gdSub').innerText = 'Weapons, Top players & Tips';
  fillList('gdWeapons', gd.weapons);
  fillList('gdPlayers', gd.players);
  fillList('gdTips', gd.tips);
  openPage('game-details');
  playClick('click');
}
function fillList(id, arr){
  const ul = document.getElementById(id);
  ul.innerHTML = '';
  arr.forEach(i=>{
    const li = document.createElement('li'); li.innerText = i;
    ul.appendChild(li);
  });
}

/* escape hatch for storage clearing during dev */
// localStorage.removeItem('gaminghub_state');

/* ---------------------------
   Init / load
   --------------------------- */
function initApp(){
  loadState();
  // ensure user object exists
  state.user = {...DEFAULT_USER, ...(state.user||{})};
  document.getElementById('loginName').value = state.user.name === 'Guest' ? '' : state.user.name;
  setUserToUI();
  // auto load logic
  if(state.autoLoad){
    setTimeout(()=>{ document.getElementById('loading').classList.add('hide'); }, 700);
  } else {
    document.getElementById('loading').classList.add('hide');
  }
  // remove loading element after animation
  setTimeout(()=>{ const el = document.getElementById('loading'); if(el) el.style.display='none'; }, 1200);

  // default landing: if user is guest show login; else home
  if(!state.user || !state.user.name || state.user.name === 'Guest'){
    openPage('login'); // first-time login
  } else {
    openPage('home');
  }

  // menu bottom default highlight
  document.querySelectorAll('.nav-item')[0].classList.add('active');

  // attach top action
  document.getElementById('soundToggle').onclick = ()=>{ toggleSound(); };
  // set sound icon initially
  document.getElementById('soundIcon').innerText = state.soundOn ? '🔊' : '🔈';

  // Render leaderboard
  if(!state.leaderboard || state.leaderboard.length === 0) state.leaderboard = DEFAULT_LEAD.slice();
  renderLeaderboard();
}

function submitScoreDemo(){
  // small demo to push a random score for your name (for demo)
  state.leaderboard.push({name: state.user.name || 'Guest', score: Math.floor(Math.random()*10000)});
  saveState(); renderLeaderboard();
}

/* small helper: increment session count when leaving game or submitting */
window.addEventListener('beforeunload', ()=>{ state.user.sessions = (state.user.sessions||0)+1; saveState(); });

/* wire nav items click highlight */
document.querySelectorAll('.nav-item').forEach(n=>{
  n.addEventListener('click', ()=>{ document.querySelectorAll('.nav-item').forEach(x=>x.classList.remove('active')); n.classList.add('active'); });
});

/* show loading then init */
document.addEventListener('DOMContentLoaded', initApp);
</script>
</body>
</html>
