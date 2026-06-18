<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Script Hub - Grow A Garden 2</title>
    <style>
        /* สไตล์พื้นฐานและพื้นหลังไล่เฉดสี */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: radial-gradient(circle, #1a1a2e 0%, #0f0c1b 100%);
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            overflow-x: hidden;
            position: relative;
        }

        /* กล่องคอนเทนเนอร์หลัก พร้อมเอฟเฟคแสงเรืองรอง (Glow) */
        .container {
            text-align: center;
            background-color: rgba(30, 30, 30, 0.85);
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 0 30px rgba(0, 255, 136, 0.3), inset 0 0 15px rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(8px);
            max-width: 550px;
            width: 90%;
            z-index: 10;
            border: 1px solid rgba(0, 255, 136, 0.2);
        }

        /* สไตล์ของรูปภาพโลโก้/แบนเนอร์ */
        .banner-img src="1781781753330.png"
            width: 100%;
            border-radius: 10px;
            margin-bottom: 15px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.6);
            border: 2px solid rgba(255, 215, 0, 0.5); /* ขอบทองจางๆ ให้เข้ากับธีม */
        }

        h1 {
            font-size: 24px;
            margin-bottom: 20px;
            color: #00ff88;
            text-shadow: 0 0 10px rgba(0, 255, 136, 0.6);
        }

        /* ปุ่มกดสไตล์นีออน */
        .btn {
            background: linear-gradient(135deg, #00ff88 0%, #00bc70 100%);
            color: #121212;
            border: none;
            padding: 14px 28px;
            font-size: 18px;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0, 255, 136, 0.4);
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0, 255, 136, 0.7);
        }

        .btn-secondary {
            background: linear-gradient(135deg, #555 0%, #333 100%);
            color: #fff;
            margin-top: 15px;
            font-size: 15px;
            padding: 10px 20px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
        }

        .btn-secondary:hover {
            box-shadow: 0 6px 15px rgba(255, 255, 255, 0.2);
        }

        .hidden {
            display: none;
        }

        textarea {
            width: 100%;
            height: 140px;
            background-color: #161616;
            color: #00ff88;
            border: 1px solid #00ff88;
            border-radius: 8px;
            padding: 12px;
            font-family: monospace;
            resize: none;
            margin-top: 15px;
            margin-bottom: 15px;
            box-sizing: border-box;
            box-shadow: inset 0 0 10px rgba(0, 255, 136, 0.2);
        }

        /* CSS สำหรับสร้างเอฟเฟคประกายดาววิ่ง (Sparkles) */
        .sparkle {
            position: absolute;
            background: radial-gradient(circle, #fff 10%, transparent 80%);
            border-radius: 50%;
            pointer-events: none;
            animation: flyAndFade 2s linear infinite;
        }

        @keyframes flyAndFade {
            0% {
                transform: translateY(0) scale(0);
                opacity: 0;
            }
            50% {
                opacity: 1;
            }
            100% {
                transform: translateY(-100px) scale(1.2);
                opacity: 0;
            }
        }
    </style>
</head>
<body>

    <div class="container">
        <img src="1000394904.png" alt="Grow A Garden 2 Banner" class="banner-img">

        <div id="home-page">
            <h1>Grow A Garden 2 - Script Hub</h1>
            <button class="btn" id="explore-btn">🧭 สำรวจรับสคริปต์</button>
        </div>

        <div id="script-page" class="hidden">
            <h1>สคริปต์พร้อมใช้งานแล้ว!</h1>
            
            <textarea readonly id="script-box"></textarea>
            
            <button class="btn" onclick="copyScript()">📋 คัดลอกสคริปต์</button>
            <br>
            <button class="btn btn-secondary" id="back-btn">🔙 ย้อนกลับ</button>
        </div>
    </div>

    <script>
        const homePage = document.getElementById('home-page');
        const scriptPage = document.getElementById('script-page');
        const exploreBtn = document.getElementById('explore-btn');
        const backBtn = document.getElementById('back-btn');
        const scriptBox = document.getElementById('script-box');

        // สคริปต์ที่คุณสามารถเข้ามาแก้ไขอัปเดตได้เรื่อยๆ
        const currentScript = `-- อัปเดตล่าสุด: ${new Date().toLocaleDateString('th-TH')}
-- แผนที่: Grow A Garden 2 (ไม่มีคีย์ / ออโต้เก็บเกล็ดทอง / เอนโมว์)
script_key = "Free_key"
loadstring(game:HttpGet("https://raw.githubusercontent.com/Fluxyyy333/HoshiOnTop/main/loader.lua"))()

        // กดปุ่มสำรวจ
        exploreBtn.addEventListener('click', () => {
            homePage.classList.add('hidden');
            scriptPage.classList.remove('hidden');
            scriptBox.value = currentScript;
        });

        // กดย้อนกลับ
        backBtn.addEventListener('click', () => {
            scriptPage.classList.add('hidden');
            homePage.classList.remove('hidden');
        });

        // คัดลอกสคริปต์
        function copyScript() {
            scriptBox.select();
            scriptBox.setSelectionRange(0, 99999);
            navigator.clipboard.writeText(scriptBox.value);
            alert("คัดลอกสคริปต์ไปใช้งานได้เลย!");
        }

        // --- ระบบสร้างเอฟเฟคประกายระยิบระยับ (Sparkle Effect) ---
        function createSparkle() {
            const sparkle = document.createElement('div');
            sparkle.classList.add('sparkle');
            
            // สุ่มตำแหน่งเริ่มต้น (กระจายทั่วจอ)
            sparkle.style.left = Math.random() * 100 + 'vw';
            sparkle.style.top = Math.random() * 100 + 'vh';
            
            // สุ่มขนาดของประกายดาว
            const size = Math.random() * 6 + 2; // ขนาด 2px ถึง 8px
            sparkle.style.width = size + 'px';
            sparkle.style.height = size + 'px';
            
            // สุ่มระยะเวลาการแสดงเอฟเฟค
            const duration = Math.random() * 2 + 1.5;
            sparkle.style.animationDuration = duration + 's';
            
            document.body.appendChild(sparkle);
            
            // ลบประกายทิ้งเมื่อแสดงแอนิเมชันจบ
            setTimeout(() => {
                sparkle.remove();
            }, duration * 1000);
        }

        // ปล่อยประกายออกมาเรื่อยๆ ทุกๆ 150 มิลลิวินาที
        setInterval(createSparkle, 150);
    </script>
</body>
</html>
