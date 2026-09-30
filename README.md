<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <title>數字紀錄系統</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 30px; }
    .box { border: 1px solid #ccc; padding: 20px; border-radius: 8px; max-width: 400px; margin-bottom: 20px; }
    input, button { padding: 10px; margin-top: 10px; width: 100%; box-sizing: border-box; }
    button { background-color: #4CAF50; color: white; border: none; cursor: pointer; }
    button:hover { background-color: #45a049; }
  </style>
</head>
<body>

  <div class="box">
    <h2>輸入數字紀錄</h2>
    <input type="number" id="numInput" placeholder="請輸入數字">
    <button onclick="sendData()">送出資料</button>
  </div>

  <div class="box">
    <h2>歷史紀錄</h2>
    <button onclick="readData()">刷新歷史資料</button>
    <ul id="logList"></ul>
  </div>

  <script>
    // ⚠️ 請替換為你在 Apps Script 部署後取得的 URL
    const GAS_URL = "YOUR_APPS_SCRIPT_URL_HERE";

    // 1. 寫入資料到 Google 試算表
    function sendData() {
      const num = document.getElementById("numInput").value;
      if (!num) return alert("請輸入數字！");

      fetch(GAS_URL, {
        method: "POST",
        mode: "no-cors", // 避免跨網域限制 (CORS)
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ number: num })
      })
      .then(() => {
        alert("資料已成功送出！");
        document.getElementById("numInput").value = "";
        readData(); // 送出後自動重新載入列表
      })
      .catch(err => alert("送出失敗：" + err));
    }

  // 2. 從 Google 試算表讀取資料
    function readData() {
      fetch(GAS_URL)
        .then(response => response.json())
        .then(data => {
          const list = document.getElementById("logList");
          list.innerHTML = "";
          
          // 逐列讀取資料並顯示
          data.forEach(row => {
            const li = document.createElement("li");
            // row[0] 為時間，row[1] 為數字
            const dateStr = new Date(row[0]).toLocaleString();
            li.textContent = `${dateStr} ： ${row[1]}`;
            list.appendChild(li);
          });
        })
        .catch(err => console.error("讀取失敗：", err));
    }

    // 頁面載入時自動讀取一次
    window.onload = readData;
  </script>
</body>
</html>
