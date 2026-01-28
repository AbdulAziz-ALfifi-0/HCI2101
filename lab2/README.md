<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لعبة XO المتقدمة - عبد العزيز الفيفي</title>
    <style>
        :root {
            --primary-color: #4a90e2;
            --secondary-color: #f39c12;
            --bg-color: #f0f2f5;
            --text-color: #2c3e50;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .container {
            background: white;
            padding: 2rem;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
            text-align: center;
            width: 100%;
            max-width: 400px;
        }

        h1 { color: var(--primary-color); }

        #start-screen, #game-screen {
            display: none;
        }

        #start-screen.active, #game-screen.active {
            display: block;
        }

        .input-group {
            margin-bottom: 15px;
            text-align: right;
        }

        input {
            width: 100%;
            padding: 10px;
            margin-top: 5px;
            border: 1px solid #ddd;
            border-radius: 5px;
            box-sizing: border-box;
        }

        button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1rem;
            transition: background 0.3s;
            width: 100%;
            margin-top: 10px;
        }

        button:hover { background-color: #357abd; }

        .board {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin: 20px 0;
        }

        .cell {
            aspect-ratio: 1/1;
            background: #eee;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.5rem;
            font-weight: bold;
            cursor: pointer;
            border-radius: 8px;
            transition: background 0.2s;
        }

        .cell:hover { background: #e0e0e0; }
        .cell.x { color: #e74c3c; }
        .cell.o { color: var(--primary-color); }

        .status {
            margin-bottom: 15px;
            font-size: 1.2rem;
            font-weight: bold;
        }

        footer {
            margin-top: auto;
            padding: 20px;
            text-align: center;
            font-size: 0.9rem;
            border-top: 2px solid var(--primary-color);
            width: 100%;
            background: #fff;
            position: relative;
            bottom: 0;
        }
    </style>
</head>
<body>

    <div class="container">
        <div id="start-screen" class="active">
            <h1>لعبة XO الذكية</h1>
            <div class="input-group">
                <label>اسم اللاعب الأول (X):</label>
                <input type="text" id="p1-name" placeholder="أدخل الاسم">
            </div>
            <div class="input-group" id="p2-input">
                <label>اسم اللاعب الثاني (O):</label>
                <input type="text" id="p2-name" placeholder="أدخل الاسم أو اتركه للعب ضد الكمبيوتر">
            </div>
            <button onclick="startGame()">بدء اللعب</button>
        </div>

        <div id="game-screen">
            <div class="status" id="status-text">دور اللاعب: </div>
            <div class="board" id="board">
                <div class="cell" onclick="handleCellClick(0)"></div>
                <div class="cell" onclick="handleCellClick(1)"></div>
                <div class="cell" onclick="handleCellClick(2)"></div>
                <div class="cell" onclick="handleCellClick(3)"></div>
                <div class="cell" onclick="handleCellClick(4)"></div>
                <div class="cell" onclick="handleCellClick(5)"></div>
                <div class="cell" onclick="handleCellClick(6)"></div>
                <div class="cell" onclick="handleCellClick(7)"></div>
                <div class="cell" onclick="handleCellClick(8)"></div>
            </div>
            <button onclick="resetGame()">إعادة اللعب</button>
            <button style="background:#95a5a6; margin-top:5px;" onclick="location.reload()">العودة للرئيسية</button>
        </div>
    </div>

    <footer>
        <p><strong>Student Name:</strong> ABDULAZIZ ABDULLAH HASAN ALFIFI (عبدالعزيز عبدالله حسن الفيفي)</p>
        <p><strong>Student ID:</strong> 446001042</p>
        <p><strong>Supervised by:</strong> Mohammed Ahmed Jabali</p>
    </footer>

    <script>
        let currentPlayer = 'X';
        let gameState = ["", "", "", "", "", "", "", "", ""];
        let gameActive = true;
        let player1, player2;
        let isVsAI = false;

        function startGame() {
            player1 = document.getElementById('p1-name').value || "اللاعب 1";
            player2 = document.getElementById('p2-name').value;
            
            if (!player2) {
                player2 = "الكمبيوتر";
                isVsAI = true;
            }

            document.getElementById('start-screen').classList.remove('active');
            document.getElementById('game-screen').classList.add('active');
            updateStatus();
        }

        function handleCellClick(index) {
            if (gameState[index] !== "" || !gameActive) return;

            makeMove(index, 'X');
            
            if (gameActive && isVsAI) {
                setTimeout(computerMove, 500);
            } else if (gameActive) {
                currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
                updateStatus();
            }
        }

        function makeMove(index, player) {
            gameState[index] = player;
            const cells = document.querySelectorAll('.cell');
            cells[index].innerText = player;
            cells[index].classList.add(player.toLowerCase());
            
            checkResult();
        }

        function computerMove() {
            let emptyCells = gameState.map((v, i) => v === "" ? i : null).filter(v => v !== null);
            if (emptyCells.length > 0 && gameActive) {
                let randomIndex = emptyCells[Math.floor(Math.random() * emptyCells.length)];
                makeMove(randomIndex, 'O');
                if (gameActive) {
                    currentPlayer = 'X';
                    updateStatus();
                }
            }
        }

        function updateStatus() {
            const name = currentPlayer === 'X' ? player1 : player2;
            document.getElementById('status-text').innerText = `دور: ${name} (${currentPlayer})`;
        }

        const winningConditions = [
            [0, 1, 2], [3, 4, 5], [6, 7, 8],
            [0, 3, 6], [1, 4, 7], [2, 5, 8],
            [0, 4, 8], [2, 4, 6]
        ];

        function checkResult() {
            let roundWon = false;
            for (let condition of winningConditions) {
                let a = gameState[condition[0]];
                let b = gameState[condition[1]];
                let c = gameState[condition[2]];
                if (a === '' || b === '' || c === '') continue;
                if (a === b && b === c) {
                    roundWon = true;
                    break;
                }
            }

            if (roundWon) {
                const winner = currentPlayer === 'X' ? player1 : player2;
                document.getElementById('status-text').innerText = `الفائز هو: ${winner}! 🎉`;
                gameActive = false;
                return;
            }

            if (!gameState.includes("")) {
                document.getElementById('status-text').innerText = "تعادل!";
                gameActive = false;
            }
        }

        function resetGame() {
            currentPlayer = 'X';
            gameState = ["", "", "", "", "", "", "", "", ""];
            gameActive = true;
            document.querySelectorAll('.cell').forEach(cell => {
                cell.innerText = "";
                cell.classList.remove('x', 'o');
            });
            updateStatus();
        }
    </script>
</body>
</html>
