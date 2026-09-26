#   
  
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
      position: relative;  
      overflow: hidden;  
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
    /* Animations */  
    .bounce { animation: bounce 0.6s; }  
    @keyframes bounce {  
      0%, 100% { transform: translateY(0); }  
      50% { transform: translateY(-15px); }  
    }  
    .shake { animation: shake 0.6s; }  
    @keyframes shake {  
      0% { transform: translateX(0); }  
      25% { transform: translateX(-10px); }  
      50% { transform: translateX(10px); }  
      75% { transform: translateX(-10px); }  
      100% { transform: translateX(0); }  
    }  
    .flying-object {  
      position: absolute;  
      width: 50px;  
      height: 50px;  
      pointer-events: none;  
      animation: flyToMonster 1s forwards;  
    }  
    @keyframes flyToMonster {  
      from { top: 80%; left: 20%; opacity: 1; }  
      to { top: 30%; left: 50%; opacity: 0; }  
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
  
  const sounds = {  
    success: new Audio("sewing_success.mp3"),  
    failRip: new Audio("cloth_rip.mp3"),  
    failButton: new Audio("button_pop.mp3"),  
    sparkle: new Audio("sparkle.mp3")  
  };  
  
  const levels = [  
    {  
      scene: "Torn Sock Monster Cave",  
      choices: [  
        { text: "Tape it", result: "fail", feedback: "Sock ripped more!", sound: "failRip" },  
        { text: "Ignore it", result: "fail", feedback: "Classmates laugh at your torn sock!", sound: "failRip" },  
        { text: "Sew it", result: "success", feedback: "You fixed the sock! +10 ThreadPoints", points: 10, sound: "success", object: "thread.png" }  
      ]  
    },  
    {  
      scene: "Missing Button Dragon Lair",  
      choices: [  
        { text: "Glue it", result: "fail", feedback: "Button falls again!", sound: "failButton" },  
        { text: "Buy new uniform", result: "fail", feedback: "Too expensive!", sound: "failButton" },  
        { text: "Sew it back", result: "success", feedback: "Button secured! +1 Golden Needle Badge", badge: 1, sound: "success", object: "button.png" }  
      ]  
    },  
    {  
      scene: "Crochet Wizard Tower",  
      choices: [  
        { text: "Crochet a coaster", result: "success", feedback: "Nice coaster! +20 ThreadPoints", points: 20, sound: "success", object: "yarn.png" },  
        { text: "Crochet a sock", result: "fail", feedback: "Sock too small!", sound: "failRip" },  
        { text: "Crochet a hat", result: "success", feedback: "Hat complete! Unlock ending.", sound: "success", object: "hat.png" }  
      ]  
    }  
  ];  
  
  function startGame() {  
    showLevel();  
    updateScoreboard();  
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
    playSound(choice.sound);  
    if (choice.result === "success") {  
      animate("bounce");  
      if (choice.points) ThreadPoints += choice.points;  
      if (choice.badge) GoldenNeedleBadge += choice.badge;  
      if (choice.object) flyObject(choice.object);  
      level++;  
    } else {  
      animate("shake");  
      level++;  
    }  
    document.getElementById("feedback").innerHTML = choice.feedback;  
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
    playSound("sparkle");  
    animate("bounce");  
  }  
  
  function restartGame() {  
    ThreadPoints = 0;  
    GoldenNeedleBadge = 0;  
    level = 0;  
    updateScoreboard();  
    showLevel();  
  }  
  
  function playSound(soundKey) {  
    if (sounds[soundKey]) {  
      Object.values(sounds).forEach(s => { s.pause(); s.currentTime = 0; });  
      sounds[soundKey].play();  
    }  
  }  
  
  function animate(type) {  
    const gameBox = document.getElementById("game");  
    gameBox.classList.add(type);  
    setTimeout(() => gameBox.classList.remove(type), 600);  
  }  
  
  function flyObject(imgFile) {  
    const obj = document.createElement("img");  
    obj.src = imgFile;  
    obj.className = "flying-object";  
    document.getElementById("game").appendChild(obj);  
    setTimeout(() => obj.remove(), 1000);  
  }  
  
  startGame();  
</script>  
  
</body>  
</html>  
