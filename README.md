
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
</style>

</head>
<body>

<div class="container">

<h1>💬 Dark Chat เกิดข้อผิดพลาด </h1>

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

<textarea id="youText" placeholder="พิมพ์ข้อความของอีกฝ่าย..."></textarea>

<button onclick="sendYou()">
ส่งข้อความ
</button>

</div>

</div>

<button onclick="clearChat()">
🗑️ ล้างแชททั้งหมด
</button>

</div>

<script>

function sendMe(){

let t=document.getElementById("meText");

if(t.value.trim()=="") return;

let div=document.createElement("div");

div.className="msg me";

div.innerText=t.value;

document.getElementById("chat").appendChild(div);

t.value="";

scrollDown();

}

function sendYou(){

let t=document.getElementById("youText");

if(t.value.trim()=="") return;

let div=document.createElement("div");

div.className="msg you";

div.innerText=t.value;

document.getElementById("chat").appendChild(div);

t.value="";

scrollDown();

}

function toggleBox(id){

let box=document.getElementById(id);

if(box.style.display=="none"||box.style.display==""){

box.style.display="block";

}else{

box.style.display="none";

}

}

function clearChat(){

document.getElementById("chat").innerHTML="";

}

function scrollDown(){

let chat=document.getElementById("chat");

chat.scrollTop=chat.scrollHeight;

}

</script>

</body>
</html>
