---
layout: none
permalink: /wordgame/
---

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>For You 💙 — Word Game</title>
  <style>
    :root {
      --bg: #0b0f17;
      --panel: #121826;
      --tile-empty: #1f2937;
      --tile-text: #e5e7eb;
      --correct: #22c55e;  /* green */
      --present: #f59e0b;  /* yellow */
      --absent: #374151;   /* gray */
      --accent: #60a5fa;   /* blue */
    }
    * { box-sizing: border-box; }
    html, body { height: 100%; }
    body {
      margin: 0; font-family: system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial, "Noto Sans", "Apple Color Emoji", "Segoe UI Emoji";
      background: radial-gradient(80vw 80vh at 50% -10%, #1c2541 0%, var(--bg) 45%, #05070b 100%);
      color: var(--tile-text);
      display: grid; place-items: center; padding: 16px;
    }
    .app {
      width: min(560px, 100%); background: color-mix(in oklab, var(--panel) 80%, #000 20%);
      border-radius: 20px; padding: 16px 16px 20px; box-shadow: 0 10px 40px rgba(0,0,0,.45);
    }
    header { display: flex; align-items: center; justify-content: space-between; padding: 6px 4px 12px; }
    h1 { font-size: 20px; margin: 0; letter-spacing: .5px; }
    .btn { cursor: pointer; border: 0; border-radius: 12px; padding: 10px 14px; background: #0f172a; color: #e5e7eb; box-shadow: inset 0 0 0 1px #1f2937; }
    .btn:active { transform: translateY(1px); }

    .topbar { display: flex; gap: 8px; align-items: center; }
    .pill {
      display: inline-flex; align-items: center; gap: 8px; padding: 6px; border-radius: 999px; background: #0d1322; box-shadow: inset 0 0 0 1px #1f2937;
    }
    .pill button { background: transparent; border: 0; color: #cbd5e1; padding: 8px 12px; border-radius: 999px; cursor: pointer; }
    .pill button.active { background: var(--accent); color: #001022; font-weight: 700; }

    .board { display: grid; grid-template-rows: repeat(var(--max-rows), 1fr); gap: 8px; margin: 12px 4px; }
    .row { display: grid; grid-template-columns: repeat(var(--word-len), 1fr); gap: 8px; }
    .tile { width: 100%; aspect-ratio: 1/1; border-radius: 12px; display: grid; place-items: center; font-weight: 800; font-size: clamp(18px, 6.4vw, 28px); text-transform: uppercase; background: var(--tile-empty); color: var(--tile-text); box-shadow: inset 0 0 0 2px rgba(255,255,255,0.03); transform-style: preserve-3d; }
    .tile.reveal { animation: flip .6s ease both; }
    @keyframes flip { 0% { transform: rotateX(0) } 49% { transform: rotateX(90deg) } 51% { transform: rotateX(90deg) } 100% { transform: rotateX(0) } }

    .tile.correct { background: var(--correct); color: #051006; box-shadow: none; }
    .tile.present { background: var(--present); color: #120a00; box-shadow: none; }
    .tile.absent  { background: var(--absent);  color: #c7d2fe; box-shadow: none; }
    .shake { animation: shake .4s both; }
    @keyframes shake { 10%, 90% { transform: translateX(-1px) } 20%, 80% { transform: translateX(2px) } 30%, 50%, 70% { transform: translateX(-4px) } 40%, 60% { transform: translateX(4px) } }

    .kbd { display: grid; grid-template-columns: repeat(20, 1fr); gap: 8px; margin-top: 14px; }
    .key { grid-column: span 2; display: grid; place-items: center; height: 44px; border-radius: 10px; background: #0f172a; color: #e5e7eb; box-shadow: inset 0 0 0 1px #1f2937; cursor: pointer; user-select: none; font-weight: 700; }
    .key.wide { grid-column: span 3; }
    .key.tiny { grid-column: span 1; }
    .key.correct { background: var(--correct); color: #051006; box-shadow: none; }
    .key.present { background: var(--present); color: #120a00; box-shadow: none; }
    .key.absent  { background: var(--absent);  color: #c7d2fe; box-shadow: none; }

    .help { margin: 8px 4px 0; font-size: 12px; color: #9ca3af; }
    .footer { margin-top: 10px; display: flex; gap: 8px; flex-wrap: wrap; }

    dialog { border: 0; border-radius: 16px; padding: 0; overflow: hidden; }
    .modal { background: #0b1222; color: #e5e7eb; min-width: min(480px, 95vw); }
    .modal header { padding: 14px 16px; background: #0f172a; }
    .modal .content { padding: 16px; }
    .stats { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
    .stat { background: #0f172a; border-radius: 12px; padding: 12px; text-align: center; }
    .stat h3 { margin: 0; font-size: 12px; color: #93c5fd; letter-spacing: .3px; }
    .stat p { margin: 6px 0 0; font-size: 22px; font-weight: 800; }

    @media (max-width: 420px) { .key { height: 42px; } .key.wide { grid-column: span 4; } }
  </style>
</head>
<body>
  <div class="app" id="app">
    <header>
      <h1>For You 💙 — Word Game</h1>
      <div class="topbar">
        <div class="pill" role="tablist" aria-label="Mode">
          <button class="active" id="dailyBtn" aria-selected="true">Daily</button>
          <button id="freeBtn" aria-selected="false">Free Play</button>
        </div>
        <button class="btn" id="shareBtn" title="Share result">Share</button>
        <button class="btn" id="statsBtn" title="Stats">Stats</button>
        <button class="btn" id="helpBtn" title="How to play">?</button>
      </div>
    </header>

    <main>
      <div id="board" class="board" aria-live="polite" aria-label="Game board"></div>
      <div class="help">Type on your keyboard or tap the keys below. 6 tries to guess the word.</div>
      <div id="keyboard" class="kbd" aria-label="On-screen keyboard"></div>
      <div class="footer">
        <button class="btn" id="newGameBtn">New Word</button>
        <button class="btn" id="customizeBtn">Customize Words</button>
      </div>
    </main>
  </div>

  <!-- Help Modal -->
  <dialog id="helpModal">
    <div class="modal">
      <header><strong>How to play</strong></header>
      <div class="content">
        <p>Guess the hidden word in 6 tries. Each guess must be a valid <em>five-letter</em> word. After each guess, the tiles will show how close your guess was:</p>
        <ul>
          <li><strong style="color: var(--correct)">Green</strong>: correct letter in the correct spot.</li>
          <li><strong style="color: var(--present)">Yellow</strong>: letter is in the word but wrong spot.</li>
          <li><strong style="color: #9ca3af">Gray</strong>: letter not in the word.</li>
        </ul>
        <p>Tap <strong>Customize Words</strong> to add your own special words (e.g., places you've been, nicknames, fav foods 🥰). In <strong>Daily</strong> mode, everyone gets the same word for the day. In <strong>Free Play</strong>, it's a new random word each game.</p>
        <p><button class="btn" id="closeHelp">Close</button></p>
      </div>
    </div>
  </dialog>

  <!-- Stats Modal -->
  <dialog id="statsModal">
    <div class="modal">
      <header><strong>Your stats</strong></header>
      <div class="content">
        <div class="stats">
          <div class="stat"><h3>Played</h3><p id="statPlayed">0</p></div>
          <div class="stat"><h3>Wins</h3><p id="statWins">0</p></div>
          <div class="stat"><h3>Win %</h3><p id="statWinRate">0</p></div>
          <div class="stat"><h3>Streak</h3><p id="statStreak">0</p></div>
        </div>
        <p style="margin-top: 12px"><button class="btn" id="closeStats">Close</button></p>
      </div>
    </div>
  </dialog>

  <!-- Customize Modal -->
  <dialog id="customModal">
    <div class="modal">
      <header><strong>Customize words</strong></header>
      <div class="content">
        <p>Paste or type your 5-letter words (one per line). We'll save them on this device.</p>
        <textarea id="customWords" style="width:100%; min-height: 160px; border-radius: 10px; padding: 10px; background:#0f172a; color:#e5e7eb; border:1px solid #1f2937"></textarea>
        <div style="display:flex; gap:8px; margin-top:10px; flex-wrap: wrap;">
          <button class="btn" id="saveCustom">Save & Use</button>
          <button class="btn" id="clearCustom">Clear</button>
          <button class="btn" id="closeCustom">Close</button>
        </div>
        <p class="help">Tip: include cute personal words like <em>mango</em>, <em>goaah</em>, <em>paani</em>, <em>chill</em>, etc. Only 5-letter words are accepted.</p>
      </div>
    </div>
  </dialog>

  <script>
    // ========= YOU CAN PERSONALIZE THESE DEFAULT WORDS =========
    // Replace this list with your special 5-letter words (lowercase only!)
    const DEFAULT_WORDS = [
      "benne","nonna"
    ];

    // ========= Core Settings =========
    const WORD_LEN = 5;
    const MAX_ROWS = 6;

    // Elements
    const boardEl = document.getElementById('board');
    const keyboardEl = document.getElementById('keyboard');
    const shareBtn = document.getElementById('shareBtn');
    const helpBtn = document.getElementById('helpBtn');
    const statsBtn = document.getElementById('statsBtn');
    const newGameBtn = document.getElementById('newGameBtn');
    const customizeBtn = document.getElementById('customizeBtn');
    const helpModal = document.getElementById('helpModal');
    const statsModal = document.getElementById('statsModal');
    const customModal = document.getElementById('customModal');
    const closeHelp = document.getElementById('closeHelp');
    const closeStats = document.getElementById('closeStats');
    const closeCustom = document.getElementById('closeCustom');
    const saveCustom = document.getElementById('saveCustom');
    const clearCustom = document.getElementById('clearCustom');
    const customWordsTA = document.getElementById('customWords');
    const dailyBtn = document.getElementById('dailyBtn');
    const freeBtn = document.getElementById('freeBtn');

    // Stats
    const statPlayed = document.getElementById('statPlayed');
    const statWins = document.getElementById('statWins');
    const statWinRate = document.getElementById('statWinRate');
    const statStreak = document.getElementById('statStreak');

    // State
    let mode = localStorage.getItem('mode') || 'daily';
    let words = getSavedWords();
    let answer = pickAnswer();
    let row = 0;
    let col = 0;
    let grid = Array.from({length: MAX_ROWS}, () => Array(WORD_LEN).fill(''));
    let statusGrid = Array.from({length: MAX_ROWS}, () => Array(WORD_LEN).fill('idle'));
    let gameOver = false;

    // Accessibility helpers
    function announce(msg) { boardEl.setAttribute('aria-label', msg); }

    // Build UI
    document.documentElement.style.setProperty('--word-len', WORD_LEN);
    document.documentElement.style.setProperty('--max-rows', MAX_ROWS);

    buildBoard();
    buildKeyboard();
    applyModeUI();
    updateStatsUI();

    // ======= Event wiring =======
    helpBtn.onclick = () => helpModal.showModal();
    closeHelp.onclick = () => helpModal.close();

    statsBtn.onclick = () => { updateStatsUI(); statsModal.showModal(); }
    closeStats.onclick = () => statsModal.close();

    customizeBtn.onclick = () => { customWordsTA.value = words.join('\n'); customModal.showModal(); };
    closeCustom.onclick = () => customModal.close();
    clearCustom.onclick = () => { localStorage.removeItem('customWords'); words = DEFAULT_WORDS; answer = pickAnswer(true); customWordsTA.value = ''; alert('Custom words cleared.'); };
    saveCustom.onclick = () => {
      const list = customWordsTA.value.split(/\n|,|;/).map(s => s.trim().toLowerCase()).filter(w => w.length === WORD_LEN && /^[a-z]+$/.test(w));
      if (list.length < 5) { alert('Please provide at least 5 valid 5-letter words.'); return; }
      localStorage.setItem('customWords', JSON.stringify(list));
      words = list; answer = pickAnswer(true); customModal.close(); alert('Saved! New word selected.');
    };

    dailyBtn.onclick = () => { mode = 'daily'; localStorage.setItem('mode', mode); applyModeUI(); resetGame(true); };
    freeBtn.onclick = () => { mode = 'free'; localStorage.setItem('mode', mode); applyModeUI(); resetGame(true); };

    newGameBtn.onclick = () => { if (mode === 'free') { resetGame(true); } else { alert('Daily mode gives one word per day. Switch to Free Play for a new word.'); } };

    shareBtn.onclick = shareResult;

    document.addEventListener('keydown', (e) => {
      if (gameOver) return;
      const key = e.key;
      if (key === 'Enter') submitRow();
      else if (key === 'Backspace' || key === 'Delete') backspace();
      else if (/^[a-zA-Z]$/.test(key)) typeLetter(key.toLowerCase());
    });

    // ======= Functions =======
    function getSavedWords() {
      try {
        const raw = localStorage.getItem('customWords');
        if (raw) {
          const parsed = JSON.parse(raw).filter(w => w.length === WORD_LEN && /^[a-z]+$/.test(w));
          if (parsed.length >= 5) return parsed;
        }
      } catch {}
      return DEFAULT_WORDS;
    }

    function applyModeUI() {
      dailyBtn.classList.toggle('active', mode === 'daily');
      freeBtn.classList.toggle('active', mode === 'free');
    }

    function dailySeed(date = new Date()) {
      // Use local date (user's device) for the daily word
      const y = date.getFullYear();
      const m = date.getMonth()+1; // 1-12
      const d = date.getDate();
      const s = `${y}-${m}-${d}`;
      let h = 0; for (let i=0;i<s.length;i++){ h = (h*31 + s.charCodeAt(i)) >>> 0; }
      return h;
    }

    function pickAnswer(forceNew=false) {
      if (mode === 'daily' && !forceNew) {
        const idx = dailySeed() % words.length;
        return words[idx];
      }
      // free play or forced new
      return words[Math.floor(Math.random()*words.length)];
    }

    function buildBoard() {
      boardEl.innerHTML = '';
      for (let r=0; r<MAX_ROWS; r++) {
        const rowEl = document.createElement('div');
        rowEl.className = 'row';
        for (let c=0; c<WORD_LEN; c++) {
          const t = document.createElement('div');
          t.className = 'tile';
          t.id = `tile-${r}-${c}`;
          rowEl.appendChild(t);
        }
        boardEl.appendChild(rowEl);
      }
    }

    function buildKeyboard() {
      keyboardEl.innerHTML = '';
      const rows = [
        'q w e r t y u i o p',
        'a s d f g h j k l',
        'Enter z x c v b n m ⌫'
      ];
      rows.forEach((line, i) => {
        const keys = line.split(' ');
        keys.forEach(k => {
          const keyEl = document.createElement('div');
          keyEl.className = 'key';
          keyEl.textContent = k.length === 1 ? k : (k === 'Enter' ? 'Enter' : '⌫');
          keyEl.dataset.key = k === '⌫' ? 'Backspace' : k;
          if (k === 'Enter') keyEl.classList.add('wide');
          if (k === '⌫') keyEl.classList.add('wide');
          keyboardEl.appendChild(keyEl);
        });
      });
      keyboardEl.addEventListener('click', (e) => {
        const keyEl = e.target.closest('.key');
        if (!keyEl || gameOver) return;
        const k = keyEl.dataset.key;
        if (k === 'Enter') submitRow();
        else if (k === 'Backspace') backspace();
        else typeLetter(k);
      });
    }

    function typeLetter(ch) {
      if (col >= WORD_LEN) return; // row full
      grid[row][col] = ch;
      renderTile(row, col, ch);
      col++;
    }

    function backspace() {
      if (col === 0) return;
      col--; grid[row][col] = ''; renderTile(row, col, '');
    }

    function submitRow() {
      if (col < WORD_LEN) { shakeRow(row); announce('Not enough letters'); return; }
      const guess = grid[row].join('');
      const result = scoreGuess(guess, answer);
      // animate reveal
      result.forEach((state, i) => {
        statusGrid[row][i] = state;
        setTimeout(() => {
          const tile = document.getElementById(`tile-${row}-${i}`);
          tile.classList.add('reveal');
          tile.classList.remove('correct','present','absent');
          tile.classList.add(state);
          updateKeyboard(guess[i], state);
        }, 120 * i);
      });

      const win = result.every(s => s === 'correct');
      setTimeout(() => {
        if (win) { onWin(guess); }
        else if (row === MAX_ROWS - 1) { onLose(); }
        else { row++; col = 0; }
      }, 120 * WORD_LEN + 50);
    }

    function scoreGuess(guess, ans) {
      // Two-pass scoring (like Wordle)
      const res = Array(WORD_LEN).fill('absent');
      const a = ans.split('');
      const g = guess.split('');
      const counts = {};
      a.forEach(ch => counts[ch] = (counts[ch]||0)+1);
      // first pass (correct)
      for (let i=0;i<WORD_LEN;i++) {
        if (g[i] === a[i]) { res[i] = 'correct'; counts[g[i]]--; }
      }
      // second pass (present)
      for (let i=0;i<WORD_LEN;i++) {
        if (res[i] === 'correct') continue;
        const ch = g[i];
        if (counts[ch] > 0) { res[i] = 'present'; counts[ch]--; }
      }
      return res;
    }

    function renderTile(r, c, ch) {
      const tile = document.getElementById(`tile-${r}-${c}`);
      tile.textContent = ch;
    }

    function updateKeyboard(ch, state) {
      const key = keyboardEl.querySelector(`[data-key="${ch}"]`);
      if (!key) return;
      const current = key.classList.contains('correct') ? 'correct' : key.classList.contains('present') ? 'present' : key.classList.contains('absent') ? 'absent' : 'idle';
      const priority = { idle: 0, absent: 1, present: 2, correct: 3 };
      if (priority[state] > priority[current]) {
        key.classList.remove('correct','present','absent');
        key.classList.add(state);
      }
    }

    function shakeRow(r) {
      const rowEl = boardEl.children[r];
      rowEl.classList.add('shake');
      setTimeout(() => rowEl.classList.remove('shake'), 400);
    }

    function onWin(guess) {
      gameOver = true; announce('You win!');
      bumpStats(true);
      setTimeout(() => alert(`Yay! You got it: ${guess.toUpperCase()} 💙`), 60);
    }

    function onLose() {
      gameOver = true; announce('You lose.');
      bumpStats(false);
      setTimeout(() => alert(`Nice try! The word was ${answer.toUpperCase()}.`), 60);
    }

    function bumpStats(win) {
      const s = JSON.parse(localStorage.getItem('stats') || '{"played":0,"wins":0,"streak":0,"lastWin":null}');
      s.played++; if (win) { s.wins++; s.streak++; s.lastWin = Date.now(); } else { s.streak = 0; }
      localStorage.setItem('stats', JSON.stringify(s));
      updateStatsUI();
    }

    function updateStatsUI() {
      const s = JSON.parse(localStorage.getItem('stats') || '{"played":0,"wins":0,"streak":0}');
      statPlayed.textContent = s.played || 0;
      statWins.textContent = s.wins || 0;
      const rate = s.played ? Math.round((s.wins / s.played) * 100) : 0;
      statWinRate.textContent = rate;
      statStreak.textContent = s.streak || 0;
    }

    function resetGame(selectNew=false) {
      if (selectNew) answer = pickAnswer(true);
      row = 0; col = 0; gameOver = false;
      grid = Array.from({length: MAX_ROWS}, () => Array(WORD_LEN).fill(''));
      statusGrid = Array.from({length: MAX_ROWS}, () => Array(WORD_LEN).fill('idle'));
      buildBoard();
      // Reset keyboard colors but keep best-known state per letter
      keyboardEl.querySelectorAll('.key').forEach(k => k.classList.remove('correct','present','absent'));
    }

    function shareResult() {
      const lines = [];
      for (let r=0; r<row + (gameOver ? 1 : 0); r++) {
        let line = '';
        for (let c=0; c<WORD_LEN; c++) {
          const s = statusGrid[r][c];
          line += s === 'correct' ? '🟩' : s === 'present' ? '🟨' : '⬛';
        }
        lines.push(line);
      }
      const title = `My Word Game ${mode === 'daily' ? 'Daily' : 'Free'} (${WORD_LEN}) — ${gameOver ? (statusGrid[row]?.every?.(x=>x==='correct') ? (row+1) : 'X') : '?' }/6`;
      const text = `${title}\n${lines.join('\n')}`;
      if (navigator.share) {
        navigator.share({ text }).catch(()=>copyToClipboard(text));
      } else {
        copyToClipboard(text);
      }
    }

    function copyToClipboard(text) {
      navigator.clipboard.writeText(text).then(() => alert('Copied result to clipboard!')).catch(() => {
        // fallback
        const ta = document.createElement('textarea');
        ta.value = text; document.body.appendChild(ta); ta.select();
        try { document.execCommand('copy'); alert('Copied result to clipboard!'); } catch { alert(text); }
        ta.remove();
      });
    }
  </script>
</body>
</html>

