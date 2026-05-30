<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>วงล้อสุ่มดวงมหาประลัย v4</title>
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
        .rate-info { background: rgba(0,0,0,0.5); padding: 12px; border-radius: 12px; font-size: 0.85rem; margin-bottom: 25px; border: 1px solid rgba(255,255,255,0.15); line-height: 1.4; }
        .rate-info span { margin: 0 4px; font-weight: bold; display: inline-block; }

        /* ตัววงล้อ */
        .wheel-wrapper { position: relative; width: 320px; height: 320px; margin: 0 auto 25px auto; }
        
        /* เข็มชี้รางวัลด้านบน */
        .pointer { 
            position: absolute; top: -15px; left: 50%; transform: translateX(-50%);
            width: 0; height: 0; border-left: 15px solid transparent; border-right: 15px solid transparent;
            border-top: 30px solid #ef4444; z-index: 10; filter: drop-shadow(0px 3px 5px rgba(0,0,0,0.5));
        }

        /* หน้าปัดวงล้อ ขยายแถบสีชมพู Gemini ให้กว้างขึ้นเพื่อให้มองเห็นขีดได้ชัดเจนบนจอ */
        .wheel { 
            width: 100%; height: 100%; border-radius: 50%; 
            border: 8px solid #facc15; box-shadow: 0 0 25px rgba(0,0,0,0.6);
            background: conic-gradient(
                #ec4899 0% 3%,     /* ขยายช่องให้เห็นขีดสีชมพูชัดเจนขึ้นบนหน้าจอ */
                #ef4444 3% 5%,     /* ไก่ทอด
                
