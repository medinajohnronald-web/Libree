<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Libre</title>
  <style>
    body {
      font-family: "Georgia", serif;
      background: #fff0f5;
      color: #333;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      overflow: hidden;
    }
    .card {
      background: white;
      padding: 30px;
      border-radius: 15px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.2);
      max-width: 600px;
      text-align: center;
      display: none;
      position: relative;
    }
    .card.active {
      display: block;
      animation: fadeIn 0.6s ease-in-out;
    }
    h1 {
      color: cyan;
      margin-bottom: 20px;
    }
    p {
      font-size: 18px;
      line-height: 1.6;
    }
    button {
      background: cyan;
      color: white;
      border: none;
      padding: 10px 20px;
      margin: 10px;
      border-radius: 8px;
      cursor: pointer;
      font-size: 16px;
      position: relative;
      transition: transform 0.3s ease, box-shadow 0.3s ease, font-size 0.3s ease;
    }
    button:hover {
      background: cyan;
    }
    .yesBtn:hover {
      transform: scale(1.2);
      box-shadow: 0 0 15px #ff69b4;
    }
    input {
      padding: 10px;
      font-size: 16px;
      border: 2px solid #c71585;
      border-radius: 8px;
      width: 70%;
    }
    @keyframes fadeIn {
      from {opacity: 0; transform: translateY(20px);}
      to {opacity: 1; transform: translateY(0);}
    }
    .heart {
      position: absolute;
      color: #ff4d6d;
      font-size: 24px;
      animation: float 5s linear infinite;
    }
    @keyframes float {
      from {transform: translateY(100vh) scale(1);}
      to {transform: translateY(-10vh) scale(1.5);}
    }
  </style>
</head>
<body>
  <!-- Part 1: Enter name -->
  <div class="card active" id="part1">
    <h1>Welcome</h1>
    <p>Please enter your name:</p>
    <input type="text" id="nameInput" placeholder="Your name here...">
    <br><br>
    <button onclick="goToPart2()">Enter</button>
  </div>

  <!-- Part 2: Valentine Question with Runaway No -->
  <div class="card" id="part2">
    <h1 id="valentineQuestion">Mang libre ka?,</h1>
    <button class="yesBtn" onclick="nextPart('celebration')">Yes 🥳</button>
    <button id="noBtn">No 🙄</button>
  </div>

  <!-- Part 3: Funny “Are you sure?” -->
  <div class="card" id="part3">
    <h1 id="funnyQuestion">Pag syurrr ba? <span id="funnyName"></span>? 🤨</h1>
    <p>huna hunaag tarong kay... ig mag no ka, then malason jud ka.</p>
    <button class="yesBtn" onclick="nextPart('celebration')">Okay fine, Yes 🥳</button>
    <button onclick="nextPart('part4')">Still No 🙂</button>
  </div>

  <!-- Part 4: Last Chance (Shrinking No & Growing Yes) -->
  <div class="card" id="part4">
    <h1>Last chance najud ni, <span id="lastChanceName"></span>...😏</h1>
    <p>Try pressing NO if you can...💅🏻💅🏻💅🏻</p>
    <button class="yesBtn" id="growingYes" onclick="nextPart('part5')">YES 💅🏻💅🏻</button>
    <button id="shrinkingNo"> 🖕🏻.</button>
  </div>

  <!-- Part 5: Celebration -->
  <div class="card" id="part5">
    <h1>🎉 Yay! 🎉</h1>
    <p id="celebrationMsg"></p>
    <p>Thank you, Thank you, Thank you</p>
  </div>

  <script>
    let userName = "";

    function goToPart2() {
      const nameField = document.getElementById("nameInput").value.trim();
      userName = nameField || "Friend"; // default if blank
      document.getElementById("funnyName").innerText = userName;
      document.getElementById("lastChanceName").innerText = userName;
      document.getElementById("valentineQuestion").innerText = `Mang libre ka? ${userName} 🌹`;
      nextPart('part2');
    }

    function nextPart(id) {
      if (id === "celebration") id = "part5"; // alias fix

      document.querySelectorAll('.card').forEach(c => c.classList.remove('active'));
      document.getElementById(id).classList.add('active');

      if(id === "part5") {
        document.getElementById("celebrationMsg").innerText =
          `Yay ${userName}! Im expecting 3 burgers, 1 large pizza, 4 chicken nuggets and a bottle of coke`;
        launchHearts();
      }
    }

    function launchHearts() {
      document.querySelectorAll(".heart").forEach(h => h.remove()); // cleanup
      for (let i = 0; i < 20; i++) {
        let heart = document.createElement("div");
        heart.classList.add("heart");
        heart.innerHTML = "❤️";
        heart.style.left = Math.random() * 100 + "vw";
        heart.style.animationDuration = (3 + Math.random() * 2) + "s";
        document.body.appendChild(heart);

        setTimeout(() => {
          heart.remove();
        }, 5000);
      }
    }

    // Runaway effect for No button (Part 2)
    const noBtn = document.getElementById("noBtn");
    let runCount = 0;
    const maxRuns = Math.floor(Math.random() * 4) + 2; // random 2–5

    function runaway(btn) {
      if (runCount < maxRuns) {
        let x = Math.random() * (window.innerWidth - btn.offsetWidth - 100);
        let y = Math.random() * (window.innerHeight - btn.offsetHeight - 150);
        btn.style.position = "absolute";
        btn.style.left = x + "px";
        btn.style.top = y + "px";
        runCount++;
      } else {
        nextPart('part3');
      }
    }

    noBtn.addEventListener("mouseover", () => runaway(noBtn));
    noBtn.addEventListener("touchstart", () => runaway(noBtn));
    noBtn.addEventListener("touchmove", () => runaway(noBtn));

    // Shrinking No & Growing Yes (Part 4)
    const shrinkingNo = document.getElementById("shrinkingNo");
    const growingYes = document.getElementById("growingYes");
    let shrinkSize = 16;
    let growSize = 16;

    shrinkingNo.addEventListener("click", () => {
      if (shrinkSize > 2) {
        shrinkSize -= 2;
        shrinkingNo.style.fontSize = shrinkSize + "px";
      }
      growSize += 4;
      growingYes.style.fontSize = growSize + "px";
    });
  </script>
</body>
</html>
