<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Rock Paper Scissors - HTML5</title>
  <style>
    :root{
      --bg1:#e6f7ea;
      --bg2:#c7e9d2;
      --card:#ffffffcc;
      --green:#4CAF50;
      --green-dark:#3a8f3f;
      --text:#1b1b1b;
    }
    * { box-sizing: border-box; }
    html, body { height: 100%; margin: 0; font-family: system-ui, -apple-system, Segoe UI, Roboto; color:var(--text); }
    body {
      background: radial-gradient(circle at 20% 20%, rgba(76, 217, 178, 0.25), transparent 40%),
                  radial-gradient(circle at 80% 60%, rgba(70, 130, 180, 0.15), transparent 40%),
                  linear-gradient(135deg, var(--bg1), var(--bg2));
      background-attachment: fixed;
      overflow-x:hidden;
    }
    .scene { min-height: 100%; display:flex; align-items:center; justify-content:center; padding: 40px 20px; }
    .card {
      background: var(--card);
      border-radius: 16px;
      padding: 20px;
      box-shadow: 0 20px 40px rgba(0,0,0,.08);
      border: 1px solid #ddd;
    }
    .menu { text-align:center; max-width: 860px; }
    .title { font-size: 28px; font-weight: 700; margin-bottom: 14px; }
    .subtitle { color:#555; margin-bottom: 20px; }
    .options { display:flex; gap:20px; justify-content: center; flex-wrap: wrap; }
    .option {
      width: 180px; height: 120px; border-radius: 14px; border: none; cursor: pointer;
      background: #5fbd6d; color:white; position: relative; padding: 0; overflow:hidden;
      display:flex; align-items: center; justify-content: center; flex-direction: column;
      box-shadow: 0 6px 12px rgba(0,0,0,.15); transition: transform .2s ease;
      text-decoration:none; text-align:center;
    }
    .option:hover { transform: translateY(-3px); }
    .option:focus { outline: 2px solid #fff; outline-offset: 2px; }
    .opt-label { position: relative; z-index: 2; font-weight: 700; }
    .anim { position: absolute; right: 8px; bottom: 8px; width: 44px; height: 44px; opacity:.9; }
    .screen { display:flex; flex-direction:column; align-items:center; justify-content:center; gap:20px; }
    .hidden { display:none; }
    .board { display:flex; align-items: center; justify-content: center; gap: 40px; flex-wrap: wrap; }
    .choices { display:flex; gap: 20px; }
    .choice {
      width: 140px; height: 140px; border-radius: 16px; border:1px solid #ddd; background:#fff; cursor:pointer;
      display:flex; flex-direction:column; align-items:center; justify-content:center; padding: 10px; box-shadow: 0 6px 12px rgba(0,0,0,.08);
    }
    .move-label { margin-top: 6px; font-weight:600; font-size:14px; }
    .icon { width: 90px; height: 90px; display:block; }
    .result { margin-top: 12px; font-size: 16px; min-height: 24px; }
    .score { font-size: 18px; font-weight: 700; margin-top: 6px; }
    .footer { margin-top: 8px; }
    .btn { background:#4CAF50; color:white; border:none; padding:8px 12px; border-radius: 8px; cursor:pointer; }
    .btn.secondary { background:#6c757d; }
    /* multiplayer area */
    .room-area { display:flex; gap:16px; align-items:center; justify-content: center; }
    .card-input { display:flex; gap:8px; align-items:center; }
    input, button { font-family: inherit; font-size: 14px; }
    input { padding: 8px 10px; border-radius: 6px; border:1px solid #ccc; }
    .play-area { display:flex; flex-direction:column; align-items:center; gap:20px; }
    .player-area, .opponent-area { width: 240px; text-align:center; }
    .center-area { display:flex; align-items:center; justify-content:center; gap:20px; }
    .vs-label { font-size: 20px; color:#555; }
    .moves { min-height: 40px; }
    .move-btn { width: 100px; height: 40px; border-radius: 8px; border:1px solid #ddd; background:#fff; cursor:pointer; }
    @media (max-width: 720px){
      .option { width: 150px; height: 110px; }
      .choice { width:120px; height:120px; }
      .center-area { flex-direction: column; }
    }
    /* simple animations for Rock/Paper/Scissors icons */
    .rock { fill:#2b2b2b; }
    .paper { fill:#f4f4f4; stroke:#333; stroke-width:1; }
    .scissors { fill:#d23; }
    @keyframes pulse { 0%{ transform: scale(1); } 50%{ transform: scale(1.08); } 100%{ transform: scale(1); } }
    .pulse { animation: pulse 0.9s ease-in-out infinite; }
  </style>
</head>
<body>
  <div class="scene">
    <div class="card menu" id="menu">
      <div class="title">Rock Paper Scissors</div>
      <div class="subtitle">Three choices. One aim: outplay your opponent.</div>
      <div class="options">
        <button class="option" id="opt-single" aria-label="Single Player">
          <div class="opt-label">Single Player</div>
          <svg class="anim" viewBox="0 0 100 100" aria-label="single-player-icon">
            <g class="pulse">
              <circle cx="25" cy="50" r="22" fill="#555" />
            </g>
          </svg>
        </button>
        <button class="option" id="opt-create" aria-label="Create Room">
          <div class="opt-label">Create Room</div>
          <svg class="anim" viewBox="0 0 100 100">
            <g class="pulse">
              <rect x="20" y="20" width="60" height="60" rx="8" fill="#4CAF50"/>
            </g>
          </svg>
        </button>
        <button class="option" id="opt-join" aria-label="Join Room">
          <div class="opt-label">Join Room</div>
          <svg class="anim" viewBox="0 0 100 100">
            <g class="pulse">
              <circle cx="50" cy="50" r="28" fill="#3b82f6"/>
            </g>
          </svg>
        </button>
      </div>
    </div>

    <div class="card screen hidden" id="single-screen">
      <div class="title">Single Player</div>
      <div class="board">
        <div class="choices" id="single-choices">
          <button class="choice" data-move="rock" aria-label="Rock">
            <svg class="icon rock" viewBox="0 0 100 100"><circle cx="50" cy="50" r="28" fill="#555"/></svg>
            <div class="move-label">Rock</div>
          </button>
          <button class="choice" data-move="paper" aria-label="Paper">
            <svg class="icon paper" viewBox="0 0 100 100"><rect x="20" y="15" width="60" height="70" rx="8" fill="#eee" stroke="#333"/></svg>
            <div class="move-label">Paper</div>
          </button>
          <button class="choice" data-move="scissors" aria-label="Scissors">
            <svg class="icon scissors" viewBox="0 0 100 100"><path d="M20 60 C40 40, 60 40, 80 60" stroke="#d00" stroke-width="12" fill="none"/></svg>
            <div class="move-label">Scissors</div>
          </button>
        </div>
      </div>
      <div class="result" id="single-result"></div>
      <div class="score" id="single-score">Score: 0 - 0</div>
      <div class="footer"><button class="btn" id="single-back">Back</button></div>
    </div>

    <div class="card screen hidden" id="multi-screen">
      <div class="title">Multiplayer</div>
      <div class="room-area">
        <button class="btn" id="btn-create-room">Create Room</button>
        <button class="btn" id="btn-join-room">Join Room</button>
      </div>
      <div class="room-area">
        <div id="room-panel" class="card" style="display:none; padding:14px; align-items:center; flex-direction:column; gap:10px;">
          <div class="card-input"><label>Room Code</label><input id="room-code" placeholder="Code goes here" /></div>
          <div class="card-input"><button class="btn" id="btn-connect">Connect</button><button class="btn secondary" id="btn-copy">Copy</button></div>
          <div id="room-status" style="font-size:14px;color:#555;">Not connected</div>
        </div>
      </div>
      <div class="play-area" id="play-area" style="display:none;">
        <div class="center-area" style="gap:40px; align-items:flex-start;">
          <div class="player-area"><div class="title">You</div><div id="you-move" class="moves"></div></div>
          <div class="vs-label">VS</div>
          <div class="opponent-area"><div class="title">Opponent</div><div id="opp-move" class="moves"></div></div>
        </div>
        <div class="choices" style="gap:18px;">
          <button class="move-btn" data-move="rock">Rock</button>
          <button class="move-btn" data-move="paper">Paper</button>
          <button class="move-btn" data-move="scissors">Scissors</button>
        </div>
      </div>
      <div class="footer"><button class="btn" id="multi-back">Back</button></div>
    </div>
  </div>
  <script>
    // Core game logic: a self-contained interface with a placeholder multiplayer flow.
    (function(){
      const MOVES = ['rock','paper','scissors'];
      const menu = document.getElementById('menu');
      const singleScreen = document.getElementById('single-screen');
      const multiScreen = document.getElementById('multi-screen');
      const singleBack = document.getElementById('single-back');
      const multiBack = document.getElementById('multi-back');
      const singleResult = document.getElementById('single-result');
      const singleScore = document.getElementById('single-score');
      const singleChoices = document.getElementById('single-choices');
      const youMove = document.getElementById('you-move');
      const oppMove = document.getElementById('opp-move');

      function show(name){
        menu.classList.add('hidden');
        if(name==='single') singleScreen.classList.remove('hidden');
        if(name==='multi') multiScreen.classList.remove('hidden');
      }
      document.getElementById('opt-single').addEventListener('click', ()=> show('single'));
      document.getElementById('opt-create').addEventListener('click', ()=> show('multi'));
      document.getElementById('opt-join').addEventListener('click', ()=> show('multi'));
      singleBack.addEventListener('click', ()=>{ singleScreen.classList.add('hidden'); menu.classList.remove('hidden'); resetSingle(); });
      multiBack.addEventListener('click', ()=>{ multiScreen.classList.add('hidden'); menu.classList.remove('hidden'); });

      // Single-player logic
      let scoreA = 0, scoreB = 0;
      function resetSingle(){ singleResult.textContent = ''; singleScore.textContent = 'Score: 0 - 0'; scoreA = 0; scoreB = 0; }
      singleChoices.addEventListener('click', (e)=>{
        const btn = e.target.closest('.choice'); if(!btn) return; const userMove = btn.dataset.move;
        const comp = MOVES[Math.floor(Math.random()*MOVES.length)];
        const r = decide(userMove, comp);
        singleResult.textContent = `You chose ${capitalize(userMove)}. Computer chose ${capitalize(comp)}. ${r===1?'You win!': r===-1?'Computer wins!': "It's a tie!"}`;
        if (r===1) scoreA++; else if (r===-1) scoreB++;
        singleScore.textContent = `Score: ${scoreA} - ${scoreB}`;
      });
      function capitalize(s){ return s.charAt(0).toUpperCase() + s.slice(1); }
      function decide(a,b){ if(a===b) return 0; if((a==='rock'&&b==='scissors') || (a==='paper'&&b==='rock') || (a==='scissors'&&b==='paper')) return 1; return -1; }

      // Multiplayer placeholders: minimal UI to illustrate flow
      const btnCreateRoom = document.getElementById('btn-create-room');
      const btnJoinRoom = document.getElementById('btn-join-room');
      const roomPanel = document.getElementById('room-panel');
      const roomCodeInput = document.getElementById('room-code');
      const btnConnect = document.getElementById('btn-connect');
      const btnCopy = document.getElementById('btn-copy');
      const roomStatus = document.getElementById('room-status');
      const playArea = document.getElementById('play-area');
      const youMovePanel = document.getElementById('you-move');
      const oppMovePanel = document.getElementById('opp-move');
      const moveButtons = Array.from(document.querySelectorAll('.move-btn'));

      btnCreateRoom.addEventListener('click', ()=>{
        const code = Math.random().toString(36).slice(2,8).toUpperCase();
        roomCodeInput.value = code;
        roomPanel.style.display = 'flex';
        roomStatus.textContent = 'Room created. Share this code to invite others.';
      });
      btnJoinRoom.addEventListener('click', ()=>{ roomPanel.style.display = 'flex'; roomStatus.textContent = 'Enter code and connect to join a room.'; });
      btnCopy.addEventListener('click', ()=>{ navigator.clipboard.writeText(roomCodeInput.value).then(()=>{ btnCopy.textContent = 'Copied'; setTimeout(()=>{ btnCopy.textContent = 'Copy'; }, 1200); }); });
      btnConnect.addEventListener('click', ()=>{
        const code = roomCodeInput.value.trim(); if(!code){ roomStatus.textContent = 'Please enter a room code'; return; }
        roomStatus.textContent = 'Connecting to room ' + code + '...';
        roomPanel.style.display = 'none'; playArea.style.display = 'block';
        // Real signaling would initialize here
      });
        moveButtons.forEach(b => b.addEventListener('click', ()=>{
        // Demo: simulate a round with a random opponent move
        if (playArea.style.display !== 'block') return;
        const userMove = b.dataset.move;
        youMovePanel.textContent = 'You: ' + capitalize(userMove);
        setTimeout(()=>{
          const opp = MOVES[Math.floor(Math.random()*MOVES.length)];
          oppMovePanel.textContent = 'Opponent: ' + capitalize(opp);
          const r = decide(userMove, opp);
          roomStatus.textContent = r===1 ? 'You win!' : r===-1 ? 'You lose!' : "It's a tie!";
        }, 600);
      }));
      function capitalize(s){ return s.charAt(0).toUpperCase() + s.slice(1); }
      function decide(a,b){ if(a===b) return 0; if((a==='rock'&&b==='scissors') || (a==='paper'&&b==='rock') || (a==='scissors'&&b==='paper')) return 1; return -1; }
    })();
  </script>
</body>
</html>
