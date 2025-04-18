<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Team Randomizer Pro</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      padding: 2rem;
      margin: 0;
      background: #0b0c10;
      color: white;
      overflow-x: hidden;
    }

    /* 🌌 Animated Star Background */
    body::before {
      content: "";
      position: fixed;
      top: 0; left: 0;
      width: 100%; height: 100%;
      background: url('https://www.transparenttextures.com/patterns/stardust.png');
      animation: stars 20s linear infinite;
      opacity: 0.2;
      z-index: -1;
    }

    @keyframes stars {
      0% { background-position: 0 0; }
      100% { background-position: 1000px 1000px; }
    }

    textarea {
      width: 100%;
      height: 150px;
      padding: 10px;
      font-size: 16px;
      margin-bottom: 10px;
      border-radius: 8px;
      border: none;
      resize: none;
    }

    select, button {
      padding: 10px;
      font-size: 16px;
      margin: 10px 5px;
      border-radius: 6px;
      border: none;
    }

    button {
      background-color: #28a745;
      color: white;
      cursor: pointer;
    }

    button:hover {
      background-color: #218838;
    }

    .teams {
      margin-top: 20px;
      display: grid;
      gap: 20px;
    }

    .team {
      background: #1f2833;
      padding: 10px;
      border-radius: 12px;
      animation: flipIn 0.8s ease-out forwards;
      transform: rotateY(90deg);
      opacity: 0;
      box-shadow: 0 0 15px rgba(255, 255, 255, 0.1);
    }

    .team h3 {
      margin: 0;
      margin-bottom: 8px;
      font-size: 20px;
    }

    @keyframes flipIn {
      to {
        transform: rotateY(0deg);
        opacity: 1;
      }
    }

    #countdown {
      font-size: 32px;
      color: #ffc107;
      font-weight: bold;
    }

    #shufflingMessage {
      margin-top: 20px;
      font-size: 20px;
    }

    #mascot {
      font-size: 50px;
      margin-bottom: 10px;
      animation: bounce 2s infinite ease-in-out;
    }

    @keyframes bounce {
      0%, 100% { transform: translateY(0); }
      50% { transform: translateY(-10px); }
    }
  </style>

  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
</head>
<body>

  <h1>✨ Team Randomizer</h1>
  <div id="mascot">🦄</div>

  <textarea id="nameInput" placeholder="Enter names, one per line..."></textarea><br>

  <label for="teamCount">Number of Teams:</label>
  <select id="teamCount">
    <option value="2">2</option>
    <option value="3">3</option>
    <option value="4">4</option>
    <option value="5">5</option>
  </select>

  <button onclick="startCountdown()">Create Teams</button>
  <button onclick="reshuffleTeams()" id="reshuffleBtn" style="display:none;">🔄 Shuffle Again</button>

  <div id="countdown"></div>
  <div id="shufflingMessage"></div>
  <div class="teams" id="teamsContainer"></div>

  <!-- 🔊 Sound Effects -->
  <audio id="soundShuffle" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_61cc1b24cd.mp3" preload="auto"></audio>
  <audio id="soundDrumroll" src="https://cdn.pixabay.com/download/audio/2022/02/23/audio_1817cb6924.mp3" preload="auto"></audio>
  <audio id="soundTada" src="https://cdn.pixabay.com/download/audio/2022/02/23/audio_82f1fbdf74.mp3" preload="auto"></audio>

  <script>
    const adjectives = ["Mighty", "Electric", "Funky", "Shadow", "Crimson", "Silent", "Golden", "Frozen", "Turbo", "Neon", "Quantum", "Brave"];
    const nouns = ["Wolves", "Tigers", "Koalas", "Wizards", "Ninjas", "Dragons", "Sharks", "Panthers", "Penguins", "Owls", "Llamas", "Foxes"];
    const mascots = ["🦄", "🐸", "🤖", "🐙", "🧸", "🐢", "🦙", "🐱‍👓", "🐧", "🐲", "🧞", "👾"];

    function setRandomMascot() {
      const mascot = mascots[Math.floor(Math.random() * mascots.length)];
      document.getElementById("mascot").textContent = mascot;
    }

    function generateTeamName() {
      const adj = adjectives[Math.floor(Math.random() * adjectives.length)];
      const noun = nouns[Math.floor(Math.random() * nouns.length)];
      return `${adj} ${noun}`;
    }

    function shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    }

    function emojiConfetti() {
      const emojis = ["🎉", "✨", "🎊", "💥", "🌟", "🪩"];
      const end = Date.now() + 1000;
      (function frame() {
        confetti({
          particleCount: 6,
          angle: 60 + Math.random() * 60,
          spread: 55,
          origin: { y: 0.6 },
          shapes: ["text"],
          scalar: 1.2,
          ticks: 200,
          gravity: 0.6,
          emoji: emojis[Math.floor(Math.random() * emojis.length)]
        });
        if (Date.now() < end) {
          requestAnimationFrame(frame);
        }
      })();
    }

    function startCountdown() {
      const namesText = document.getElementById("nameInput").value.trim();
      const names = namesText.split("\n").map(n => n.trim()).filter(n => n);
      const teamCount = parseInt(document.getElementById("teamCount").value);

      if (names.length < teamCount) {
        alert("Not enough names for that many teams!");
        return;
      }

      setRandomMascot();
      document.getElementById("teamsContainer").innerHTML = "";
      document.getElementById("shufflingMessage").textContent = "";
      document.getElementById("reshuffleBtn").style.display = "none";

      document.getElementById("soundDrumroll").play();

      let timeLeft = 3;
      const countdownEl = document.getElementById("countdown");
      countdownEl.textContent = `Revealing in ${timeLeft}...`;

      const interval = setInterval(() => {
        timeLeft--;
        if (timeLeft > 0) {
          countdownEl.textContent = `Revealing in ${timeLeft}...`;
        } else {
          clearInterval(interval);
          countdownEl.textContent = "";
          createTeams(names, teamCount);
        }
      }, 1000);
    }

    function createTeams(names, teamCount) {
      shuffle(names);
      document.getElementById("soundShuffle").play();

      const container = document.getElementById("teamsContainer");
      const teams = Array.from({ length: teamCount }, () => []);
      names.forEach((name, i) => {
        teams[i % teamCount].push(name);
      });

      document.getElementById("shufflingMessage").textContent = "✨ Teams are ready!";
      setTimeout(() => {
        emojiConfetti();
        document.getElementById("soundTada").play();
      }, 200);

      teams.forEach((team, index) => {
        const teamDiv = document.createElement("div");
        teamDiv.className = "team";
        teamDiv.style.animationDelay = `${index * 0.3}s`;
        teamDiv.innerHTML = `
          <h3>🏆 ${generateTeamName()}</h3>
          <ul>${team.map(n => `<li>👤 ${n}</li>`).join("")}</ul>
        `;
        container.appendChild(teamDiv);
      });

      document.getElementById("reshuffleBtn").style.display = "inline-block";
    }

    function reshuffleTeams() {
      startCountdown();
    }

    window.onload = setRandomMascot;
  </script>

</body>
</html>
