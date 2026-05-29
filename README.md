<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>วงล้อสุ่มดวงมหาประลัย</title>
    <style>
        * { box-sizing: border-box; user-select: none; -webkit-user-select: none; }
        body, html { 
            margin: 0; padding: 0; width: 100%; height: 100%; 
            background: linear-gradient(135deg, #111827, #312e81, #111827);
            font-family: sans-serif; color: white;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            overflow: hidden; touch-action: none;
        }
        
        .container { text-align: center; width: 100%; max-width: 450px; padding: 20px; z-index: 2; }
        h1 { font-size: 1.8rem; margin-bottom: 5px; color: #facc15; text-shadow: 2px 2px #000; }
        
        /* รายการเปอร์เซ็นต์บอกผู้เล่น */
        .rate-info { background: rgba(0,0,0,0.4); padding: 8px; border-radius: 10px; font-size: 0.85rem; margin-bottom: 20px; border: 1px solid rgba(255,255,255,0.1); }
        .rate-info span { margin: 0 5px; font-weight: bold; }

        /* ตัววงล้อ */
        .wheel-wrapper { position: relative; width: 320px; height: 320px; margin: 0 auto 25px auto; }
        
        /* เข็มชี้รางวัลด้านบน */
        .pointer { 
            position: absolute; top: -15px; left: 50%; transform: translateX(-50%);
            width: 0; height: 0; border-left: 15px solid transparent; border-right: 15px solid transparent;
            border-top: 30px solid #ef4444; z-index: 10; filter: drop-shadow(0px 2px 4px rgba(0,0,0,0.5));
        }

        /* หน้าปัดวงล้อ */
        .wheel { 
            width: 100%; height: 100%; border-radius: 50%; 
            border: 8px solid #facc15; box-shadow: 0 0 20px rgba(0,0,0,0.6), 0 0 15px rgba(250,204,21,0.3);
            background: conic-gradient(
                #ef4444 0% 2%,    /* ไก่ทอด 2% */
                #3b82f6 2% 69%,   /* คุณเป็นเก 67% */
                #4b5563 69% 100%  /* เกลือ 31% */
            );
            transition: transform 4s cubic-bezier(0.1, 0.8, 0.1, 1);
        }

        /* ปุ่มกดตรงกลางวงล้อ */
        .wheel-btn {
            position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);
            width: 80px; height: 80px; background: #facc15; border-radius: 50%;
            border: 4px solid #fff; color: #000; font-weight: bold; font-size: 1.2rem;
            display: flex; align-items: center; justify-content: center; cursor: pointer;
            box-shadow: 0 4px 10px rgba(0,0,0,0.5); z-index: 5;
        }
        .wheel-btn:active { background: #eab308; transform: translate(-50%, -50%) scale(0.95); }

        /* กล่องแสดงผลลัพธ์ */
        #result-box {
            min-height: 80px; padding: 15px; background: rgba(0, 0, 0, 0.5); 
            border-radius: 15px; border: 2px dashed rgba(255,255,255,0.2);
            font-size: 1.2rem; font-weight: bold; display: flex; align-items: center; justify-content: center;
        }
        .text-legendary { color: #f472b6; text-shadow: 0 0 8px #f472b6; font-size: 1.3rem; }
        .text-rare { color: #f97316; }
        .text-common { color: #60a5fa; }
    </style>
</head>
<body>

    <div class="container">
        <h1>🔮 วงล้อมหาดวง 🔮</h1>
        
        <div class="rate-info">
            <span style="color:#ef4444">🍗 ไก่ทอด 2%</span> | 
            <span style="color:#60a5fa">🏳️‍🌈 เป็นเก 67%</span> | 
            <span style="color:#9ca3af">🍃 เกลือ 31%</span><br>
            <span style="color:#f472b6">✨ ลับ: Geminiเป็นเก (สุ่มครบทุกๆ 55 รอบ)</span>
        </div>

        <div class="wheel-wrapper">
            <div class="pointer"></div>
            <div id="wheel" class="wheel"></div>
            <div id="wheel-btn" class="wheel-btn">หมุน!</div>
        </div>
        
        <div id="result-box">
            <span id="result-text" style="color: #aaa;">กดตรงกลางวงล้อเพื่อเริ่มหมุน...</span>
        </div>
    </div>

    <script>
        let rollCount = 0;
        let isSpinning = false;
        let currentRotation = 0;

        const wheel = document.getElementById('wheel');
        const wheelBtn = document.getElementById('wheel-btn');
        const resultText = document.getElementById('result-text');

        // ฟังก์ชันเริ่มหมุนวงล้อ (ผูกทั้งแบบ Touch และ Click แก้ปุ่มกดไม่ติด)
        function startSpin(e) {
            if (e) e.preventDefault();
            if (isSpinning) return;

            isSpinning = true;
            rollCount++; 
            resultText.style.opacity = "0.5";
            resultText.innerText = "กำลังสุ่มดวง...";

            let targetDeg = 0;
            let resultHTML = "";

            // ตรวจสอบเงื่อนไขลับ: ครบทุกๆ 55 รอบ (55, 110, 165...) รับตัวท็อปทันที
            if (rollCount % 55 === 0) {
                // บังคับให้วงล้อหยุดตรงโซน "เป็นเก" เพื่อเนียนๆ แต่แสดงข้อความลับระดับพระเจ้า
                targetDeg = 120; 
                resultHTML = `<span class="text-legendary">🌟 รางวัลใหญ่ระดับลับสุดยอด! 🌟<br>Gemini 0.0000.1 "Geminiเป็นเก"</span>`;
            } else {
                // สุ่มระบบปกติแบบอิงเปอร์เซ็นต์
                let chance = Math.random() * 100;

                if (chance < 2) {
                    // ไก่ทอด 2% -> หยุดช่วงองศา 0 ถึง 7.2
                    targetDeg = 3.6;
                    resultHTML = `<span class="text-rare">🍗 ยินดีด้วย! ได้รับไก่ทอด (2%)</span>`;
                } else if (chance < 69) {
                    // คุณเป็นเก 67% -> หยุดช่วงองศา 7.2 ถึง 248.4
                    targetDeg = 120;
                    resultHTML = `<span class="text-common">🏳️‍🌈 ผลลัพธ์: คุณเป็นเก (67%)</span>`;
                } else {
                    // เกลือ 31% -> หยุดช่วงองศา 248.4 ถึง 360
                    targetDeg = 300;
                    resultHTML = `<span style="color:#aaa;">🍃 ดวงเกลือ... ไม่ได้รับอะไรเลย (31%)</span>`;
                }
            }

            // คำนวณองศาการหมุน: ให้หมุนรอบตัวเองก่อน 5 รอบ (1800 องศา) แล้วค่อยไปหยุดที่เป้าหมาย
            // ลบองศาออกจาก 360 เพราะวงล้อหมุนตามเข็มนาฬิกาแต่จุดหยุดสวนทางกับเข็มชี้ด้านบน
            const finalDeg = (360 - targetDeg);
            currentRotation += 1800 + finalDeg - (currentRotation % 360);
            
            wheel.style.transform = `rotate(${currentRotation}deg)`;

            // รอจนกว่าวงล้อจะหยุดหมุน (4 วินาทีตาม CSS transition) แล้วค่อยโชว์ผลลัพธ์
            setTimeout(() => {
                resultText.style.opacity = "1";
                resultText.innerHTML = resultHTML;
                isSpinning = false;
            }, 4000);
        }

        // ใช้ทัชอีเวนต์คู่กับคลิกอีเวนต์เพื่อแก้ปัญหาแท็บเล็ตจิ้มยาก
        wheelBtn.addEventListener('touchstart', startSpin, { passive: false });
        wheelBtn.addEventListener('click', startSpin);
    </script>
</body>
</html>
