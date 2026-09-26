# Ghhfhfz
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Stitch Quest RPG</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: url('background.gif') no-repeat center center fixed;
      background-size: cover;
      margin: 0;
      padding: 0;
    }
    .game-box {
      background: rgba(255, 255, 255, 0.65);
      backdrop-filter: blur(6px);
      width: 80%;
      margin: 40px auto;
      padding: 20px;
      border-radius: 12px;
      text-align: center;
    }
    .highlight-text {
      color: red;
      font-weight: bold;
    }
    .choices button {
      margin: 10px;
      padding: 12px 20px;
      font-size: 18px;
      cursor: pointer;
    }
    #scoreboard {
      margin-top: 20px;
      font-size: 20px;
    }
  </style>
</head>
<body>

<div class="game-box" id="game">
  <h1>Stitch Quest RPG</h1>
  <div id="scene"></div>
  <div class="choices" id="choices"></div>
  <div id="feedback"></div>
  <div id="scoreboard"></div>
</div>

<script>
  let ThreadPoints = 0;
  let GoldenNeedleBadge = 0;
  let level = 0;

  const levels = [
    {
      scene: "Torn Sock Monster Cave",
      choices: [
        { text: "Tape it", result: "fail", feedback: "Sock ripped more!" },
        { text: "Ignore it", result: "fail", feedback: "Classmates laugh at your torn sock!" },
        { text: "Sew it", result: "success", feedback: "You fixed the sock! +10 ThreadPoints", points: 10 }
      ]
    },
    {
      scene: "Missing Button Dragon Lair",
      choices: [
        { text: "Glue it", result: "fail", feedback: "Button falls again!" },
        { text: "Buy new uniform", result: "fail", feedback: "Too expensive!" },
        { text: "Sew it back", result: "success", feedback: "Button secured! +1 Golden Needle Badge", badge: 1 }
      ]
    },
    {
      scene: "Crochet Wizard Tower",
      choices: [
        { text: "Crochet a coaster", result: "success", feedback: "Nice coaster! +20 ThreadPoints", points: 20 },
        { text: "Crochet a sock", result: "fail", feedback: "Sock too small!" },
        { text: "Crochet a hat", result: "success", feedback: "Hat complete! Unlock ending." }
      ]
    }
  ];

  function startGame() {
    showLevel();
  }

  function showLevel() {
    if (level < levels.length) {
      const current = levels[level];
      document.getElementById("scene").innerHTML = `<h2>${current.scene}</h2>`;
      const choicesDiv = document.getElementById("choices");
      choicesDiv.innerHTML = "";
      current.choices.forEach(choice => {
        const btn = document.createElement("button");
        btn.textContent = choice.text;
        btn.onclick = () => handleChoice(choice);
        choicesDiv.appendChild(btn);
      });
    } else {
      endGame();
    }
  }

  function handleChoice(choice) {
    let feedback = choice.feedback;
    if (choice.result === "success") {
      if (choice.points) ThreadPoints += choice.points;
      if (choice.badge) GoldenNeedleBadge += choice.badge;
      level++;
    } else {
      // fail still moves forward
      level++;
    }
    document.getElementById("feedback").innerHTML = feedback;
    updateScoreboard();
    setTimeout(showLevel, 1500);
  }

  function updateScoreboard() {
    document.getElementById("scoreboard").innerHTML =
      `ThreadPoints: ${ThreadPoints} | GoldenNeedleBadge: ${GoldenNeedleBadge}`;
  }

  function endGame() {
    document.getElementById("scene").innerHTML = "<h2>Castle Scoreboard Hall</h2>";
    document.getElementById("choices").innerHTML =
      `<button onclick="restartGame()">Restart</button>`;
    document.getElementById("feedback").innerHTML =
      `Victory! You earned ${ThreadPoints} ThreadPoints and ${GoldenNeedleBadge} Golden Needle Badge(s)!`;
  }

  function restartGame() {
    ThreadPoints = 0;
    GoldenNeedleBadge = 0;
    level = 0;
    updateScoreboard();
    showLevel();
  }

  startGame();
</script>

</body>
</html>
