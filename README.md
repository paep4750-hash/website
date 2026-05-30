<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>วงล้อสุ่มดวงมหาประลัย v2</title>
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
        
        /* รายการบอกเปอร์เซ็นต์ */
        .rate-info { background: rgba(0,0,0,0.5); padding: 12px; border-radius: 12px; font-size: 0.9rem; margin-bottom: 25px; border: 1px solid rgba(255,255,255,0.15); }
        .rate-info span { margin: 0 5px; font-weight: bold; display: inline-block; }

        /* ตัววงล้อ */
        .wheel-wrapper { position: relative; width: 320px; height: 320px; margin: 0 auto 25px auto; }
        
        /* เข็มชี้รางวัลด้านบน */
        .pointer { 
            position: absolute; top: -15px; left: 50%; transform: translateX(-50%);
            width: 0; height: 0; border-left: 15px solid transparent; border-right: 15px solid transparent;
            border-top: 30px solid #ef4444; z-index: 10; filter: drop-shadow(0px 3px 5px rgba(0,0,0,0.5));
        }

        /* หน้าปัดวงล้อสัดส่วนสีเหลือง/น้ำเงิน/เทา */
        .wheel { 
            width: 100%; height: 100%; border-radius: 50%; 
            border: 8px solid #facc15; box-shadow: 0 0 25px rgba(0,0,0,0.6);
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

        /* ระบบหน้าต่างข้อความเด้ง (Popup Modal) */
        .modal-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.75); display: flex; align-items: center;
            justify-content: center; z-index: 100; opacity: 0; pointer-events: none;
            transition: opacity 0.3s ease;
        }
        .modal-overlay.show { opacity: 1; pointer-events: auto; }

        .modal-box {
            background: #1f2937; border: 3px solid #facc15; width: 85%; max-width: 380px;
            padding: 30px 20px; border-radius: 20px; text-align: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            transform: scale(0.7); transition: transform 0.3s ease;
        }
        .modal-overlay.show .modal-box { transform: scale(1); }

        .modal-title { font-size: 1.6rem; font-weight: bold; color: #facc15; margin-bottom: 15px; }
        .modal-text { font-size: 1.3rem; margin-bottom: 25px; font-weight: bold; line-height: 1.4; }
        
        .modal-close-btn {
            background: #facc15; color: #000; border: none; padding: 12px 35px;
            font-size: 1.1rem; font-weight: bold; border-radius: 25px; cursor: pointer;
            box-shadow: 0 4px 10px rgba(250,204,21,0.3);
        }
        .modal-close-btn:active { background: #eab308; }

        /* ข้อความสถานะด้านล่างวงล้อ */
        #status-text { font-size: 1.1rem; color: #9ca3af; min-height: 24px; font-weight: bold; }
    </style>
</head>
<body>

    <div class="container">
        <h1>🔮 วงล้อมหาดวง 🔮</h1>
        
        <div class="rate-info">
            <span style="color:#ef4444">🍗 ไก่ทอด 2%</span> | 
            <span style="color:#60a5fa">🏳️‍🌈 คุณเป็นเก 67%</span> | 
            <span style="color:#9ca3af">🍃 เกลือ 31%</span>
        </div>

        <div class="wheel-wrapper">
            <div class="pointer"></div>
            <div id="wheel" class="wheel"></div>
            <div id="wheel-btn" class="wheel-btn">หมุน!</div>
        </div>
        
        <div id="status-text">กดตรงกลางวงล้อเพื่อเริ่มหมุน...</div>
    </div>

    <div id="reward-modal" class="modal-overlay">
        <div class="modal-box">
            <div class="modal-title">🎉 ผลลัพธ์ดวงของคุณ 🎉</div>
            <div id="modal-content" class="modal-text">ยินดีด้วยคุณได้รับรางวัล!</div>
            <button id="modal-close" class="modal-close-btn">ตกลง</button>
        </div>
    </div>

    <script>
        let isSpinning = false;
        let currentRotation = 0;

        const wheel = document.getElementById('wheel');
        const wheelBtn = document.getElementById('wheel-btn');
        const statusText = document.getElementById('status-text');
        
        const rewardModal = document.getElementById('reward-modal');
        const modalContent = document.getElementById('modal-content');
        const modalClose = document.getElementById('modal-close');

        function startSpin(e) {
            if (e) e.preventDefault();
            if (isSpinning) return;

            isSpinning = true;
            statusText.innerText = "กำลังสุ่มดวง...";

            let targetDeg = 0;
            let resultMessage = "";

            // ระบบสุ่มแบบสุ่มดวงสดแท้ร้อยเปอร์เซ็นต์อิงตามเรตที่ตั้งไว้
            let chance = Math.random() * 100;

            if (chance < 2) {
                // ได้รับไก่ทอด 2% -> สั่งให้วงล้อหยุดหมุนที่โซนสีแดง (0 ถึง 7.2 องศา)
                targetDeg = 3.6;
                resultMessage = `<span style="color:#f97316;">🍗 ยินดีด้วย!<br>คุณได้รับไก่ทอด (2%)</span>`;
            } else if (chance < 69) {
                // คุณเป็นเก 67% -> สั่งให้วงล้อหยุดหมุนที่โซนสีน้ำเงิน (7.2 ถึง 248.4 องศา)
                targetDeg = 120;
                resultMessage = `<span style="color:#60a5fa;">🏳️‍🌈 ยินดีด้วย!<br>คุณเป็นเก (67%)</span>`;
            } else {
                // เกลือ 31% -> สั่งให้วงล้อหยุดหมุนที่โซนสีเทา (248.4 ถึง 360 องศา)
                targetDeg = 300;
                resultMessage = `<span style="color:#9ca3af;">🍃 เสียใจด้วย...<br>คุณได้เกลือ ไม่ได้รับอะไรเลย (31%)</span>`;
            }

            // คำนวณความเร็วรอบหมุนติ้วๆ 5 รอบ (1800 องศา) แล้วเหวี่ยงไปลงล็อคเป้าหมาย
            const finalDeg = (360 - targetDeg);
            currentRotation += 1800 + finalDeg - (currentRotation % 360);
            
            wheel.style.transform = `rotate(${currentRotation}deg)`;

            // รอหน้าปัดวงล้อหมุนจนนิ่ง (4 วินาที) แล้วปล่อยหน้าต่าง Popup แจ้งข้อความเด้งขึ้นมา
            setTimeout(() => {
                modalContent.innerHTML = resultMessage;
                rewardModal.classList.add('show'); // สั่งให้หน้าต่าง Popup แสดงตัวออกจอ
                statusText.innerText = "สุ่มเสร็จสิ้น!";
            }, 4000);
        }

        // ปุ่มปิดหน้าต่างโมดอลเพื่อกลับมาสุ่มต่อได้เรื่อยๆ
        function closeModal() {
            rewardModal.classList.remove('show');
            statusText.innerText = "กดตรงกลางวงล้อเพื่อเริ่มหมุนอีกครั้ง...";
            isSpinning = false;
        }

        // ผูกสัญญานสัมผัสสำหรับแท็บเล็ตและหน้าจอคอมให้กดติดง่ายและลื่นไหล
        wheelBtn.addEventListener('touchstart', startSpin, { passive: false });
        wheelBtn.addEventListener('click', startSpin);
        
        modalClose.addEventListener('touchstart', (e) => { e.preventDefault(); closeModal(); });
        modalClose.addEventListener('click', closeModal);
    </script>
</body>
</html>
