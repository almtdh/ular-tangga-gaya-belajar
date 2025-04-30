# ular-tangga-gaya-belajar
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ular Tangga Gaya Belajar</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f4f4f4; text-align: center; padding: 20px; }
    h1 { color: #333; }
    #board { display: grid; grid-template-columns: repeat(6, 60px); gap: 5px; margin: 20px auto; width: max-content; }
    .cell { width: 60px; height: 60px; background: #fff; border: 1px solid #ccc; display: flex; align-items: center; justify-content: center; font-size: 14px; position: relative; }
    .player { color: white; border-radius: 50%; padding: 5px 10px; position: absolute; bottom: 5px; font-size: 12px; }
    .P1 { background: red; right: 5px; }
    .P2 { background: blue; left: 5px; }
    .P3 { background: green; top: 5px; right: 5px; }
    .P4 { background: orange; top: 5px; left: 5px; }
    button { padding: 10px 20px; font-size: 16px; cursor: pointer; margin: 10px; }
    #log { margin-top: 20px; min-height: 100px; background: #fff; padding: 10px; border-radius: 10px; }
    #scoreboard { margin-top: 20px; font-weight: bold; }
    img.question-img { width: 100px; height: auto; margin-top: 10px; }
  </style>
</head>
<body>
  <h1>Game Ular Tangga Gaya Belajar</h1>
  <div id="board"></div>
  <button onclick="rollDice()">Lempar Dadu 🎲</button>
  <div id="log"></div>
  <div id="scoreboard">
    Skor P1: <span id="score1">0</span> |
    Skor P2: <span id="score2">0</span> |
    Skor P3: <span id="score3">0</span> |
    Skor P4: <span id="score4">0</span><br>
    <strong>Giliran: <span id="turn">P1</span></strong>
  </div>

  <audio id="moveSound" src="https://www.soundjay.com/button/beep-07.wav" preload="auto"></audio>
  <audio id="finishSound" src="https://www.soundjay.com/human/sounds/applause-01.mp3" preload="auto"></audio>

  <script>
    const board = document.getElementById("board");
    const log = document.getElementById("log");
    const scoreDisplays = {
      P1: document.getElementById("score1"),
      P2: document.getElementById("score2"),
      P3: document.getElementById("score3"),
      P4: document.getElementById("score4")
    };
    const turnDisplay = document.getElementById("turn");
    const moveSound = document.getElementById("moveSound");
    const finishSound = document.getElementById("finishSound");

    const boardSize = 30;
    let positions = { P1: 1, P2: 1, P3: 1, P4: 1 };
    let scores = { P1: 0, P2: 0, P3: 0, P4: 0 };
    let playerOrder = ["P1", "P2", "P3", "P4"];
    let currentPlayerIndex = 0;

    const questions = [
      { text: "Apa gaya belajar yang suka gambar dan warna?", img: "https://i.imgur.com/1l6gnMp.png" },
      { text: "Peragakan belajar ala kinestetik!", img: "https://i.imgur.com/T0K9jO3.png" },
      { text: "Apa ciri gaya belajar auditori?", img: "https://i.imgur.com/SqKrZTC.png" },
      { text: "Gaya belajar mana suka praktik langsung?", img: "https://i.imgur.com/DFVdJt7.png" },
      { text: "Jika kamu suka mencatat saat belajar, kamu termasuk...?", img: "https://i.imgur.com/67mpmnH.png" },
      { text: "Apa strategi belajar untuk visual learner?", img: "https://i.imgur.com/6rTfzkL.png" },
      { text: "Tantangan: Ulangi kalimat yang guru ucapkan tanpa mencatat.", img: "https://i.imgur.com/C90DH0s.png" },
      { text: "Gaya belajar kinestetik cocok dengan aktivitas apa?", img: "https://i.imgur.com/jXzI5oD.png" },
      { text: "Apa keuntungan mengetahui gaya belajar kita?", img: "https://i.imgur.com/8k4c26u.png" }
    ];

    const ladders = { 5: 10, 11: 16, 20: 26 };
    const snakes = { 14: 7, 22: 13, 28: 19 };
    const questionSpots = [3, 6, 9, 12, 15, 18, 21, 24, 27];

    function createBoard() {
      for (let i = boardSize; i >= 1; i--) {
        const cell = document.createElement("div");
        cell.className = "cell";
        cell.id = `cell-${i}`;
        cell.innerHTML = i;
        board.appendChild(cell);
      }
      updatePlayers();
    }

    function updatePlayers() {
      document.querySelectorAll(".player").forEach(p => p.remove());
      for (let player in positions) {
        const cell = document.getElementById(`cell-${positions[player]}`);
        const playerDiv = document.createElement("div");
        playerDiv.className = `player ${player}`;
        playerDiv.innerText = player;
        cell.appendChild(playerDiv);
      }
    }

    function rollDice() {
      let currentPlayer = playerOrder[currentPlayerIndex];
      const roll = Math.floor(Math.random() * 6) + 1;
      let nextPos = positions[currentPlayer] + roll;
      if (nextPos > boardSize) nextPos = boardSize;

      log.innerHTML = `<p>${currentPlayer} melempar angka <strong>${roll}</strong></p>`;
      positions[currentPlayer] = nextPos;
      moveSound.play();

      if (ladders[nextPos]) {
        log.innerHTML += `<p>🎉 ${currentPlayer} naik tangga ke ${ladders[nextPos]}</p>`;
        positions[currentPlayer] = ladders[nextPos];
        scores[currentPlayer] += 2;
      } else if (snakes[nextPos]) {
        log.innerHTML += `<p>😱 ${currentPlayer} digigit ular! Turun ke ${snakes[nextPos]}</p>`;
        positions[currentPlayer] = snakes[nextPos];
        scores[currentPlayer] -= 1;
      } else {
        scores[currentPlayer] += 1;
      }

      updatePlayers();
      for (let p of playerOrder) {
        scoreDisplays[p].innerText = scores[p];
      }

      if (questionSpots.includes(positions[currentPlayer])) {
        const q = questions[Math.floor(Math.random() * questions.length)];
        log.innerHTML += `<p><strong>Pertanyaan:</strong> ${q.text}</p><img src="${q.img}" class="question-img">`;
      }

      if (positions[currentPlayer] === boardSize) {
        finishSound.play();
        log.innerHTML += `<p>🎉 ${currentPlayer} menang!</p>`;
      } else {
        currentPlayerIndex = (currentPlayerIndex + 1) % playerOrder.length;
        turnDisplay.innerText = playerOrder[currentPlayerIndex];
      }
    }

    createBoard();
  </script>
</body>
</html>
