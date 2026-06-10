<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>หน้าแรก - Knowledge Hub</title>
</head>
<body class="bg-slate-50 min-h-screen flex flex-col items-center justify-center p-6">
    <div class="text-center">
        <h1 class="text-6xl font-black text-blue-600 mb-6">Learning Portal</h1>
        <p class="text-gray-600 mb-10 text-xl">แหล่งเรียนรู้ที่รวมทุกทักษะไว้ในที่เดียว</p>
        
        <div class="space-x-4">
            <button onclick="alert('ระบบ Google Login กำลังเชื่อมต่อ...')" class="bg-white border-2 border-blue-600 text-blue-600 px-8 py-3 rounded-full font-bold hover:bg-blue-50 transition">Login with Google</button>
            <a href="explore.html" class="bg-blue-600 text-white px-8 py-3 rounded-full font-bold hover:bg-blue-700 transition">สำรวจเว็บไซต์</a>
        </div>

<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>คลังความรู้ - สำรวจเว็บไซต์</title>
</head>
<body class="bg-slate-100 p-10">

    <!-- ปุ่มย้อนกลับ -->
    <a href="index.html" class="text-blue-600 font-bold mb-8 inline-block hover:underline">← กลับหน้าแรก</a>

    <h2 class="text-4xl font-bold mb-10 text-center">บทความและสาระน่ารู้</h2>

    <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-8 max-w-6xl mx-auto">
        <!-- Card 1 -->
        <div class="bg-white p-6 rounded-3xl shadow-lg border border-slate-200">
            <h3 class="text-2xl font-bold mb-3">การเขียนโค้ดเบื้องต้น</h3>
            <p class="text-slate-600">เรียนรู้วิธีการคิดแบบตรรกะและการใช้ภาษา HTML/CSS ในการสร้างเว็บไซต์ด้วยตัวเองจากศูนย์</p>
        </div>

        <!-- Card 2 -->
        <div class="bg-white p-6 rounded-3xl shadow-lg border border-slate-200">
            <h3 class="text-2xl font-bold mb-3">เคล็ดลับการออกแบบ UI</h3>
            <p class="text-slate-600">สี ฟอนต์ และระยะห่าง (Spacing) คือหัวใจสำคัญที่จะทำให้เว็บไซต์ของคุณดูเป็นมืออาชีพ</p>
        </div>

        <!-- Card 3 -->
        <div class="bg-white p-6 rounded-3xl shadow-lg border border-slate-200">
            <h3 class="text-2xl font-bold mb-3">ความปลอดภัยในโลกออนไลน์</h3>
            <p class="text-slate-600">รู้วิธีป้องกันข้อมูลส่วนตัวและการใช้งาน Google Login อย่างปลอดภัยสำหรับผู้พัฒนาเว็บ</p>
        </div>
    </div>
</body>
</html>
