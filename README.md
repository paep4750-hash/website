<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Dark Chat</title>

<style>
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:Arial,sans-serif;
}

body{
background:#0d1117;
color:#fff;
display:flex;
justify-content:center;
padding:20px;
}

.container{
width:100%;
max-width:800px;
background:#161b22;
padding:20px;
border-radius:18px;
box-shadow:0 0 30px rgba(0,255,255,.15);
/* เพิ่มขอบเรนโบว์แบบเคลื่อนไหว (Rainbow Border) */
border: 4px solid transparent;
background-image: linear-gradient(#161b22, #161b22), linear-gradient(120deg, #ff0000, #ff7f00, #ffff00, #00ff00, #00ffff, #0000ff, #8b00ff, #ff0000);
background-origin: border-box;
background-clip: padding-box, border-box;
background-size: 200% auto;
animation: rainbow-border 4s linear infinite;
}

/* ออนิเมชันสำหรับขอบเรนโบว์ */
@keyframes rainbow-border {
0% { background-position: 0% 50%; }
50% { background-position: 100% 50%; }
100% { background-position: 0% 50%; }
}

h1{
text-align:center;
margin-bottom:20px;
color:#00d4ff;
}

.chat{
background:#0b1015;
height:420px;
overflow-y:auto;
padding:15px;
border-radius:15px;
margin-bottom:20px;
}

.msg{
max-width:75%;
padding:12px;
border-radius:15px;
margin:10px 0;
word-wrap:break-word;
animation:fade .25s;
}

.me{
margin-left:auto;
background:#00bcd4;
}

.you{
background:#333;
}

@keyframes fade{
from{
opacity:0;
transform:translateY(10px);
}
to{
opacity:1;
transform:translateY(0);
}
}

button{
width:100%;
padding:12px;
margin-top:10px;
background:#00bcd4;
border:none;
border-radius:10px;
color:white;
font-size:16px;
cursor:pointer;
transition:.25s;
}

button:hover{
background:#00dfff;
}

textarea{
width:100%;
height:70px;
margin-top:10px;
background:#20252d;
border:none;
color:white;
padding:10px;
border-radius:10px;
resize:none;
}

.box{
margin-top:15px;
}

#youBox{
display:none;
}

/* --- เริ่มต้นสไตล์ระบบยืนยันตัวตนที่เพิ่มเข้ามา --- */
#authBox {
background: #0b1015;
padding: 25px;
border-radius: 15px;
text-align: center;
margin-top: 10px;
margin-bottom: 20px;
border: 1px dashed #00bcd4;
}
.auth-btn-verify {
background: #00bcd4;
color: white;
padding: 14px;
border: none;
border-radius: 10px;
font-size: 16px;
font-weight: bold;
cursor: pointer;
width: 100%;
transition: 0.25s;
}
.auth-btn-verify:hover {
background: #00dfff;
}
.auth-btn-verify:disabled {
background: #555 !important;
color: #aaa;
cursor: not-allowed;
}
.auth-timer {
font-size: 18px;
font-weight: bold;
color: #00d4ff;
margin-top: 15px;
letter-spacing: 0.5px;
}
/* --- สิ้นสุดสไตล์ระบบยืนยันตัวตน --- */

/* --- สไตล์สำหรับ Modal ยืนยันการลบแชท เพื่อเลี่ยงการใช้ alert/confirm บน iframe --- */
.modal-overlay {
display: none;
position: fixed;
top: 0;
left: 0;
width: 100%;
height: 100%;
background: rgba(0,0,0,0.7);
justify-content: center;
align-items: center;
z-index: 1000;
}
.modal-content {
background: #161b22;
padding: 20px;
border-radius: 15px;
text-align: center;
max-width: 400px;
width: 90%;
border: 2px solid #00bcd4;
}
.modal-buttons {
display: flex;
gap: 10px;
margin-top: 15px;
}
.modal-btn-confirm {
background: #ff4444;
}
.modal-btn-confirm:hover {
background: #ff6666;
}
.modal-btn-cancel {
background: #555;
}
.modal-btn-cancel:hover {
background: #777;
}

/* ออนิเมชันปุ่มรับ Robux ดึงดูดสายตา */
@keyframes pulse-robux {
0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(233, 30, 99, 0.7); }
70% { transform: scale(1.02); box-shadow: 0 0 0 10px rgba(233, 30, 99, 0); }
100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(233, 30, 99, 0); }
}
</style>

