<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Knowledge Hub - แพลตฟอร์มแบ่งปันความรู้</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-50 text-gray-900">

    <nav class="flex justify-between items-center py-6 px-10 bg-white shadow-sm">
        <h1 class="text-2xl font-bold text-blue-600">KnowledgeHub</h1>
        <div class="space-x-4">
            <button onclick="window.location.href='index.html'" class="text-gray-600 hover:text-blue-600 font-medium">หน้าแรก</button>
            <button onclick="alert('กำลังนำท่านไปยังระบบ Login Google...')" class="bg-blue-600 text-white px-5 py-2 rounded-full hover:bg-blue-700 transition">Login Google</button>
        </div>
    </nav>

    <main class="max-w-6xl mx-auto mt-12 px-6">
        <section class="text-center mb-16">
            <h2 class="text-5xl font-extrabold mb-4">ยินดีต้อนรับสู่โลกแห่งการเรียนรู้</h2>
            <p class="text-gray-600 text-lg mb-8">สำรวจเนื้อหาที่คุณสนใจและพัฒนาทักษะไปพร้อมกับเรา</p>
            <button onclick="document.getElementById('explore').scrollIntoView({behavior: 'smooth'})" class="bg-gray-900 text-white px-8 py-3 rounded-xl hover:bg-gray-800 transition">สำรวจเว็บไซต์</button>
        </section>

        <section id="explore" class="grid md:grid-cols-3 gap-8">
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 hover:shadow-md transition">
                <div class="text-4xl mb-4">💡</div>
                <h3 class="text-xl font-bold mb-2">พื้นฐานการเขียนโปรแกรม</h3>
                <p class="text-gray-600 text-sm">เรียนรู้หลักการคิดแบบอัลกอริทึมและภาษาเริ่มต้นสำหรับนักพัฒนา</p>
            </div>
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 hover:shadow-md transition">
                <div class="text-4xl mb-4">🎨</div>
                <h3 class="text-xl font-bold mb-2">ศิลปะแห่งการดีไซน์ UI</h3>
                <p class="text-gray-600 text-sm">เจาะลึกการวาง Layout และเลือกคู่สีให้เว็บไซต์ดูแพงและน่าใช้งาน</p>
            </div>
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 hover:shadow-md transition">
                <div class="text-4xl mb-4">🚀</div>
                <h3 class="text-xl font-bold mb-2">ทักษะธุรกิจยุคใหม่</h3>
                <p class="text-gray-600 text-sm">เรียนรู้กลยุทธ์การเติบโตและการจัดการโปรเจกต์ในยุคดิจิทัล</p>
            </div>
        </section>
    </main>

</body>
</html>
