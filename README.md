<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<title>Gemini Access Center</title>
<link rel="stylesheet" href="style.css">
</head>

<body>

<div class="box">

<h1>🤖 Gemini Access Center</h1>

<p>
ระบบทดสอบสิทธิ์เข้าใช้งาน AI รุ่นลับ
</p>

<div id="loginPage">

<button onclick="login()">
🔑 Login
</button>

</div>

<div id="registerPage" style="display:none">

<h2>ล็อกอินสำเร็จ</h2>

<button onclick="registerUser()">
📝 Register
</button>

</div>

<div id="waiting" style="display:none">

<h2>⏳ กำลังส่งข้อมูล...</h2>

<p id="timer"></p>

</div>

<div id="result" style="display:none">

<h2 id="status"></h2>

<p id="message"></p>

</div>

</div>

<script src="script.js"></script>

</body>
</html>

body{

margin:0;
background:linear-gradient(135deg,#0f172a,#1e3a8a);

font-family:Arial;
color:white;
text-align:center;

}

.box{

margin:auto;
margin-top:80px;

width:400px;

background:#202020;

padding:30px;

border-radius:20px;

box-shadow:0 0 30px cyan;

}

button{

padding:15px 35px;

font-size:18px;

border:none;

border-radius:10px;

background:#00bfff;

color:white;

cursor:pointer;

transition:.3s;

}

button:hover{

transform:scale(1.08);

background:#00ffff;

}

function login(){

document.getElementById("loginPage").style.display="none";

document.getElementById("registerPage").style.display="block";

}

function registerUser(){

document.getElementById("registerPage").style.display="none";

document.getElementById("waiting").style.display="block";

// 1 ชั่วโมง
let seconds=3600;

// ทดสอบเร็ว ๆ เปลี่ยนเป็น
// let seconds=10;

let timer=setInterval(function(){

seconds--;

let h=Math.floor(seconds/3600);
let m=Math.floor((seconds%3600)/60);
let s=seconds%60;

document.getElementById("timer").innerHTML=
h+" ชั่วโมง "+m+" นาที "+s+" วินาที";

if(seconds<=0){

clearInterval(timer);

finish();

}

},1000);

}

function finish(){

document.getElementById("waiting").style.display="none";

document.getElementById("result").style.display="block";

let pass=Math.random()<0.5;

if(pass){

document.getElementById("status").innerHTML="✅ ผ่าน";

document.getElementById("message").innerHTML=
"Gemini อนุมัติการใช้งานเรียบร้อย 😂";

}else{

document.getElementById("status").innerHTML="❌ ไม่ผ่าน";

document.getElementById("message").innerHTML=
"Gemini ตรวจพบว่าคุณ...กดเร็วเกินไป 🤣";

}

}
