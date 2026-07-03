<!DOCTYPE html>
<html lang="th" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CYBER HUB - Premium Script Store</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = { darkMode: 'class', theme: { extend: { colors: { slate: { 850: '#1e293b', 950: '#030712' } } } } }
  </script>
</head>
<body class="bg-slate-950 text-slate-100 font-sans min-h-screen flex flex-col justify-between selection:bg-purple-600 selection:text-white">

  <div id="toast-container" class="fixed top-5 right-5 z-50 flex flex-col gap-2"></div>

  <!-- Header Nav -->
  <header class="sticky top-0 z-40 bg-slate-900/90 backdrop-blur-md border-b border-slate-800 px-4 py-3">
    <div class="max-w-4xl mx-auto flex items-center justify-between">
      <div class="flex items-center gap-2">
        <div class="p-1.5 bg-gradient-to-tr from-purple-600 to-indigo-600 rounded-lg text-white font-bold">💻</div>
        <div>
          <h1 class="text-sm font-bold tracking-wider text-purple-400">CYBER HUB</h1>
          <p class="text-[8px] text-slate-500 uppercase font-semibold">Secure Cloud Store</p>
        </div>
      </div>
      <div id="nav-actions" class="text-[10px] text-slate-400">🟢 ระบบพร้อมใช้งาน</div>
    </div>
  </header>

  <!-- Main Content -->
  <main class="max-w-4xl mx-auto px-4 py-6 flex-grow w-full">
    
    <!-- PAGE 1: GATE -->
    <div id="view-gate" class="max-w-sm mx-auto mt-4">
      <div class="bg-slate-900/80 border border-slate-800 rounded-xl p-5 shadow-xl space-y-4">
        <div class="text-center">
          <span class="text-3xl">🔑</span>
          <h2 class="text-lg font-black mt-1">ระบบยืนยันคีย์ใช้งาน</h2>
          <p class="text-[10px] text-slate-400">คีย์แอดมินแยกออกปลอดภัย 100% 🔐</p>
        </div>

        <div class="bg-slate-950/80 border border-slate-850 rounded-lg p-3 text-center">
          <p class="text-[10px] text-purple-300 font-bold">💎 รับคีย์ใช้งานฟรี (Bypass)</p>
          <p class="text-[9px] text-slate-400 my-1.5">กดด้านล่างเพื่อจำลองข้ามด่านระบบสุ่มคีย์ใช้งานฟรี 24 ชม.</p>
          <button onclick="openBypass()" class="w-full bg-purple-600 hover:bg-purple-500 py-1.5 text-white font-bold text-xs rounded transition-all">เริ่มข้ามด่าน</button>
        </div>

        <div class="space-y-2">
          <input id="key-input" type="text" placeholder="วางคีย์สุ่ม หรือใส่คีย์แอดมินของคุณ..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-xs font-mono focus:outline-none focus:border-purple-500 text-center">
          <button onclick="verifyKey()" class="w-full bg-slate-100 hover:bg-white text-slate-950 font-black py-2 rounded text-xs transition-all">ตรวจสอบคีย์</button>
        </div>

        <div class="pt-4 border-t border-slate-800/60 flex gap-1.5">
          <input id="admin-key-input" type="password" placeholder="คีย์แอดมิน..." class="flex-1 px-3 py-1.5 bg-slate-950 border border-slate-850 rounded text-xs focus:outline-none">
          <button onclick="loginAdmin()" class="px-3 bg-indigo-600 hover:bg-indigo-505 text-white text-xs font-bold rounded">แอดมิน</button>
        </div>
      </div>
    </div>

        <!-- PAGE 2: HUB -->
    <div id="view-hub" class="hidden space-y-4">
      <div class="bg-slate-900/50 border border-slate-800 rounded-xl p-4 flex justify-between items-center text-xs">
        <div>
          <h2 class="font-bold text-white">⚡ ยินดีต้อนรับสู่คลังหลัก <span class="text-purple-400">CYBER HUB</span></h2>
          <p class="text-[10px] text-slate-400">คุณสามารถค้นหาและคัดลอกสคริปต์ไปใช้แอปเกมของคุณได้ทันที</p>
        </div>
        <div class="bg-slate-950 px-3 py-1.5 border border-slate-800 rounded-lg text-center font-bold">
          <p class="text-[8px] text-slate-500 uppercase">สิทธิ์</p>
          <p id="role-badge" class="text-emerald-400 text-[10px]">USER</p>
        </div>
      </div>

      <div class="flex gap-2">
        <input id="search-input" oninput="renderHub()" type="text" placeholder="ค้นหาชื่อเกมหรือสคริปต์..." class="flex-1 px-3 py-2 bg-slate-900/60 border border-slate-850 rounded-lg text-xs focus:outline-none">
        <div id="filters" class="flex gap-1 overflow-x-auto no-scrollbar"></div>
      </div>

      <div id="scripts-grid" class="grid grid-cols-1 md:grid-cols-3 gap-3"></div>
    </div>

    <!-- PAGE 3: ADMIN -->
    <div id="view-admin" class="hidden space-y-4">
      <div class="bg-slate-900 border border-indigo-500/20 rounded-xl p-4 flex justify-between items-center text-xs">
        <h2 class="font-bold text-white">🛠️ แผงอัปโหลดและจัดการสคริปต์ (Admin Mode)</h2>
        <span class="text-[9px] bg-indigo-500/20 text-indigo-300 px-2 py-0.5 rounded font-black">OWNER</span>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <form onsubmit="saveScript(event)" class="bg-slate-900/40 border border-slate-850 rounded-xl p-4 space-y-2.5 text-xs">
          <input type="hidden" id="form-id">
          <input type="text" id="form-title" placeholder="ชื่อสคริปต์..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100" required>
          <div class="grid grid-cols-2 gap-2">
            <input type="text" id="form-game" placeholder="เกม..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100" required>
            <input type="text" id="form-cat" placeholder="หมวดหมู่..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100">
          </div>
          <textarea id="form-desc" placeholder="คำอธิบาย..." rows="2" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100 resize-none"></textarea>
          <textarea id="form-code" placeholder="โค้ดสคริปต์ (Lua Code)..." rows="4" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-[9px] font-mono text-slate-100" required></textarea>
          <div class="flex gap-1.5"><button type="submit" class="flex-1 py-1.5 bg-indigo-600 text-white font-bold rounded">บันทึก</button><button type="button" id="btn-cancel" onclick="resetForm()" class="hidden px-2.5 bg-slate-800 text-slate-400 rounded">ยกเลิก</button></div>
        </form>

        <div class="md:col-span-2 space-y-3">
          <div class="bg-slate-900/30 border border-slate-850 rounded-xl p-3 overflow-x-auto">
            <table class="w-full text-left text-xs text-slate-400">
              <thead class="text-slate-500 border-b border-slate-800 bg-slate-950/20"><tr><th class="p-2">สคริปต์</th><th class="p-2">เกม</th><th class="p-2 text-right">เครื่องมือ</th></tr></thead>
              <tbody id="admin-list"></tbody>
            </table>
          </div>
          <div class="bg-slate-900/30 border border-slate-850 rounded-xl p-3">
            <h4 class="text-xs font-bold text-slate-300 border-b border-slate-800 pb-1.5 mb-2">🔑 ทะเบียนรายชื่อคีย์สุ่มทั่วไป (ใช้งานได้)</h4>
            <div id="keys-list" class="space-y-1.5 max-h-24 overflow-y-auto pr-1"></div>
          </div>
        </div>
      </div>
    </div>
  </main>

  <!-- MODALS -->
  <div id="modal-bypass" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-purple-500/20 rounded-xl p-5 w-full max-w-xs text-center space-y-3">
      <div id="step-1">
        <h3 class="text-xs font-bold text-white">ขั้นตอนข้ามด่านรับคีย์</h3>
        <p class="text-[10px] text-slate-400 my-2">ระบบความปลอดภัยจะประมวลผลสร้างคีย์สุ่มเป็นเวลา 5 วินาที</p>
        <button onclick="startTimer()" class="px-4 py-1.5 bg-purple-600 text-white text-xs font-bold rounded">ยืนยันตัวตน</button>
      </div>
      <div id="step-2" class="hidden">
        <p class="text-xs text-slate-400">กำลังสุ่มรหัสคีย์เข้าระบบ: <span id="countdown" class="font-bold text-purple-400">5</span> วินาที</p>
      </div>
      <div id="step-3" class="space-y-2.5 hidden">
        <div class="bg-slate-950 p-2 border border-emerald-500/20 rounded flex justify-between items-center"><code id="result-key" class="text-emerald-400 font-mono text-xs font-black"></code><button onclick="copyKey()" class="px-2 py-0.5 bg-slate-800 text-[9px] rounded">คัดลอก</button></div>
        <button onclick="autoFill()" class="w-full py-1.5 bg-slate-100 text-slate-900 font-bold text-xs rounded">กรอกอัตโนมัติ</button>
      </div>
    </div>
  </div>

  <div id="modal-view" class="fixed inset-0 z-50 bg-black/85 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-xl p-4 w-full max-w-sm space-y-3">
      <div class="flex justify-between border-b border-slate-800 pb-1.5"><span id="view-tag" class="text-[10px] font-black text-purple-400"></span><button onclick="closeView()" class="text-slate-400">✕</button></div>
      <h3 id="view-title" class="text-xs font-bold text-white"></h3>
      <p id="view-desc" class="text-[11px] text-slate-400"></p>
      <div class="bg-slate-950 p-2.5 rounded font-mono text-[9px] relative max-h-28 overflow-y-auto"><button id="btn-copy-code" class="absolute top-1.5 right-1.5 px-1.5 py-0.5 bg-purple-600 text-[8px] text-white rounded">Copy</button><pre><code id="view-code"></code></pre></div>
    </div>
  </div>

  <div id="modal-delete-confirm" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 w-full max-w-xs text-center space-y-4">
      <h3 class="text-xs font-bold text-white">ต้องการลบสคริปต์นี้ใช่หรือไม่?</h3>
      <div class="flex gap-2"><button id="btn-confirm-delete" class="flex-1 py-1.5 bg-red-600 text-white text-xs font-bold rounded-lg">ยืนยัน</button><button onclick="closeDeleteModal()" class="flex-1 py-1.5 bg-slate-800 text-slate-300 text-xs rounded-lg">ยกเลิก</button></div>
    </div>
  </div>

      <!-- PAGE 2: HUB -->
    <div id="view-hub" class="hidden space-y-4">
      <div class="bg-slate-900/50 border border-slate-800 rounded-xl p-4 flex justify-between items-center text-xs">
        <div>
          <h2 class="font-bold text-white">⚡ ยินดีต้อนรับสู่คลังหลัก <span class="text-purple-400">CYBER HUB</span></h2>
          <p class="text-[10px] text-slate-400">คุณสามารถค้นหาและคัดลอกสคริปต์ไปใช้แอปเกมของคุณได้ทันที</p>
        </div>
        <div class="bg-slate-950 px-3 py-1.5 border border-slate-800 rounded-lg text-center font-bold">
          <p class="text-[8px] text-slate-500 uppercase">สิทธิ์</p>
          <p id="role-badge" class="text-emerald-400 text-[10px]">USER</p>
        </div>
      </div>

      <div class="flex gap-2">
        <input id="search-input" oninput="renderHub()" type="text" placeholder="ค้นหาชื่อเกมหรือสคริปต์..." class="flex-1 px-3 py-2 bg-slate-900/60 border border-slate-850 rounded-lg text-xs focus:outline-none">
        <div id="filters" class="flex gap-1 overflow-x-auto no-scrollbar"></div>
      </div>

      <div id="scripts-grid" class="grid grid-cols-1 md:grid-cols-3 gap-3"></div>
    </div>

    <!-- PAGE 3: ADMIN -->
    <div id="view-admin" class="hidden space-y-4">
      <div class="bg-slate-900 border border-indigo-500/20 rounded-xl p-4 flex justify-between items-center text-xs">
        <h2 class="font-bold text-white">🛠️ แผงอัปโหลดและจัดการสคริปต์ (Admin Mode)</h2>
        <span class="text-[9px] bg-indigo-500/20 text-indigo-300 px-2 py-0.5 rounded font-black">OWNER</span>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <form onsubmit="saveScript(event)" class="bg-slate-900/40 border border-slate-850 rounded-xl p-4 space-y-2.5 text-xs">
          <input type="hidden" id="form-id">
          <input type="text" id="form-title" placeholder="ชื่อสคริปต์..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100" required>
          <div class="grid grid-cols-2 gap-2">
            <input type="text" id="form-game" placeholder="เกม..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100" required>
            <input type="text" id="form-cat" placeholder="หมวดหมู่..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100">
          </div>
          <textarea id="form-desc" placeholder="คำอธิบาย..." rows="2" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100 resize-none"></textarea>
          <textarea id="form-code" placeholder="โค้ดสคริปต์ (Lua Code)..." rows="4" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-[9px] font-mono text-slate-100" required></textarea>
          <div class="flex gap-1.5"><button type="submit" class="flex-1 py-1.5 bg-indigo-600 text-white font-bold rounded">บันทึก</button><button type="button" id="btn-cancel" onclick="resetForm()" class="hidden px-2.5 bg-slate-800 text-slate-400 rounded">ยกเลิก</button></div>
        </form>

        <div class="md:col-span-2 space-y-3">
          <div class="bg-slate-900/30 border border-slate-850 rounded-xl p-3 overflow-x-auto">
            <table class="w-full text-left text-xs text-slate-400">
              <thead class="text-slate-500 border-b border-slate-800 bg-slate-950/20"><tr><th class="p-2">สคริปต์</th><th class="p-2">เกม</th><th class="p-2 text-right">เครื่องมือ</th></tr></thead>
              <tbody id="admin-list"></tbody>
            </table>
          </div>
          <div class="bg-slate-900/30 border border-slate-850 rounded-xl p-3">
            <h4 class="text-xs font-bold text-slate-300 border-b border-slate-800 pb-1.5 mb-2">🔑 ทะเบียนรายชื่อคีย์สุ่มทั่วไป (ใช้งานได้)</h4>
            <div id="keys-list" class="space-y-1.5 max-h-24 overflow-y-auto pr-1"></div>
          </div>
        </div>
      </div>
    </div>
  </main>

  <!-- MODALS -->
  <div id="modal-bypass" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-purple-500/20 rounded-xl p-5 w-full max-w-xs text-center space-y-3">
      <div id="step-1">
        <h3 class="text-xs font-bold text-white">ขั้นตอนข้ามด่านรับคีย์</h3>
        <p class="text-[10px] text-slate-400 my-2">ระบบความปลอดภัยจะประมวลผลสร้างคีย์สุ่มเป็นเวลา 5 วินาที</p>
        <button onclick="startTimer()" class="px-4 py-1.5 bg-purple-600 text-white text-xs font-bold rounded">ยืนยันตัวตน</button>
      </div>
      <div id="step-2" class="hidden">
        <p class="text-xs text-slate-400">กำลังสุ่มรหัสคีย์เข้าระบบ: <span id="countdown" class="font-bold text-purple-400">5</span> วินาที</p>
      </div>
      <div id="step-3" class="space-y-2.5 hidden">
        <div class="bg-slate-950 p-2 border border-emerald-500/20 rounded flex justify-between items-center"><code id="result-key" class="text-emerald-400 font-mono text-xs font-black"></code><button onclick="copyKey()" class="px-2 py-0.5 bg-slate-800 text-[9px] rounded">คัดลอก</button></div>
        <button onclick="autoFill()" class="w-full py-1.5 bg-slate-100 text-slate-900 font-bold text-xs rounded">กรอกอัตโนมัติ</button>
      </div>
    </div>
  </div>

  <div id="modal-view" class="fixed inset-0 z-50 bg-black/85 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-xl p-4 w-full max-w-sm space-y-3">
      <div class="flex justify-between border-b border-slate-800 pb-1.5"><span id="view-tag" class="text-[10px] font-black text-purple-400"></span><button onclick="closeView()" class="text-slate-400">✕</button></div>
      <h3 id="view-title" class="text-xs font-bold text-white"></h3>
      <p id="view-desc" class="text-[11px] text-slate-400"></p>
      <div class="bg-slate-950 p-2.5 rounded font-mono text-[9px] relative max-h-28 overflow-y-auto"><button id="btn-copy-code" class="absolute top-1.5 right-1.5 px-1.5 py-0.5 bg-purple-600 text-[8px] text-white rounded">Copy</button><pre><code id="view-code"></code></pre></div>
    </div>
  </div>

  <div id="modal-delete-confirm" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 w-full max-w-xs text-center space-y-4">
      <h3 class="text-xs font-bold text-white">ต้องการลบสคริปต์นี้ใช่หรือไม่?</h3>
      <div class="flex gap-2"><button id="btn-confirm-delete" class="flex-1 py-1.5 bg-red-600 text-white text-xs font-bold rounded-lg">ยืนยัน</button><button onclick="closeDeleteModal()" class="flex-1 py-1.5 bg-slate-800 text-slate-300 text-xs rounded-lg">ยกเลิก</button></div>
    </div>
  </div>

      <!-- PAGE 2: HUB -->
    <div id="view-hub" class="hidden space-y-4">
      <div class="bg-slate-900/50 border border-slate-800 rounded-xl p-4 flex justify-between items-center text-xs">
        <div>
          <h2 class="font-bold text-white">⚡ ยินดีต้อนรับสู่คลังหลัก <span class="text-purple-400">CYBER HUB</span></h2>
          <p class="text-[10px] text-slate-400">คุณสามารถค้นหาและคัดลอกสคริปต์ไปใช้แอปเกมของคุณได้ทันที</p>
        </div>
        <div class="bg-slate-950 px-3 py-1.5 border border-slate-800 rounded-lg text-center font-bold">
          <p class="text-[8px] text-slate-500 uppercase">สิทธิ์</p>
          <p id="role-badge" class="text-emerald-400 text-[10px]">USER</p>
        </div>
      </div>

      <div class="flex gap-2">
        <input id="search-input" oninput="renderHub()" type="text" placeholder="ค้นหาชื่อเกมหรือสคริปต์..." class="flex-1 px-3 py-2 bg-slate-900/60 border border-slate-850 rounded-lg text-xs focus:outline-none">
        <div id="filters" class="flex gap-1 overflow-x-auto no-scrollbar"></div>
      </div>

      <div id="scripts-grid" class="grid grid-cols-1 md:grid-cols-3 gap-3"></div>
    </div>

    <!-- PAGE 3: ADMIN -->
    <div id="view-admin" class="hidden space-y-4">
      <div class="bg-slate-900 border border-indigo-500/20 rounded-xl p-4 flex justify-between items-center text-xs">
        <h2 class="font-bold text-white">🛠️ แผงอัปโหลดและจัดการสคริปต์ (Admin Mode)</h2>
        <span class="text-[9px] bg-indigo-500/20 text-indigo-300 px-2 py-0.5 rounded font-black">OWNER</span>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <form onsubmit="saveScript(event)" class="bg-slate-900/40 border border-slate-850 rounded-xl p-4 space-y-2.5 text-xs">
          <input type="hidden" id="form-id">
          <input type="text" id="form-title" placeholder="ชื่อสคริปต์..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100" required>
          <div class="grid grid-cols-2 gap-2">
            <input type="text" id="form-game" placeholder="เกม..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100" required>
            <input type="text" id="form-cat" placeholder="หมวดหมู่..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100">
          </div>
          <textarea id="form-desc" placeholder="คำอธิบาย..." rows="2" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-slate-100 resize-none"></textarea>
          <textarea id="form-code" placeholder="โค้ดสคริปต์ (Lua Code)..." rows="4" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded text-[9px] font-mono text-slate-100" required></textarea>
          <div class="flex gap-1.5"><button type="submit" class="flex-1 py-1.5 bg-indigo-600 text-white font-bold rounded">บันทึก</button><button type="button" id="btn-cancel" onclick="resetForm()" class="hidden px-2.5 bg-slate-800 text-slate-400 rounded">ยกเลิก</button></div>
        </form>

        <div class="md:col-span-2 space-y-3">
          <div class="bg-slate-900/30 border border-slate-850 rounded-xl p-3 overflow-x-auto">
            <table class="w-full text-left text-xs text-slate-400">
              <thead class="text-slate-500 border-b border-slate-800 bg-slate-950/20"><tr><th class="p-2">สคริปต์</th><th class="p-2">เกม</th><th class="p-2 text-right">เครื่องมือ</th></tr></thead>
              <tbody id="admin-list"></tbody>
            </table>
          </div>
          <div class="bg-slate-900/30 border border-slate-850 rounded-xl p-3">
            <h4 class="text-xs font-bold text-slate-300 border-b border-slate-800 pb-1.5 mb-2">🔑 ทะเบียนรายชื่อคีย์สุ่มทั่วไป (ใช้งานได้)</h4>
            <div id="keys-list" class="space-y-1.5 max-h-24 overflow-y-auto pr-1"></div>
          </div>
        </div>
      </div>
    </div>
  </main>

  <!-- MODALS -->
  <div id="modal-bypass" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-purple-500/20 rounded-xl p-5 w-full max-w-xs text-center space-y-3">
      <div id="step-1">
        <h3 class="text-xs font-bold text-white">ขั้นตอนข้ามด่านรับคีย์</h3>
        <p class="text-[10px] text-slate-400 my-2">ระบบความปลอดภัยจะประมวลผลสร้างคีย์สุ่มเป็นเวลา 5 วินาที</p>
        <button onclick="startTimer()" class="px-4 py-1.5 bg-purple-600 text-white text-xs font-bold rounded">ยืนยันตัวตน</button>
      </div>
      <div id="step-2" class="hidden">
        <p class="text-xs text-slate-400">กำลังสุ่มรหัสคีย์เข้าระบบ: <span id="countdown" class="font-bold text-purple-400">5</span> วินาที</p>
      </div>
      <div id="step-3" class="space-y-2.5 hidden">
        <div class="bg-slate-950 p-2 border border-emerald-500/20 rounded flex justify-between items-center"><code id="result-key" class="text-emerald-400 font-mono text-xs font-black"></code><button onclick="copyKey()" class="px-2 py-0.5 bg-slate-800 text-[9px] rounded">คัดลอก</button></div>
        <button onclick="autoFill()" class="w-full py-1.5 bg-slate-100 text-slate-900 font-bold text-xs rounded">กรอกอัตโนมัติ</button>
      </div>
    </div>
  </div>

  <div id="modal-view" class="fixed inset-0 z-50 bg-black/85 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-xl p-4 w-full max-w-sm space-y-3">
      <div class="flex justify-between border-b border-slate-800 pb-1.5"><span id="view-tag" class="text-[10px] font-black text-purple-400"></span><button onclick="closeView()" class="text-slate-400">✕</button></div>
      <h3 id="view-title" class="text-xs font-bold text-white"></h3>
      <p id="view-desc" class="text-[11px] text-slate-400"></p>
      <div class="bg-slate-950 p-2.5 rounded font-mono text-[9px] relative max-h-28 overflow-y-auto"><button id="btn-copy-code" class="absolute top-1.5 right-1.5 px-1.5 py-0.5 bg-purple-600 text-[8px] text-white rounded">Copy</button><pre><code id="view-code"></code></pre></div>
    </div>
  </div>

  <div id="modal-delete-confirm" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 w-full max-w-xs text-center space-y-4">
      <h3 class="text-xs font-bold text-white">ต้องการลบสคริปต์นี้ใช่หรือไม่?</h3>
      <div class="flex gap-2"><button id="btn-confirm-delete" class="flex-1 py-1.5 bg-red-600 text-white text-xs font-bold rounded-lg">ยืนยัน</button><button onclick="closeDeleteModal()" class="flex-1 py-1.5 bg-slate-800 text-slate-300 text-xs rounded-lg">ยกเลิก</button></div>
    </div>
  </div>

    <footer class="py-3 text-center text-[8px] text-slate-600 border-t border-slate-900"><p>© 2026 CYBER HUB STORE.</p></footer>

      <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
    import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
    import { getFirestore, doc, setDoc, getDoc, collection, updateDoc, deleteDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

    // ==========================================
    // 🔒 ตั้งค่าคีย์ผู้พัฒนาส่วนตัวของคุณที่ตรงนี้!
    // (คนทั่วไปที่แอบดูโค้ดบน GitHub จะเห็นช่องว่างเปล่าๆ นี้ ทำให้ระบบปลอดภัยสูงสุด)
    // ==========================================
    const state = { 
      view: 'gate', 
      auth: false, 
      admin: false, 
      masterKeys: ["15466675546844776", "153227753"], // แก้ไขหรือใส่คีย์ส่วนตัวของคุณในฟันหนูนี้ได้เลยครับ!
      keys: [], 
      activeGame: 'All', 
      scripts: []
    };

    let timer, count = 5, genKey = '';
    let db = null, authInstance = null, useCloud = false;
    const appId = 'cyber-hub-app';

    const startAppEngine = async () => {
      try {
        if (typeof __firebase_config !== 'undefined' && __firebase_config) {
          const firebaseConfig = JSON.parse(__firebase_config);
          const app = initializeApp(firebaseConfig);
          authInstance = getAuth(app);
          db = getFirestore(app);
          useCloud = true;

          document.getElementById('nav-actions').innerHTML = `<span class="text-emerald-400">🟢 ระบบซิงค์คลาวด์เรียบร้อย</span>`;

          if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) { 
            await signInWithCustomToken(authInstance, __initial_auth_token); 
          } else { 
            await signInAnonymously(authInstance); 
          }

          onAuthStateChanged(authInstance, async (user) => { 
            if (user) { 
              syncScriptsFromCloud(); 
              syncKeysFromCloud(); 
            } 
          });
        } else {
          loadOfflineEngine();
        }
      } catch (e) {
        loadOfflineEngine();
      }
    };

    function loadOfflineEngine() {
      useCloud = false;
      document.getElementById('nav-actions').innerHTML = `🔵 โหมดจำลองในเครื่อง`;
      const localScripts = localStorage.getItem('cyber_hub_scripts');
      const localKeys = localStorage.getItem('cyber_hub_keys');
      if (localScripts) {
        state.scripts = JSON.parse(localScripts);
      } else {
        state.scripts = [
          { id: 1, title: 'Blox Fruits Auto Farm Hub', game: 'Blox Fruits', category: 'Main Quest', description: 'สคริปต์ช่วยฟาร์มเลเวลแบบออโต้ ฟาร์มเสถียรที่สุด ปลอดภัยสูง', code: 'loadstring(game:HttpGet("https://raw.githubusercontent.com/CyberHubRoblox/main/bloxfruits.lua"))()' }
        ];
      }
      if (localKeys) state.keys = JSON.parse(localKeys);
      initHub();
    }

    function syncScriptsFromCloud() {
      onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'scripts'), (snapshot) => {
        const cloudScripts = [];
        snapshot.forEach(doc => { cloudScripts.push({ docId: doc.id, ...doc.data() }); });
        state.scripts = cloudScripts.sort((a, b) => b.id - a.id);
        initHub();
      });
    }

    function syncKeysFromCloud() {
      onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'keys'), (snapshot) => {
        const cloudKeys = [];
        snapshot.forEach(doc => { cloudKeys.push({ docId: doc.id, ...doc.data() }); });
        state.keys = cloudKeys; renderAdmin();
      });
    }

    const showToast = (msg, err = false) => {
      const el = document.createElement('div');
      el.className = `px-3 py-1.5 rounded text-[10px] font-bold border shadow ${err ? 'bg-red-950 text-red-300 border-red-500/20' : 'bg-emerald-950 text-emerald-300 border-emerald-500/20'}`;
      el.innerText = msg; document.getElementById('toast-container').appendChild(el);
      setTimeout(() => el.remove(), 2000);
    };

    const setView = (v) => {
      state.view = v;
      ['gate', 'hub', 'admin'].forEach(x => document.getElementById('view-' + x).classList.add('hidden'));
      document.getElementById('view-' + v).classList.remove('hidden');
      renderNav();
    };

    const renderNav = () => {
      const container = document.getElementById('nav-actions');
      if (!state.auth) return;
      container.innerHTML = `
        <button onclick="setView('hub')" class="px-2 py-1 rounded text-[10px] font-bold ${state.view === 'hub' ? 'text-purple-400 bg-purple-500/10' : 'text-slate-400'}">คลังสคริปต์</button>
        ${state.admin ? `<button onclick="setView('admin')" class="px-2 py-1 rounded text-[10px] font-bold ${state.view === 'admin' ? 'text-indigo-400 bg-indigo-500/10' : 'text-slate-400'}">หลังบ้านแอดมิน</button>` : ''}
        <button onclick="location.reload()" class="text-red-400 text-[10px] px-2 font-bold">ออก</button>
      `;
    };

    const openBypass = () => { document.getElementById('modal-bypass').classList.remove('hidden'); ['step-1', 'step-2', 'step-3'].forEach(id => document.getElementById(id).classList.add('hidden')); document.getElementById('step-1').classList.remove('hidden'); };
    const closeBypass = () => document.getElementById('modal-bypass').classList.add('hidden');

    const startTimer = () => {
      document.getElementById('step-1').classList.add('hidden'); document.getElementById('step-2').classList.remove('hidden');
      count = 5; document.getElementById('countdown').innerText = count;
      clearInterval(timer);
      timer = setInterval(() => {
        count--; document.getElementById('countdown').innerText = count;
        if (count === 0) {
          clearInterval(timer);
          genKey = 'KEY-' + Math.random().toString(36).substring(2, 8).toUpperCase();
          
          if (useCloud) {
            setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'keys', genKey), { key: genKey, expires: Date.now() + 86400000 });
          } else {
            state.keys.push({ key: genKey, expires: Date.now() + 86400000 });
            localStorage.setItem('cyber_hub_keys', JSON.stringify(state.keys));
          }

          document.getElementById('step-2').classList.add('hidden'); document.getElementById('step-3').classList.remove('hidden');
          document.getElementById('result-key').innerText = genKey;
        }
      }, 1000);
    };

    const copyKey = () => { navigator.clipboard.writeText(genKey); showToast('คัดลอกคีย์เรียบร้อย!'); };
    const autoFill = () => { document.getElementById('key-input').value = genKey; closeBypass(); };

    const verifyKey = () => {
      const input = document.getElementById('key-input').value.trim();
      const isMaster = state.masterKeys.includes(input);
      const isNormal = state.keys.some(k => k.key === input && k.expires > Date.now());

      if (isMaster) {
        state.auth = true; state.admin = true; setView('hub'); showToast('สิทธิ์ผู้พัฒนาสูงสุดปลดล็อกแล้ว!'); initHub();
      } else if (isNormal) {
        state.auth = true; state.admin = false; setView('hub'); showToast('ผ่านสิทธิ์คีย์สุ่มสำเร็จ!'); initHub();
      } else { showToast('คีย์ไม่ถูกต้องหรือหมดอายุ!', true); }
    };

    const loginAdmin = () => {
      const input = document.getElementById('admin-key-input').value.trim();
      if (state.masterKeys.includes(input)) {
        state.auth = true; state.admin = true; setView('admin'); showToast('ยินดีต้อนรับหลังบ้านแอดมิน!'); initHub();
      } else { showToast('รหัสผ่านไม่ถูกต้อง!', true); }
    };

    const initHub = () => {
      document.getElementById('hub-script-count').innerText = state.scripts.length;
      document.getElementById('role-badge').innerText = state.admin ? 'ADMIN ROOT' : 'USER';
      renderFilters(); renderHub(); renderAdmin();
    };

    const renderFilters = () => {
      const container = document.getElementById('filters');
      const list = ['All', ...new Set(state.scripts.map(s => s.game))];
      container.innerHTML = list.map(g => `<button onclick="window.setGame('${g}')" class="px-2 py-0.5 text-[9px] rounded font-bold ${state.activeGame === g ? 'bg-purple-600 text-white' : 'bg-slate-900 text-slate-400'}">${g === 'All' ? 'ทั้งหมด' : g}</button>`).join('');
    };

    const setGame = (g) => { state.activeGame = g; renderFilters(); renderHub(); };

    const renderHub = () => {
      const search = document.getElementById('search-input').value.toLowerCase();
      const grid = document.getElementById('scripts-grid');
      const list = state.scripts.filter(s => (s.title.toLowerCase().includes(search) || s.game.toLowerCase().includes(search)) && (state.activeGame === 'All' || s.game === state.activeGame));
      
      grid.innerHTML = list.length === 0 ? '<p class="text-[10px] text-slate-500 text-center py-4">ไม่พบสคริปต์ในระบบ</p>' : list.map(s => `
        <div class="bg-slate-900/50 border border-slate-850 p-3 rounded-lg flex flex-col justify-between">
          <div><span class="text-[8px] text-purple-400 font-bold">${s.game}</span><h3 class="text-xs font-bold text-white mt-1">${s.title}</h3></div>
          <div class="flex justify-end gap-1 pt-3 border-t border-slate-800/40 mt-3">
            <button onclick="window.viewScript(${s.id})" class="bg-slate-850 text-[9px] px-2 py-1 text-slate-200 rounded">ดูสคริปต์</button>
            <button onclick="window.copyCode(${s.id})" class="bg-purple-600 text-[9px] px-2 py-1 text-white rounded font-bold">📋 ก็อป</button>
          </div>
        </div>
      `).join('');
    };

    const copyCode = (id) => { const s = state.scripts.find(x => x.id === id); if (s) { navigator.clipboard.writeText(s.code); showToast('คัดลอกโค้ดสคริปต์เรียบร้อย!'); }};
    const viewScript = (id) => { const s = state.scripts.find(x => x.id === id); if (s) { document.getElementById('view-tag').innerText = s.game; document.getElementById('view-title').innerText = s.title; document.getElementById('view-desc').innerText = s.description || ''; document.getElementById('view-code').innerText = s.code; document.getElementById('btn-copy-code').onclick = () => copyCode(s.id); document.getElementById('modal-view').classList.remove('hidden'); }};
    const closeView = () => document.getElementById('modal-view').classList.add('hidden');

    const renderAdmin = () => {
      if (!state.admin) return;
      document.getElementById('admin-list').innerHTML = state.scripts.map(s => `
        <tr class="border-b border-slate-800/40 text-[10px]"><td class="p-2 text-white max-w-[120px] truncate">${s.title}</td><td>${s.game}</td><td class="text-right"><button onclick="window.editScript(${s.id})" class="text-indigo-400 px-1 font-bold">✏️</button><button onclick="window.deleteScript(${s.id})" class="text-red-400 px-1 font-bold">🗑️</button></td></tr>
      `).join('');
      document.getElementById('keys-list').innerHTML = state.keys.map(k => `<div class="bg-slate-950 p-1.5 border border-slate-850 rounded flex justify-between text-[10px]"><code class="text-yellow-400 font-mono font-bold">${k.key}</code><span class="text-slate-500">Active</span></div>`).join('');
    };

    const saveScript = async (e) => {
      e.preventDefault();
      const id = document.getElementById('form-id').value;
      const title = document.getElementById('form-title').value.trim();
      const game = document.getElementById('form-game').value.trim();
      const category = document.getElementById('form-cat').value.trim();
      const description = document.getElementById('form-desc').value.trim();
      const code = document.getElementById('form-code').value.trim();

      if (id) {
        if (useCloud) {
          await updateDoc(doc(db, 'artifacts', appId, 'public', 'data', 'scripts', id), { title, game, category, description, code });
        } else {
          state.scripts = state.scripts.map(s => s.id === parseInt(id) ? { ...s, title, game, category, description, code } : s);
          localStorage.setItem('cyber_hub_scripts', JSON.stringify(state.scripts));
        }
      } else {
        const newId = Date.now().toString();
        const newObj = { id: parseInt(newId), title, game, category, description, code, date: new Date().toISOString().split('T')[0] };
        if (useCloud) {
          await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'scripts', newId), newObj);
        } else {
          state.scripts.unshift(newObj);
          localStorage.setItem('cyber_hub_scripts', JSON.stringify(state.scripts));
        }
      }
      resetForm(); showToast('บันทึกสคริปต์เสร็จสิ้น!'); initHub();
    };

    const editScript = (id) => {
      const s = state.scripts.find(x => x.id === id);
      if (s) {
        document.getElementById('form-id').value = s.id; document.getElementById('form-title').value = s.title;
        document.getElementById('form-game').value = s.game; document.getElementById('form-cat').value = s.category || '';
        document.getElementById('form-desc').value = s.description || ''; document.getElementById('form-code').value = s.code;
        document.getElementById('btn-cancel').classList.remove('hidden');
      }
    };

    const resetForm = () => { document.getElementById('script-form').reset(); document.getElementById('form-id').value = ''; document.getElementById('btn-cancel').classList.add('hidden'); };
    
    const deleteScript = async (id) => { 
      if(confirm('คุณต้องการลบสคริปต์นี้ใช่หรือไม่?')) { 
        if (useCloud) {
          await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'scripts', id.toString()));
        } else {
          state.scripts = state.scripts.filter(s => s.id !== id); 
          localStorage.setItem('cyber_hub_scripts', JSON.stringify(state.scripts));
        }
        showToast('ลบสคริปต์เรียบร้อย'); 
        initHub(); 
      }
    };

    startAppEngine();

    window.setView = setView; window.openBypass = openBypass; window.closeBypass = closeBypass;
    window.startTimer = startTimer; window.copyKey = copyKey; window.autoFill = autoFill;
    window.verifyKey = verifyKey; window.loginAdmin = loginAdmin; window.setGame = setGame;
    window.copyCode = copyCode; window.viewScript = viewScript; window.closeView = closeView;
    window.saveScript = saveScript; window.editScript = editScript; window.resetForm = resetForm;
    window.deleteScript = deleteScript;
  </script>
</body>
</html>

  
  
