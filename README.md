<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>PRIVATE JONAYET</title>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; background:#f9f9f9; }
    h1 { color: #333; margin-top: 20px; }
    .bio {
      margin-top: 10px;
      margin-bottom: 30px; /* bio এবং টেবিলের মধ্যে ফাঁকা */
      font-size: 18px;
      font-weight: bold;
      color: #000;
      line-height: 1.6;
    }
    table { margin: auto; border-collapse: collapse; width: 70%; }
    th, td { border: 1px solid #999; padding: 10px; }
    th { background: #e0e0e0; }
    input, button { padding: 8px; margin: 5px; }
    input { width: 150px; }
    .action-btn { cursor: pointer; margin-left: 5px; color: blue; text-decoration: underline; }
    .score-btn { cursor: pointer; margin-left: 5px; padding: 2px 6px; border: 1px solid #666; border-radius: 4px; background: #f0f0f0; }

    /* Admin login icon */
    #loginIcon {
      position: fixed;
      top: 10px;
      left: 10px;
      font-size: 30px;
      cursor: pointer;
      user-select: none;
    }
    #loginDiv {
      display: none;
      position: fixed;
      top: 50px;
      left: 10px;
      background: #fff;
      border: 1px solid #999;
      padding: 10px;
      border-radius: 8px;
      box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    }

    /* Mobile Number */
    #mumbaiNumber {
      position: fixed;
      top: 10px;
      right: 10px;
      background: #fff;
      border: 1px solid #999;
      padding: 8px 12px;
      border-radius: 6px;
      box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
      font-weight: bold;
      z-index: 1000;
    }
  </style>
</head>
<body>
  <h1>PRIVATE JONAYET</h1>

  <!-- Bio Section -->
  <div class="bio">
    لا إله إلا الله مُحَمَّدٌ رَسُولُ الله ﷺ <br>
    নামাজ বেহেশতের চাবি
  </div>

  <!-- Admin login icon -->
  <div id="loginIcon" onclick="toggleLoginDiv()">🔒</div>

  <!-- Admin login -->
  <div id="loginDiv">
    <input type="password" id="adminPassword" placeholder="Enter Admin Password">
    <button onclick="loginAdmin()">Login</button>
  </div>

  <!-- Input fields for new player (only visible to admin) -->
  <div id="inputDiv" style="display:none;">
    <input type="text" id="name" placeholder="Enter name">
    <input type="number" id="score" placeholder="Enter score">
    <button onclick="addPlayer()">Add</button>
    <button onclick="savePlayers()">Save</button>
  </div>

  <!-- Leaderboard table -->
  <table id="leaderboard">
    <tr>
      <th>Rank</th>
      <th>Name</th>
      <th>Score</th>
    </tr>
  </table>

  <!-- Mobile Number -->
  <div id="mumbaiNumber">
    Mobile Number: 01810-424455
  </div>

  <script>
    const ADMIN_PASSWORD = "private2025";
    let isAdmin = false;
    let players = JSON.parse(localStorage.getItem("players")) || [];

    function toggleLoginDiv() {
      const div = document.getElementById("loginDiv");
      div.style.display = div.style.display === "block" ? "none" : "block";
    }

    function loginAdmin() {
      let pw = document.getElementById("adminPassword").value;
      if(pw === ADMIN_PASSWORD) {
        isAdmin = true;
        document.getElementById("loginDiv").style.display = "none";
        document.getElementById("inputDiv").style.display = "block";
        alert("Admin logged in! You can now add/edit/delete scores.");
        updateBoard();
      } else {
        alert("Wrong password!");
      }
    }

    function updateBoard() {
      let table = document.getElementById("leaderboard");
      table.innerHTML = `
        <tr>
          <th>Rank</th>
          <th>Name</th>
          <th>Score</th>
        </tr>
      `;
      players.sort((a, b) => b.score - a.score);
      players.forEach((player, index) => {
        let row = table.insertRow();
        row.insertCell(0).innerText = index + 1;

        let nameCell = row.insertCell(1);
        nameCell.innerText = player.name;
        if (isAdmin) {
          let editBtn = document.createElement("span");
          editBtn.innerText = " Edit";
          editBtn.className = "action-btn";
          editBtn.onclick = () => editPlayer(index);
          let delBtn = document.createElement("span");
          delBtn.innerText = " Delete";
          delBtn.className = "action-btn";
          delBtn.onclick = () => deletePlayer(index);
          nameCell.appendChild(editBtn);
          nameCell.appendChild(delBtn);
        }

        let scoreCell = row.insertCell(2);
        scoreCell.innerText = player.score;

        if (isAdmin) {
          let plusBtn = document.createElement("button");
          plusBtn.innerText = "+";
          plusBtn.className = "score-btn";
          plusBtn.onclick = () => { players[index].score++; updateBoard(); };

          let minusBtn = document.createElement("button");
          minusBtn.innerText = "-";
          minusBtn.className = "score-btn";
          minusBtn.onclick = () => { if(players[index].score>0) players[index].score--; updateBoard(); };

          scoreCell.appendChild(plusBtn);
          scoreCell.appendChild(minusBtn);
        }

        if (index === 0) row.style.background = "gold";
        else if (index === 1) row.style.background = "silver";
        else if (index === 2) row.style.background = "lightcoral";
      });
    }

    function addPlayer() {
      if(!isAdmin) return alert("You are not admin!");
      let name = document.getElementById("name").value.trim();
      let score = parseInt(document.getElementById("score").value);
      if(name && !isNaN(score)) {
        players.push({ name, score });
        updateBoard();
        document.getElementById("name").value = "";
        document.getElementById("score").value = "";
      } else {
        alert("Please enter a valid name and score");
      }
    }

    function editPlayer(index) {
      let newName = prompt("Enter new name:", players[index].name);
      let newScore = prompt("Enter new score:", players[index].score);
      if (newName && !isNaN(parseInt(newScore))) {
        players[index].name = newName;
        players[index].score = parseInt(newScore);
        updateBoard();
      }
    }

    function deletePlayer(index) {
      if(confirm("Are you sure you want to delete this player?")) {
        players.splice(index, 1);
        updateBoard();
      }
    }

    function savePlayers() {
      localStorage.setItem("players", JSON.stringify(players));
      alert("Leaderboard saved successfully!");
    }

    updateBoard();
  </script>
</body>
</html># JONAYET-PRIVATE
JONAYET PRIVATE