</head>
<body>

<div class="container">

<h1>💬 Dark Chat ระบบ AI 3.0 </h1>

<!-- เพิ่มเติม: หน้าต่างยืนยันตัวตนจะแสดงเป็นอันดับแรก -->
<div id="authBox">
    <h2 style="font-size: 18px; color: #00d4ff; margin-bottom: 8px;">🔐 ระบบความปลอดภัยและการยืนยันตัวตน</h2>
    <p style="font-size: 14px; color: #888; margin-bottom: 15px;">กรุณากดปุ่มเพื่อยืนยันสิทธิ์เข้าใช้งานระบบแชท (ใช้เวลาดำเนินการ 40 วินาที)</p>
    <button id="authBtn" class="auth-btn-verify" onclick="startAuthVerification()">🛡️ เริ่มต้นการยืนยันตัวตน</button>
    <div id="authTimer" class="auth-timer" style="display: none;"></div>
</div>

<!-- เพิ่มเติม: ห่อหุ้มเนื้อหาเดิมทั้งหมดไว้ใน chatWrapper และซ่อนไว้ก่อนยืนยันสิทธิ์สำเร็จ -->
<div id="chatWrapper" style="display: none;">

<div id="chat" class="chat"></div>

<div class="box">

<button onclick="toggleBox('meBox')">
🧑 พิมพ์
</button>

<div id="meBox">

<textarea id="meText" placeholder="พิมพ์ข้อความ..."></textarea>

<button onclick="sendMe()">
ส่งข้อความ
</button>

</div>

</div>

<div class="box">

<button onclick="toggleBox('youBox')">
🤖 เปิด/ปิด ติดต่อเราได้ที่นี่
</button>

<div id="youBox">

<textarea id="youText" placeholder="พิมพ์ข้อความส่งไปยังserver..."></textarea>

<button onclick="sendYou()">
ส่งข้อความ
</button>

</div>

<!-- ปุ่มเข้า Discord Server -->
<button onclick="window.open('https://discord.gg/YUZY3uVwM','_blank')">
🌐 เข้า Server หลัก
</button>

</div>

<button onclick="showClearChatModal()">
🗑️ ล้างแชททั้งหมด
</button>

<!-- ================= เริ่มต้นฟังก์ชันพิเศษเพิ่มเติม (ยอมรับเกน, รับเกน, เอา 10 robux) ================= -->
<div id="gainSystemBox" style="margin-top: 15px; padding: 15px; background: #0b1015; border-radius: 15px; border: 1px dashed #e91e63; text-align: center;">
    <h3 style="font-size: 16px; color: #e91e63; margin-bottom: 10px; display: flex; align-items: center; justify-content: center; gap: 8px;">
        🎁 ระบบรับสิทธิ์และโบนัสเกณฑ์พิเศษ
    </h3>
    <p style="font-size: 13px; color: #aaa; margin-bottom: 12px;">กดยอมรับเกนและรอเวลาเพื่อปลดล็อกฟังก์ชันรับ Robux</p>
    
    <div style="display: flex; gap: 10px;">
        <button id="btnAcceptGain" style="margin-top: 0; background: #4caf50; flex: 1; font-weight: bold;" onclick="startGainTimer()">
            ✅ ยอมรับเกน
        </button>
        <button id="btnClaimGain" style="margin-top: 0; background: #9e9e9e; flex: 1; font-weight: bold; cursor: not-allowed;" onclick="claimGain()" disabled>
            🎁 รับเกน
        </button>
    </div>
    
    <div id="gainTimerStatus" style="font-size: 14px; font-weight: bold; color: #ffeb3b; margin-top: 12px; display: none;"></div>

    <!-- ปุ่มลับ เอา 10 robux จะปรากฏขึ้นหลังกดรับเกนสำเร็จ -->
    <button id="btnGetRobux" style="display: none; background: #e91e63; font-weight: bold; margin-top: 15px; animation: pulse-robux 1.5s infinite;" onclick="showRobuxSuccessModal()">
        🎮 เอา 10 robux
    </button>
</div>
<!-- ================= สิ้นสุดฟังก์ชันพิเศษเพิ่มเติม ================= -->

</div> <!-- ปิด chatWrapper -->

</div>

