
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Script Hub</title>
    <style>
        /* สไตล์พื้นฐานของเว็บไซต์ */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #121212;
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }

        .container {
            text-align: center;
            background-color: #1e1e1e;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
            max-width: 500px;
            width: 90%;
        }

        h1 {
            margin-bottom: 20px;
            color: #00ff88;
        }

        .btn {
            background-color: #00ff88;
            color: #121212;
            border: none;
            padding: 12px 24px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 6px;
            cursor: pointer;
            transition: background 0.3s ease;
        }

        .btn:hover {
            background-color: #00cc6e;
        }

        .btn-secondary {
            background-color: #555;
            color: #fff;
            margin-top: 15px;
        }

        .btn-secondary:hover {
            background-color: #444;
        }

        /* ซ่อน/แสดงตามหน้า */
        .hidden {
            display: none;
        }

        textarea {
            width: 100%;
            height: 150px;
            background-color: #2d2d2d;
            color: #00ff88;
            border: 1px solid #444;
            border-radius: 6px;
            padding: 10px;
            font-family: monospace;
            resize: none;
            margin-top: 15px;
            margin-bottom: 15px;
        }
    </style>
</head>
<body>

    <div class="container">
        <div id="home-page">
            <h1>ยินดีต้อนรับสู่ Script Hub</h1>
            <button class="btn" id="explore-btn">🧭 ปุ่มสำรวจ</button>
        </div>

        <div id="script-page" class="hidden">
            <h1>สคริปต์ของคุณพร้อมใช้งานแล้ว</h1>
            
            <textarea readonly id="script-box"></textarea>
            
            <button class="btn" onclick="copyScript()">📋 คัดลอกสคริปต์</button>
            <br>
            <button class="btn btn-secondary" id="back-btn">🔙 กดย้อนกลับ</button>
        </div>
    </div>

    <script>
        const homePage = document.getElementById('home-page');
        const scriptPage = document.getElementById('script-page');
        const exploreBtn = document.getElementById('explore-btn');
        const backBtn = document.getElementById('back-btn');
        const scriptBox = document.getElementById('script-box');

        // 🛑 แก้ไขสคริปต์ประจำวัน/สัปดาห์ ตรงนี้ได้เลยครับ! 🛑
        const currentScript = `-- อัปเดตล่าสุด: ${new Date().toLocaleDateString('th-TH')}
script_key = "Free_key"
loadstring(game:HttpGet("https://raw.githubusercontent.com/Fluxyyy333/HoshiOnTop/main/loader.lua"))()`;

        // เมื่อกดปุ่มสำรวจ -> แสดงหน้าสคริปต์ทันที
        exploreBtn.addEventListener('click', () => {
            homePage.classList.add('hidden');
            scriptPage.classList.remove('hidden');
            scriptBox.value = currentScript; // ใส่สคริปต์เข้าไปในช่อง text
        });

        // เมื่อกดย้อนกลับ -> กลับหน้าแรก
        backBtn.addEventListener('click', () => {
            scriptPage.classList.add('hidden');
            homePage.classList.remove('hidden');
        });

        // ฟังก์ชันกดคัดลอกสคริปต์
        function copyScript() {
            scriptBox.select();
            scriptBox.setSelectionRange(0, 99999);
            navigator.clipboard.writeText(scriptBox.value);
            alert("คัดลอกสคริปต์เรียบร้อยแล้ว!");
        }
    </script>
</body>
</html>
