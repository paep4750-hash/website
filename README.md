<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ระบบสุ่มดวงมหาประลัย</title>
    <style>
        * { box-sizing: border-box; }
        body, html { 
            margin: 0; padding: 0; width: 100%; height: 100%; 
            background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
            font-family: 'Arial', sans-serif; color: white;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
        }
        
        .container {
            width: 90%; max-width: 500px; background: rgba(0, 0, 0, 0.6);
            padding: 30px; border-radius: 20px; text-align: center;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
            border: 2px solid rgba(255, 255, 255, 0.1);
        }

        h1 { font-size: 2rem; margin-bottom: 20px; color: #ffeb3b; text-shadow: 2px 2px #000; }

        /* ปุ่มสุ่มขนาดใหญ่เหมาะกับแท็บเล็ต */
        #roll-btn {
            width: 100%; padding: 20px; font-size: 1.5rem; font-weight: bold;
            color: #000; background: #00ff88; border: none; border-radius: 50px;
            cursor: pointer; box-shadow: 0 5px 15px rgba(0,255,136,0.4);
            transition: transform 0.1s ease, background-color 0.2s;
        }
        #roll-btn:active { transform: scale(0.95); background-color: #00cc6a; }

        /* กล่องแสดงผลลัพธ์ */
        #result-box {
            margin-top: 30px; min-height: 120px; padding: 20px;
            background: rgba(255, 255, 255, 0.1); border-radius: 15px;
            display: flex; align-items: center; justify-content: center;
            font-size: 1.4rem; font-weight: bold; line-height: 1.5;
            border: 1px dashed rgba(255,255,255,0.3);
        }

        /* ตกแต่งสีตามเกรดรางวัล */
        .color-legendary { color: #ff33ff; text-shadow: 0 0 10px #ff33ff; font-size: 1.6rem; }
        .color-rare { color: #ff9800; text-shadow: 0 0 8px #ff9800; }
        .color-common { color: #00e5ff; }
        .color-salt { color: #aaaaaa; }
    </style>
</head>
<body>

    <div class="container">
        <h1>🔮 ระบบสุ่มดวงสุดเกรียน 🔮</h1>
        <button id="roll-btn">กดเพื่อสุ่มดวง!</button>
        
        <div id="result-box">
            <span id="result-text">กดปุ่มด้านบนเพื่อเริ่มสุ่มดวงของคุณ...</span>
        </div>
    </div>

    <script>
        // ตัวแปรนับรอบสะสมในการสุ่ม
        let rollCount = 0;

        const rollBtn = document.getElementById('roll-btn');
        const resultText = document.getElementById('result-text');

        rollBtn.addEventListener('click', () => {
            rollCount++; // เพิ่มจำนวนรอบสะสมขึ้นทีละ 1
            
            // เช็กเงื่อนไขลับ: ถ้ากดครบ 55 รอบพอดีเป๊ะ ล็อกรางวัลใหญ่ทันที
            if (rollCount === 55) {
                resultText.innerHTML = `<span class="color-legendary">🌟 รางวัลใหญ่ระดับพระเจ้า! 🌟<br>Gemini 0.0000.1 "Geminiเป็นเก"</span>`;
                return;
            }

            // ระบบสุ่มแบบปกติ (กรณีรอบที่ 1-54 หรือรอบที่ 56 เป็นต้นไป)
            let chance = Math.random() * 100; // สุ่มตัวเลขตั้งแต่ 0 ถึง 99.99

            if (chance < 2) {
                // โอกาส 2% (0 ถึง 1.99)
                resultText.innerHTML = `<span class="color-rare">🍗 ยินดีด้วย!<br>คุณได้รับไก่ทอด (2%)</span>`;
            } 
            else if (chance < 69) {
                // โอกาส 67% (2 ถึง 68.99)
                resultText.innerHTML = `<span class="color-common">🏳️‍🌈 ผลลัพธ์ดวงของคุณวันนี้:<br>คุณเป็นเก (67%)</span>`;
            } 
            else {
                // โอกาสที่เหลือ 31% (69 ถึง 100) -> เกลือ
                resultText.innerHTML = `<span class="color-salt">🍃 ดวงเกลือมาก...<br>คุณสุ่มไม่ได้อะไรเลย (31%)</span>`;
            }
        });
    </script>
</body>
</html>
