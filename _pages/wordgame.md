---
layout: none
permalink: /wordgame/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wordle Clone</title>
    <style>
        body {
            font-family: 'Helvetica Neue', Arial, sans-serif;
            background: #121213;
            color: #ffffff;
            margin: 0;
            padding: 20px;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .header {
            text-align: center;
            border-bottom: 1px solid #3a3a3c;
            padding-bottom: 20px;
            margin-bottom: 30px;
            width: 100%;
            max-width: 500px;
        }

        h1 {
            font-size: 2.5rem;
            font-weight: 700;
            margin: 0;
            letter-spacing: 0.2rem;
        }

        .game-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .grid {
            display: grid;
            grid-template-rows: repeat(6, 1fr);
            gap: 5px;
            padding: 10px;
        }

        .row {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 5px;
        }

        .tile {
            width: 62px;
            height: 62px;
            border: 2px solid #3a3a3c;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2rem;
            font-weight: bold;
            text-transform: uppercase;
            background: #121213;
            color: #ffffff;
            transition: all 0.3s ease;
        }

        .tile.filled {
            border-color: #565758;
            animation: pop 0.1s ease-in-out;
        }

        .tile.correct {
            background: #538d4e;
            border-color: #538d4e;
        }

        .tile.present {
            background: #b59f3b;
            border-color: #b59f3b;
        }

        .tile.absent {
            background: #3a3a3c;
            border-color: #3a3a3c;
        }

        @keyframes pop {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }

        .keyboard {
            margin-top: 30px;
        }

        .keyboard-row {
            display: flex;
            justify-content: center;
            margin-bottom: 8px;
            gap: 6px;
        }

        .key {
            background: #818384;
            color: #ffffff;
            border: none;
            padding: 14px 12px;
            border-radius: 4px;
            font-size: 0.8rem;
            font-weight: bold;
            cursor: pointer;
            text-transform: uppercase;
            min-width: 43px;
            transition: background-color 0.1s ease;
        }

        .key:hover {
            background: #9ca3a5;
        }

        .key.wide {
            min-width: 65px;
            font-size: 0.7rem;
        }

        .key.correct {
            background: #538d4e;
        }

        .key.present {
            background: #b59f3b;
        }

        .key.absent {
            background: #3a3a3c;
        }

        .message {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: #ffffff;
            color: #121213;
            padding: 12px 24px;
            border-radius: 4px;
            font-weight: bold;
            display: none;
            z-index: 1000;
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
        }

        .message.show {
            display: block;
            animation: slideDown 0.3s ease;
        }

        @keyframes slideDown {
            from { transform: translateX(-50%) translateY(-20px); opacity: 0; }
            to { transform: translateX(-50%) translateY(0); opacity: 1; }
        }

        .admin-popup {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .admin-popup.show {
            display: flex;
        }

        .admin-content {
            background: #1f1f23;
            padding: 30px;
            border-radius: 12px;
            border: 1px solid #3a3a3c;
            min-width: 300px;
            text-align: center;
        }

        .admin-input {
            background: #2d2d30;
            border: 1px solid #3a3a3c;
            color: #ffffff;
            padding: 12px 16px;
            border-radius: 6px;
            margin: 10px 0;
            width: 100%;
            box-sizing: border-box;
            font-size: 16px;
        }

        .admin-button {
            background: #538d4e;
            color: #ffffff;
            border: none;
            padding: 12px 24px;
            border-radius: 6px;
            cursor: pointer;
            margin: 10px 5px;
            font-weight: bold;
            font-size: 16px;
            transition: background-color 0.2s ease;
        }

        .admin-button:hover {
            background: #6aaa64;
        }

        .admin-button.cancel {
            background: #818384;
        }

        .admin-button.cancel:hover {
            background: #9ca3a5;
        }

        .add-word-btn {
            background: #818384;
            color: #ffffff;
            border: none;
            padding: 12px 24px;
            border-radius: 4px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 20px;
            transition: background-color 0.1s ease;
        }

        .add-word-btn:hover {
            background: #9ca3a5;
        }

        .stats {
            display: none;
        }

        .new-game-btn {
            background: #818384;
            color: #ffffff;
            border: none;
            padding: 12px 24px;
            border-radius: 4px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 20px;
            transition: background-color 0.1s ease;
        }

        .new-game-btn:hover {
            background: #9ca3a5;
        }

        @media (max-width: 480px) {
            .tile {
                width: 50px;
                height: 50px;
                font-size: 1.5rem;
            }
            
            .key {
                padding: 12px 8px;
                min-width: 35px;
            }
            
            .key.wide {
                min-width: 55px;
            }
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>MUMUORDLE 💖</h1>
    </div>

    <div class="admin-popup" id="adminPopup">
        <div class="admin-content">
            <h3 style="margin-top: 0; color: #ffffff;">Add New Word</h3>
            <input type="password" id="adminPassword" class="admin-input" placeholder="Enter password">
            <input type="text" id="newWord" class="admin-input" placeholder="Enter 5-letter word" maxlength="5">
            <div>
                <button onclick="addWord()" class="admin-button">Add Word</button>
                <button onclick="closeAdminPopup()" class="admin-button cancel">Cancel</button>
            </div>
        </div>
    </div>

    <div class="message" id="message"></div>

    <div class="game-container">
        <div class="grid" id="grid"></div>
        
        <div class="keyboard">
            <div class="keyboard-row">
                <button class="key" onclick="handleKeyPress('Q')">Q</button>
                <button class="key" onclick="handleKeyPress('W')">W</button>
                <button class="key" onclick="handleKeyPress('E')">E</button>
                <button class="key" onclick="handleKeyPress('R')">R</button>
                <button class="key" onclick="handleKeyPress('T')">T</button>
                <button class="key" onclick="handleKeyPress('Y')">Y</button>
                <button class="key" onclick="handleKeyPress('U')">U</button>
                <button class="key" onclick="handleKeyPress('I')">I</button>
                <button class="key" onclick="handleKeyPress('O')">O</button>
                <button class="key" onclick="handleKeyPress('P')">P</button>
            </div>
            <div class="keyboard-row">
                <button class="key" onclick="handleKeyPress('A')">A</button>
                <button class="key" onclick="handleKeyPress('S')">S</button>
                <button class="key" onclick="handleKeyPress('D')">D</button>
                <button class="key" onclick="handleKeyPress('F')">F</button>
                <button class="key" onclick="handleKeyPress('G')">G</button>
                <button class="key" onclick="handleKeyPress('H')">H</button>
                <button class="key" onclick="handleKeyPress('J')">J</button>
                <button class="key" onclick="handleKeyPress('K')">K</button>
                <button class="key" onclick="handleKeyPress('L')">L</button>
            </div>
            <div class="keyboard-row">
                <button class="key wide" onclick="submitGuess()">ENTER</button>
                <button class="key" onclick="handleKeyPress('Z')">Z</button>
                <button class="key" onclick="handleKeyPress('X')">X</button>
                <button class="key" onclick="handleKeyPress('C')">C</button>
                <button class="key" onclick="handleKeyPress('V')">V</button>
                <button class="key" onclick="handleKeyPress('B')">B</button>
                <button class="key" onclick="handleKeyPress('N')">N</button>
                <button class="key" onclick="handleKeyPress('M')">M</button>
                <button class="key wide" onclick="deleteLetter()">⌫</button>
            </div>
        </div>

        <button class="new-game-btn" onclick="newGame()">NEW GAME</button>
        <button class="add-word-btn" onclick="openAdminPopup()">ADD WORDS</button>

        <div class="stats">
            <div>Games Played: <span id="gamesPlayed">0</span> | Games Won: <span id="gamesWon">0</span></div>
        </div>
    </div>

    <script>
        // Game state
        let currentRow = 0;
        let currentCol = 0;
        let gameOver = false;
        let currentWord = '';
        let guesses = [];
        
        // Admin password
        const ADMIN_PASSWORD = 'squardle'; // Change this to your desired password
        
        // Default word list - you can add more words through the admin panel
        let wordList = [
            'BENNE', 'NONNA', 'YOUME'
        ];

        // Initialize game
        function initGame() {
            createGrid();
            selectRandomWord();
            loadStats();
            // Load custom words from memory
            const savedWords = getSavedWords();
            if (savedWords.length > 0) {
                wordList = [...wordList, ...savedWords];
            }
        }

        function createGrid() {
            const grid = document.getElementById('grid');
            grid.innerHTML = '';
            
            for (let row = 0; row < 6; row++) {
                const rowDiv = document.createElement('div');
                rowDiv.className = 'row';
                
                for (let col = 0; col < 5; col++) {
                    const tile = document.createElement('div');
                    tile.className = 'tile';
                    tile.id = `tile-${row}-${col}`;
                    rowDiv.appendChild(tile);
                }
                
                grid.appendChild(rowDiv);
            }
        }

        function selectRandomWord() {
            currentWord = wordList[Math.floor(Math.random() * wordList.length)];
            console.log('Current word:', currentWord); // For testing - remove in production
        }

        function handleKeyPress(letter) {
            if (gameOver || currentCol >= 5) return;
            
            const tile = document.getElementById(`tile-${currentRow}-${currentCol}`);
            tile.textContent = letter;
            tile.classList.add('filled');
            currentCol++;
        }

        function deleteLetter() {
            if (gameOver || currentCol <= 0) return;
            
            currentCol--;
            const tile = document.getElementById(`tile-${currentRow}-${currentCol}`);
            tile.textContent = '';
            tile.classList.remove('filled');
        }

        function submitGuess() {
            if (gameOver || currentCol < 5) return;
            
            // Get current guess
            let guess = '';
            for (let i = 0; i < 5; i++) {
                guess += document.getElementById(`tile-${currentRow}-${i}`).textContent;
            }
            
            guesses.push(guess);
            
            // Check guess against current word
            const result = checkGuess(guess, currentWord);
            animateRow(currentRow, result);
            updateKeyboard(guess, result);
            
            // Check if won or lost
            if (guess === currentWord) {
                gameOver = true;
                setTimeout(() => {
                    showMessage('🎉 Congratulations! You won!');
                    updateStats(true);
                }, 1500);
            } else if (currentRow >= 5) {
                gameOver = true;
                setTimeout(() => {
                    showMessage(`Game over! The word was: ${currentWord}`);
                    updateStats(false);
                }, 1500);
            }
            
            currentRow++;
            currentCol = 0;
        }

        function checkGuess(guess, word) {
            const result = [];
            const wordArray = word.split('');
            const guessArray = guess.split('');
            
            // Mark correct letters first
            for (let i = 0; i < 5; i++) {
                if (guessArray[i] === wordArray[i]) {
                    result[i] = 'correct';
                    wordArray[i] = null; // Mark as used
                }
            }
            
            // Mark present and absent letters
            for (let i = 0; i < 5; i++) {
                if (result[i]) continue; // Already marked as correct
                
                const letterIndex = wordArray.indexOf(guessArray[i]);
                if (letterIndex !== -1) {
                    result[i] = 'present';
                    wordArray[letterIndex] = null; // Mark as used
                } else {
                    result[i] = 'absent';
                }
            }
            
            return result;
        }

        function animateRow(row, result) {
            for (let i = 0; i < 5; i++) {
                setTimeout(() => {
                    const tile = document.getElementById(`tile-${row}-${i}`);
                    tile.classList.add(result[i]);
                }, i * 100);
            }
        }

        function updateKeyboard(guess, result) {
            for (let i = 0; i < 5; i++) {
                const letter = guess[i];
                const keys = document.querySelectorAll(`.key`);
                
                keys.forEach(key => {
                    if (key.textContent === letter) {
                        // Only update if not already marked as correct
                        if (!key.classList.contains('correct')) {
                            key.classList.remove('present', 'absent');
                            key.classList.add(result[i]);
                        }
                    }
                });
            }
        }

        function showMessage(text) {
            const message = document.getElementById('message');
            message.textContent = text;
            message.classList.add('show');
            setTimeout(() => {
                message.classList.remove('show');
            }, 3000);
        }

        function newGame() {
            currentRow = 0;
            currentCol = 0;
            gameOver = false;
            guesses = [];
            
            // Reset grid
            const tiles = document.querySelectorAll('.tile');
            tiles.forEach(tile => {
                tile.textContent = '';
                tile.className = 'tile';
            });
            
            // Reset keyboard
            const keys = document.querySelectorAll('.key');
            keys.forEach(key => {
                key.classList.remove('correct', 'present', 'absent');
            });
            
            // Select new word
            selectRandomWord();
        }

        // Admin popup functions
        function openAdminPopup() {
            document.getElementById('adminPopup').classList.add('show');
        }

        function closeAdminPopup() {
            document.getElementById('adminPopup').classList.remove('show');
            // Clear inputs
            document.getElementById('adminPassword').value = '';
            document.getElementById('newWord').value = '';
        }

        // Admin functions
        function addWord() {
            const password = document.getElementById('adminPassword').value;
            const newWord = document.getElementById('newWord').value.toUpperCase();
            
            if (password !== ADMIN_PASSWORD) {
                showMessage('❌ Incorrect password');
                return;
            }
            
            if (newWord.length !== 5 || !/^[A-Z]+$/.test(newWord)) {
                showMessage('❌ Word must be exactly 5 letters');
                return;
            }
            
            if (wordList.includes(newWord)) {
                showMessage('❌ Word already exists');
                return;
            }
            
            wordList.push(newWord);
            saveCustomWords();
            showMessage('✅ Word added successfully');
            closeAdminPopup();
        }

        function saveCustomWords() {
            const customWords = wordList.slice(15); // Save only custom words (after the initial 15)
            try {
                // Since we can't use localStorage, we'll keep words in memory for the session
                // In a real implementation, you'd need a backend to persist these
                console.log('Custom words saved:', customWords);
            } catch (e) {
                console.log('Could not save words');
            }
        }

        function getSavedWords() {
            try {
                // In a real implementation, you'd load from a backend
                return [];
            } catch (e) {
                return [];
            }
        }

        function loadStats() {
            // For now, just show 0s since we can't persist stats
            document.getElementById('gamesPlayed').textContent = '0';
            document.getElementById('gamesWon').textContent = '0';
        }

        function updateStats(won) {
            const gamesPlayedElement = document.getElementById('gamesPlayed');
            const gamesWonElement = document.getElementById('gamesWon');
            
            let gamesPlayed = parseInt(gamesPlayedElement.textContent) + 1;
            let gamesWon = parseInt(gamesWonElement.textContent) + (won ? 1 : 0);
            
            gamesPlayedElement.textContent = gamesPlayed;
            gamesWonElement.textContent = gamesWon;
        }

        // Keyboard event listener
        document.addEventListener('keydown', (e) => {
            // Don't intercept keys when popup is open or when typing in input fields
            const popupOpen = document.getElementById('adminPopup').classList.contains('show');
            const isInputField = e.target.tagName === 'INPUT';
            
            if (gameOver || popupOpen || isInputField) return;
            
            const key = e.key.toUpperCase();
            
            if (key === 'ENTER') {
                e.preventDefault();
                submitGuess();
            } else if (key === 'BACKSPACE') {
                e.preventDefault();
                deleteLetter();
            } else if (/^[A-Z]$/.test(key)) {
                e.preventDefault();
                handleKeyPress(key);
            }
        });

        // Initialize game when page loads
        initGame();
    </script>
</body>
</html>



