<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>Knowledge Portal</title>
</head>
<body class="bg-gray-50 text-gray-800">

    <div id="home" class="flex flex-col items-center justify-center min-h-screen p-6 text-center">
        <h1 class="text-5xl font-bold mb-8">Welcome</h1>
        <div class="flex gap-4">
            <button class="bg-white border border-gray-300 text-gray-500 px-6 py-2 rounded-lg text-sm cursor-default">Login Google</button>
            <button onclick="showPage('explore')" class="bg-blue-600 text-white px-6 py-2 rounded-lg text-sm hover:bg-blue-700 transition">Explore</button>
        </div>
    </div>

    <div id="explore" class="hidden p-6 max-w-4xl mx-auto">
        <button onclick="showPage('home')" class="text-blue-600 text-sm font-semibold mb-8 hover:underline">← Home</button>
        
        <h2 class="text-3xl font-bold mb-8">Knowledge Base</h2>
        
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div class="bg-white p-6 rounded-xl border border-gray-200">
                <h3 class="font-bold mb-2">Digital Strategy</h3>
                <p class="text-sm text-gray-600">การวางกลยุทธ์ในยุคดิจิทัลต้องอาศัยการวิเคราะห์ข้อมูลเชิงลึกและการปรับตัวตามกระแสโลกอย่างรวดเร็ว</p>
            </div>
            <div class="bg-white p-6 rounded-xl border border-gray-200">
                <h3 class="font-bold mb-2">Advanced UI Design</h3>
                <p class="text-sm text-gray-600">หลักการออกแบบ User Interface ไม่ใช่แค่ความสวยงาม แต่คือการสร้างประสบการณ์ใช้งานที่ลื่นไหลให้แก่ผู้ใช้</p>
            </div>
            <div class="bg-white p-6 rounded-xl border border-gray-200">
                <h3 class="font-bold mb-2">Data Analytics</h3>
                <p class="text-sm text-gray-600">การเปลี่ยนข้อมูลดิบให้กลายเป็นความเข้าใจ เพื่อใช้ตัดสินใจเชิงธุรกิจที่มีประสิทธิภาพแม่นยำ</p>
            </div>
            <div class="bg-white p-6 rounded-xl border border-gray-200">
                <h3 class="font-bold mb-2">Growth Hacking</h3>
                <p class="text-sm text-gray-600">เทคนิคการเร่งการเติบโตขององค์กรผ่านการทดสอบและการวัดผลที่รวดเร็ว (Rapid Experimentation)</p>
            </div>
            <div class="bg-white p-6 rounded-xl border border-gray-200">
                <h3 class="font-bold mb-2">Global Trends</h3>
                <p class="text-sm text-gray-600">อัปเดตเทคโนโลยีและเศรษฐกิจโลก เพื่อให้ทันต่อการเปลี่ยนแปลงที่เกิดขึ้นในทุกๆ วัน</p>
            </div>
            <div class="bg-white p-6 rounded-xl border border-gray-200">
                <h3 class="font-bold mb-2">Modern Marketing</h3>
                <p class="text-sm text-gray-600">การเข้าถึงกลุ่มเป้าหมายในยุคที่ข้อมูลมีมากมาย การสร้างตัวตนให้โดดเด่นถือเป็นหัวใจสำคัญ</p>
            </div>
        </div>
    </div>

    <script>
        function showPage(pageId) {
            document.getElementById('home').classList.add('hidden');
            document.getElementById('explore').classList.add('hidden');
            document.getElementById(pageId).classList.remove('hidden');
        }
    </script>
</body>
</html>
