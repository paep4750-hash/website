<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>My Personal Web</title>
</head>
<body class="bg-gray-50">

    <div id="home" class="p-8 text-center min-h-screen flex flex-col justify-center">
        <h1 class="text-4xl font-bold mb-6">ยินดีต้อนรับสู่เว็บไซต์ของฉัน</h1>
        <div class="space-y-4">
            <button class="block w-full max-w-sm mx-auto bg-blue-600 text-white py-3 rounded-xl font-bold">Login with Google</button>
            <button onclick="showExplore()" class="block w-full max-w-sm mx-auto bg-gray-800 text-white py-3 rounded-xl font-bold">สำรวจเว็บไซต์</button>
        </div>
    </div>

    <div id="explore" class="hidden p-8">
        <button onclick="showHome()" class="text-blue-600 mb-6 font-semibold">← ย้อนกลับหน้าแรก</button>
        <h2 class="text-2xl font-bold mb-6">คลังความรู้</h2>
        <div class="space-y-4">
            <div class="bg-white p-4 rounded-lg shadow">ความรู้ที่ 1: การใช้แท็บเล็ตเขียนโปรแกรม</div>
            <div class="bg-white p-4 rounded-lg shadow">ความรู้ที่ 2: พื้นฐาน HTML บนมือถือ</div>
        </div>
    </div>

    <script>
        function showExplore() {
            document.getElementById('home').classList.add('hidden');
            document.getElementById('explore').classList.remove('hidden');
        }
        function showHome() {
            document.getElementById('explore').classList.add('hidden');
            document.getElementById('home').classList.remove('hidden');
        }
    </script>
</body>
</html>