<!-- Custom Modal สำหรับยืนยันการล้างแชท -->
<div id="confirmModal" class="modal-overlay">
    <div class="modal-content">
        <h3>🗑️ ยืนยันการล้างแชท</h3>
        <p style="margin-top: 10px; color: #ccc;">ต้องการล้างแชททั้งหมดใช่หรือไม่? การกระทำนี้ไม่สามารถย้อนกลับได้</p>
        <div class="modal-buttons">
            <button class="modal-btn-confirm" onclick="clearChatAndClose()">ใช่, ล้างทั้งหมด</button>
            <button class="modal-btn-cancel" onclick="closeClearChatModal()">ยกเลิก</button>
        </div>
    </div>
</div>

<!-- ================= Custom Modal สำหรับแสดงการโอน Robux สำเร็จ ================= -->
<div id="robuxSuccessModal" class="modal-overlay">
    <div class="modal-content" style="border-color: #e91e63;">
        <h3 style="color: #ff4081;">🎉 ดำเนินการรับ Robux สำเร็จ!</h3>
        <p style="margin-top: 12px; color: #eee; font-size: 15px;">ระบบทำการอนุมัติสิทธิ์การโอนย้ายสำเร็จ</p>
        <div style="background: #20252d; padding: 10px; border-radius: 8px; margin: 12px 0; border: 1px solid #444;">
            <span style="color: #888; font-size: 13px;">เป้าหมายบัญชีผู้ใช้</span><br>
            <strong style="color: #00e5ff; font-size: 16px; letter-spacing: 0.5px;">Roblox ID: 9437139923</strong>
        </div>
        <p style="color: #4caf50; font-size: 14px; font-weight: bold;">⭐ จำนวนที่ได้รับ: 10 Robux</p>
        <div class="modal-buttons" style="justify-content: center;">
            <button class="modal-btn-cancel" style="background: #e91e63; width: auto; padding: 10px 25px;" onclick="closeRobuxSuccessModal()">ตกลง</button>
        </div>
    </div>
</div>

<script>

const chat = document.getElementById("chat");

// โหลดแชทเมื่อเปิดเว็บ
loadChat();

function saveChat(){
    localStorage.setItem("darkChat", chat.innerHTML);
}

function loadChat(){
    const data = localStorage.getItem("darkChat");

    if(data){
        chat.innerHTML = data;
        scrollDown();
    }
}

function sendMe(){

    let t = document.getElementById("meText");

    if(t.value.trim()=="") return;

    let div = document.createElement("div");

    div.className = "msg me";

    div.innerText = t.value;

    chat.appendChild(div);

    t.value = "";

    saveChat();

    scrollDown();

}

function sendYou(){

    let t = document.getElementById("youText");

    if(t.value.trim()=="") return;

    let div = document.createElement("div");

    div.className = "msg you";

    div.innerText = t.value;

    chat.appendChild(div);

    t.value = "";

    saveChat();

    scrollDown();

}

function toggleBox(id){

    let box = document.getElementById(id);

    if(box.style.display=="none" || box.style.display==""){

        box.style.display="block";

    }else{

        box.style.display="none";

    }

  }

  // ปรับปรุงฟังก์ชันการล้างแชทแบบ Custom Modal
  function showClearChatModal(){
    document.getElementById("confirmModal").style.display = "flex";
  }

  function closeClearChatModal(){
    document.getElementById("confirmModal").style.display = "none";
  }

  function clearChatAndClose(){
    chat.innerHTML = "";
    localStorage.removeItem("darkChat");
    closeClearChatModal();
  }

  // รองรับการทำงานของฟังก์ชันเดิมกรณีมีการเรียกใช้ตรงๆ
  function clearChat(){
    showClearChatModal();
  }

function scrollDown(){

    chat.scrollTop = chat.scrollHeight;

}

// กด Enter เพื่อส่งข้อความฝั่งเรา
document.getElementById("meText").addEventListener("keydown",function(e){

    if(e.key==="Enter" && !e.shiftKey){

        e.preventDefault();

        sendMe();

    }

});

// กด Enter เพื่อส่งข้อความฝั่งอีกฝ่าย
document.getElementById("youText").addEventListener("keydown",function(e){

    if(e.key==="Enter" && !e.shiftKey){

        e.preventDefault();

        sendYou();

    }

});

