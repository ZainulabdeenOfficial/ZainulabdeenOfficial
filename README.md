<!DOCTYPE html>
<html lang="en" data-theme="dracula">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>GitHub Streak Score Card</title>
  <style>
    :root {
      --bg-color: #0d1117;
      --text-color: #c9d1d9;
      --accent-color: #58a6ff;
      --fill-color: #1f6feb;
    }

    [data-theme='light'] {
      --bg-color: #ffffff;
      --text-color: #000000;
      --accent-color: #007bff;
      --fill-color: #00bfff;
    }

    [data-theme='nord'] {
      --bg-color: #2e3440;
      --text-color: #eceff4;
      --accent-color: #81a1c1;
      --fill-color: #5e81ac;
    }

    [data-theme='dark'] {
      --bg-color: #0d1117;
      --text-color: #c9d1d9;
      --accent-color: #58a6ff;
      --fill-color: #1f6feb;
    }

    body {
      margin: 0;
      padding: 20px;
      background: var(--bg-color);
      color: var(--text-color);
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
    }

    .input-container {
      margin-bottom: 20px;
      text-align: center;
    }

    .input-container input, .input-container select, .input-container input[type="color"] {
      padding: 8px 12px;
      border-radius: 6px;
      font-size: 1rem;
      margin: 0 5px;
    }

    .card {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      max-width: 950px;
      width: 100%;
      background: var(--bg-color);
      border: 2px solid var(--accent-color);
      border-radius: 12px;
      padding: 20px;
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.5);
    }

    .card-section {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
      position: relative;
    }

    .card-section:not(:last-child)::after {
      content: "|";
      color: var(--accent-color);
      position: absolute;
      right: -10px;
      font-weight: bold;
      font-size: 24px;
      top: 50%;
      transform: translateY(-50%);
    }

    .profile-pic, .circle-container {
      width: 100px;
      height: 100px;
      border-radius: 50%;
      border: 2px solid var(--accent-color);
      overflow: hidden;
    }

    .profile-pic {
      object-fit: cover;
    }

    .circle-svg {
      width: 100%;
      height: 100%;
    }

    .score-text {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 1rem;
      color: var(--text-color);
      font-weight: bold;
      pointer-events: none;
      text-align: center;
    }

    .stat-item {
      font-size: 1rem;
      font-weight: 600;
      margin: 8px 0;
      color: var(--text-color);
    }

    .markdown-link {
      margin-top: 20px;
      text-align: center;
    }

    @media (max-width: 768px) {
      .card {
        grid-template-columns: 1fr;
      }
      .card-section:not(:last-child)::after {
        content: "";
      }
    }
  </style>
</head>
<body>
  <div class="input-container">
    <input type="text" id="usernameInput" placeholder="Enter GitHub username" onkeypress="handleEnter(event)" />
    <select id="themeSelect" onchange="changeTheme()">
      <option value="dracula">Dracula</option>
      <option value="light">Light</option>
      <option value="nord">Nord</option>
      <option value="dark">Dark</option>
    </select>
    <label>Border Color: <input type="color" id="borderColorPicker" onchange="updateCustomColors()"></label>
    <label>Water Color: <input type="color" id="waterColorPicker" onchange="updateCustomColors()"></label>
  </div>

  <div class="card">
    <div class="card-section">
      <img src="" id="avatar" alt="GitHub Avatar" class="profile-pic">
      <p><strong id="ghUser">Username</strong></p>
    </div>
    <div class="card-section">
      <div class="circle-container">
        <svg viewBox="0 0 100 100" class="circle-svg">
          <defs>
            <clipPath id="wave-clip">
              <circle cx="50" cy="50" r="40" />
            </clipPath>
          </defs>
          <g clip-path="url(#wave-clip)">
            <rect y="60" width="100" height="100" fill="var(--fill-color)">
              <animate attributeName="y" values="60;20;60" dur="3s" repeatCount="indefinite" />
            </rect>
          </g>
          <circle cx="50" cy="50" r="40" stroke="var(--accent-color)" stroke-width="2" fill="none" />
        </svg>
        <div class="score-text" id="streakScore">🔥</div>
      </div>
    </div>
    <div class="card-section">
      <div class="stat-item">🏆 <span id="ghLongest">0</span> Longest Streak</div>
      <div class="stat-item">📊 <span id="ghTotal">0</span> Contributions</div>
    </div>
  </div>

  <div class="markdown-link">
    <p>Copy for GitHub README:</p>
    <code id="markdownPreview">![Streak Score](https://github-streak-score-card.vercel.app/?user=ZainulabdeenOfficial)</code>
  </div>

  <script>
    function handleEnter(event) {
      if (event.key === "Enter") {
        const username = document.getElementById("usernameInput").value.trim();
        if (username) {
          window.location.search = `?user=${username}`;
        }
      }
    }

    function getUsernameFromURL() {
      const params = new URLSearchParams(window.location.search);
      return params.get('user') || 'ZainulabdeenOfficial';
    }

    function changeTheme() {
      const theme = document.getElementById("themeSelect").value;
      document.documentElement.setAttribute("data-theme", theme);
    }

    function updateCustomColors() {
      const borderColor = document.getElementById("borderColorPicker").value;
      const fillColor = document.getElementById("waterColorPicker").value;
      document.documentElement.style.setProperty('--accent-color', borderColor);
      document.documentElement.style.setProperty('--fill-color', fillColor);
    }

    async function loadStats(username) {
      document.getElementById("ghUser").textContent = username;
      document.getElementById("markdownPreview").textContent = `![Streak Score](https://github-streak-score-card.vercel.app/?user=${username})`;

      try {
        const res = await fetch(`https://api.github.com/users/${username}`);
        const user = await res.json();
        document.getElementById('avatar').src = user.avatar_url || '';
      } catch (e) {
        document.getElementById('avatar').style.display = 'none';
      }

      try {
        const res = await fetch(`https://github-contributions-api.jogruber.de/v4/${username}`);
        const data = await res.json();
        const total = data?.years?.[0]?.total || '0';
        document.getElementById("ghTotal").textContent = total;
      } catch (e) {
        document.getElementById("ghTotal").textContent = '0';
      }

      try {
        const response = await fetch(`https://streak-stats.demolab.com/api/json?user=${username}`);
        const json = await response.json();
        const current = json?.currentStreak?.length || 0;
        const longest = json?.longestStreak?.length || 0;
        document.getElementById("ghLongest").textContent = longest;
        document.getElementById("streakScore").innerHTML = `🔥\n${current} Days`;
      } catch (e) {
        document.getElementById("ghLongest").textContent = '0';
        document.getElementById("streakScore").innerHTML = `🔥\n0 Days`;
      }
    }

    const username = getUsernameFromURL();
    loadStats(username);
    document.getElementById("usernameInput").value = username;
  </script>
</body>
</html>
