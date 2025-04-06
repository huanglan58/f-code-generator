<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <title>发光体编号生成器</title>
  <style>
    body {
      font-family: 'Helvetica Neue', sans-serif;
      background-color: #0f0f0f;
      color: white;
      text-align: center;
      padding: 40px;
    }
    input, select, button {
      padding: 12px;
      margin: 10px;
      font-size: 16px;
      border-radius: 6px;
    }
    .result {
      margin-top: 40px;
      padding: 20px;
      background: #1e1e1e;
      border-radius: 12px;
    }
    canvas {
      margin-top: 20px;
      display: none;
    }
  </style>
</head>
<body>
  <h1>F_发光体编号生成器</h1>

  <label>请输入你的昵称：</label><br>
  <input type="text" id="username" placeholder="你的宇宙昵称"><br>

  <label>请选择你现在的状态：</label><br>
  <select id="status">
    <option>平静</option>
    <option>开心</option>
    <option>焦虑</option>
    <option>柔软</option>
    <option>崩溃</option>
    <option>突破</option>
  </select><br>

  <label>你现在所在的位置：</label><br>
  <select id="location">
    <option>街头</option>
    <option>公园</option>
    <option>地铁</option>
    <option>房间</option>
    <option>展览</option>
    <option>咖啡馆</option>
  </select><br>

  <label>可选上传你的背景图：</label><br>
  <input type="file" id="bgImageInput" accept="image/*"><br>

  <button onclick="generateCode()">生成我的发光体编号</button>

  <div class="result" id="output" style="display:none">
    <h3 id="userId"></h3>
    <h2 id="code"></h2>
    <p id="signature"></p>
    <button onclick="generateCardImage()">生成编号卡图并下载</button>
    <canvas id="cardCanvas" width="800" height="1200"></canvas>
  </div>

  <script>
    const statusMap = {
      "平静": "01",
      "开心": "04",
      "焦虑": "02",
      "柔软": "03",
      "崩溃": "05",
      "突破": "06"
    };

    const locationMap = {
      "街头": "02",
      "公园": "01",
      "地铁": "06",
      "房间": "03",
      "展览": "07",
      "咖啡馆": "02"
    };

    const senseOptions = ["01", "02", "03", "04", "05", "06"];
    const signatures = {
      "F_01.04.02": "你今天像一面宁静的镜子，映出街头最柔和的光。",
      "F_04.04.07": "你在艺术空间发光的不是外表，而是意识本身。",
      "F_02.05.06": "你在匆忙中寻找节奏，而节奏正等待你停下来。"
    };

    let userCounter = 101;
    let currentUserId = "";
    let currentFCode = "";
    let currentSignature = "";
    let bgImage = null;

    document.getElementById("bgImageInput").addEventListener("change", function(e) {
      const reader = new FileReader();
      reader.onload = function(event) {
        const img = new Image();
        img.onload = function() {
          bgImage = img;
        };
        img.src = event.target.result;
      };
      reader.readAsDataURL(e.target.files[0]);
    });

    function generateCode() {
      const username = document.getElementById("username").value || "匿名发光者";
      const status = document.getElementById("status").value;
      const location = document.getElementById("location").value;
      const s = statusMap[status] || "00";
      const l = locationMap[location] || "00";
      const p = senseOptions[Math.floor(Math.random() * senseOptions.length)];
      const code = `F_${s}.${p}.${l}`;
      const signature = signatures[code] || "这是一次未知的发光旅程，你的编号是宇宙中新诞生的一颗星。";
      const userId = `发光体_00${userCounter}`;

      document.getElementById("output").style.display = 'block';
      document.getElementById("userId").textContent = `${username}｜${userId}`;
      document.getElementById("code").textContent = `今日编号：${code}`;
      document.getElementById("signature").textContent = signature;

      currentUserId = userId;
      currentFCode = code;
      currentSignature = signature;

      userCounter++;
    }

    function generateCardImage() {
      const canvas = document.getElementById("cardCanvas");
      const ctx = canvas.getContext("2d");
      canvas.style.display = "block";

      if (bgImage) {
        ctx.drawImage(bgImage, 0, 0, canvas.width, canvas.height);
      } else {
        ctx.fillStyle = "#111";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
      }

      ctx.fillStyle = "#fff";
      ctx.font = "36px sans-serif";
      ctx.fillText(currentUserId, 40, 100);

      ctx.font = "48px sans-serif";
      ctx.fillText(currentFCode, 40, 180);

      ctx.beginPath();
      ctx.arc(400, 350, 80, 0, 2 * Math.PI);
      ctx.fillStyle = "#f0c040";
      ctx.fill();

      ctx.font = "28px sans-serif";
      const lines = wrapText(currentSignature, 30);
      lines.forEach((line, i) => {
        ctx.fillText(line, 40, 500 + i * 40);
      });

      const logo = new Image();
      logo.onload = () => {
        ctx.drawImage(logo, 600, 1050, 160, 160);
        const link = document.createElement('a');
        link.download = `${currentUserId}_${currentFCode}.png`;
        link.href = canvas.toDataURL();
        link.click();
      };
      logo.src = "https://files.oaiusercontent.com/file-KTUQ3uSR8nftzgmFmLKUbB?se=2124-04-08T01%3A02%3A33Z&sp=r&sv=2021-08-06&sr=b&rscd=inline&rsct=image/jpeg&skoid=f4107b58-9a50-4f4b-94db-f6d07bb9d99d&sktid=10b426c7-cd5b-4b9a-98a9-3d6dd41cbf9b&skt=2024-04-07T01%3A02%3A33Z&ske=2024-04-08T01%3A02%3A33Z&sks=b&skv=2021-08-06&sig=REDACTED";
    }

    function wrapText(text, maxChars) {
      const result = [];
      let line = "";
      for (let word of text) {
        line += word;
        if (line.length >= maxChars || word === '\n') {
          result.push(line);
          line = "";
        }
      }
      if (line) result.push(line);
      return result;
    }
  </script>
</body>
</html># f-code-generator