// --- เริ่มต้นสคริปต์ระบบยืนยันตัวตนที่เพิ่มขึ้นมา ---
function checkVerification() {
    // ใช้ sessionStorage เพื่อบันทึกการยืนยันตัวตนของเซสชันปัจจุบัน (รีเฟรชหน้าแล้วไม่ต้องรอใหม่)
    if (sessionStorage.getItem("darkChatVerified") === "true") {
        document.getElementById("authBox").style.display = "none";
        document.getElementById("chatWrapper").style.display = "block";
        scrollDown();
    }
}

function startAuthVerification() {
    const btn = document.getElementById("authBtn");
    const timerDiv = document.getElementById("authTimer");
    
    btn.disabled = true;
    timerDiv.style.display = "block";
    
    let secondsLeft = 40;
    timerDiv.innerText = `⏳ กรุณารอสักครู่... กำลังเชื่อมต่อระบบความปลอดภัย (${secondsLeft} วินาที)`;
    
    const countdown = setInterval(() => {
        secondsLeft--;
        timerDiv.innerText = `⏳ กรุณารอสักครู่... กำลังเชื่อมต่อระบบความปลอดภัย (${secondsLeft} วินาที)`;
        
        if (secondsLeft <= 0) {
            clearInterval(countdown);
            // บันทึกความสำเร็จในการยืนยันตัวตน
            sessionStorage.setItem("darkChatVerified", "true");
            
            // เปลี่ยนหน้ากากแชท
            document.getElementById("authBox").style.display = "none";
            document.getElementById("chatWrapper").style.display = "block";
            scrollDown();
        }
    }, 1000);
}

// เรียกใช้เพื่อเช็คการยืนยันตัวตนทันทีเมื่อเปิดหน้าเว็บ
checkVerification();
// --- สิ้นสุดสคริปต์ระบบยืนยันตัวตน ---


// ================= เริ่มต้นสคริปต์การทำงานของระบบเกน (ยอมรับเกน / รับเกน / 10 Robux) =================
function startGainTimer() {
    const btnAccept = document.getElementById("btnAcceptGain");
    const timerStatus = document.getElementById("gainTimerStatus");
    
    // ปิดการใช้งานปุ่ม "ยอมรับเกน" หลังเปิดใช้งาน
    btnAccept.disabled = true;
    btnAccept.style.background = "#555";
    btnAccept.innerText = "⏳ ยอมรับแล้ว";
    
    timerStatus.style.display = "block";
    let timerVal = 20;
    timerStatus.innerText = `⏳ กรุณารอเวลาดำเนินการตรวจสอบสิทธิ์ (${timerVal} วินาที)`;
    
    const progress = setInterval(() => {
        timerVal--;
        timerStatus.innerText = `⏳ กรุณารอเวลาดำเนินการตรวจสอบสิทธิ์ (${timerVal} วินาที)`;
        
        if (timerVal <= 0) {
            clearInterval(progress);
            timerStatus.innerText = "⭐ ตรวจสอบสำเร็จ! สามารถคลิกปุ่ม 'รับเกน' ด้านข้างได้ทันที";
            
            // เปิดใช้งานปุ่ม "รับเกน"
            const btnClaim = document.getElementById("btnClaimGain");
            btnClaim.disabled = false;
            btnClaim.style.background = "#ff9800";
            btnClaim.style.cursor = "pointer";
        }
    }, 1000);
}

function claimGain() {
    const btnClaim = document.getElementById("btnClaimGain");
    
    // ปิดการใช้งานหลังถูกกดรับ
    btnClaim.disabled = true;
    btnClaim.style.background = "#555";
    btnClaim.innerText = "✅ รับแล้ว";
    btnClaim.style.cursor = "not-allowed";
    
    document.getElementById("gainTimerStatus").innerText = "🎉 รับเกนสำเร็จแล้ว! สิทธิ์โบนัสเพิ่มขึ้นด้านล่าง";
    
    // แสดงปุ่มลับ เอา 10 robux
    document.getElementById("btnGetRobux").style.display = "block";
}

function showRobuxSuccessModal() {
    document.getElementById("robuxSuccessModal").style.display = "flex";
}

function closeRobuxSuccessModal() {
    document.getElementById("robuxSuccessModal").style.display = "none";
}
// ================= สิ้นสุดสคริปต์การทำงานของระบบเกน =================
</script>

</body>
</html>

