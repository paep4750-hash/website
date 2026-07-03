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
  <style>
    .no-scrollbar::-webkit-scrollbar { display: none; }
    .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
  </style>
</head>
<body class="bg-slate-950 text-slate-100 font-sans antialiased min-h-screen flex flex-col justify-between selection:bg-purple-600 selection:text-white">

  <div id="toast-container" class="fixed top-5 right-5 z-50 flex flex-col gap-2"></div>

  <header class="sticky top-0 z-40 bg-slate-900/90 backdrop-blur-md border-b border-slate-800 px-4 py-4">
    <div class="max-w-6xl mx-auto flex items-center justify-between">
      <div class="flex items-center gap-3">
        <div class="p-2 bg-gradient-to-tr from-purple-600 to-indigo-600 rounded-lg text-white shadow-lg">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 9l3 3-3 3m5 0h3M5 20h14a2 2 0 002-2V6a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"></path></svg>
        </div>
        <div>
          <h1 class="text-base font-bold tracking-wider bg-clip-text text-transparent bg-gradient-to-r from-purple-400 to-indigo-300">CYBER HUB</h1>
          <p class="text-[10px] text-slate-500 uppercase tracking-widest font-semibold">Secure Cloud Store</p>
        </div>
      </div>
      <div id="nav-actions" class="flex items-center gap-2">
        <span class="text-[10px] bg-slate-900 text-slate-400 px-3 py-1.5 rounded-lg border border-slate-800 flex items-center gap-1.5">
          <svg class="w-3.5 h-3.5 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4"></path></svg>
          เชื่อมต่อฐานข้อมูลเรียบร้อย...
        </span>
      </div>
    </div>
  </header>

  <main class="max-w-6xl mx-auto px-4 py-8 flex-grow w-full">
    <div id="view-gate" class="max-w-md mx-auto mt-6">
      <div class="bg-slate-900/60 border border-slate-800 rounded-2xl p-6 shadow-2xl backdrop-blur-sm">
        <div class="text-center mb-6">
          <div class="inline-flex p-3 bg-yellow-500/10 rounded-full text-yellow-500 mb-3 border border-yellow-500/25">
            <svg class="w-8 h-8 animate-pulse" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 7a2 2 0 012 2m4 0a6 6 0 01-7.743 5.743L11 17H9v2H7v2H4a1 1 0 01-1-1v-2.586a1 1 0 01.293-.707l5.964-5.964A6 6 0 1121 9z"></path></svg>
          </div>
          <h2 class="text-xl font-extrabold tracking-tight">ระบบยืนยันคีย์ใช้งาน</h2>
          <p class="text-xs text-slate-400 mt-1">คีย์แอดมินและข้อมูลสำคัญได้รับการป้องกันสูงสุด 🔐</p>
        </div>

        <div class="bg-slate-950/60 border border-slate-850 rounded-xl p-4 mb-6">
          <div class="flex items-center justify-between mb-2.5">
            <span class="text-[10px] text-purple-400 font-bold uppercase tracking-wider flex items-center gap-1">🔑 ระบบรับคีย์ทั่วไป (Bypass Flow)</span>
          </div>
          <p class="text-[11px] text-slate-400 mb-4 leading-relaxed">คลิกด้านล่างเพื่อเข้าหน้าต่างจำลองระบบสุ่มคีย์ผ่านด่านเพื่อความปลอดภัย</p>
          <button onclick="openSimulatedBypass()" class="w-full bg-gradient-to-r from-purple-600 to-indigo-600 text-white font-semibold text-xs py-2.5 rounded-lg shadow-lg">รับคีย์ใช้งานที่นี่</button>
        </div>

        <div class="space-y-3.5">
          <input id="key-input" type="text" placeholder="กรอกคีย์ใช้งานของคุณ..." class="block w-full px-4 py-2.5 bg-slate-950 border border-slate-850 rounded-xl text-xs font-mono">
          <button onclick="verifyAccessKey()" class="w-full bg-slate-100 hover:bg-white text-slate-950 font-extrabold py-2.5 rounded-xl text-xs shadow-md">ปลดล็อกและเข้าสู่คลังหลัก</button>
        </div>

        <div class="mt-8 pt-6 border-t border-slate-800/80">
          <p class="text-[10px] text-center text-slate-500 uppercase tracking-wider mb-3">แผงควบคุมหลักผู้พัฒนา</p>
          <div class="flex gap-2">
            <input id="admin-key-input" type="password" placeholder="คีย์แอดมินส่วนตัว..." class="flex-1 px-3 py-2 bg-slate-950 border border-slate-850 rounded-lg text-xs font-mono">
            <button onclick="loginAsAdmin()" class="px-4 py-2 bg-indigo-600 text-white text-xs font-semibold rounded-lg">แอดมิน</button>
          </div>
        </div>
      </div>
    </div>

    <div id="view-hub" class="hidden space-y-6">
      <div class="bg-slate-900/40 border border-slate-800 rounded-2xl p-6 flex flex-col md:flex-row justify-between items-center gap-4">
        <div>
          <h2 class="text-lg font-black text-white">ระบบผ่านสิทธิ์ <span class="text-purple-400">CYBER HUB ONLINE</span></h2>
          <p class="text-xs text-slate-400">ข้อมูลสคริปต์เชื่อมต่อตรงกับ Persistent Cloud Database</p>
        </div>
        <div class="flex gap-3">
          <div class="bg-slate-950 border border-slate-800 px-4 py-2 rounded-xl text-center"><p class="text-[9px] text-slate-500 font-bold uppercase">สคริปต์</p><p id="hub-script-count" class="text-sm font-bold text-purple-400 font-mono">0</p></div>
          <div class="bg-slate-950 border border-slate-800 px-4 py-2 rounded-xl text-center"><p class="text-[9px] text-slate-500 font-bold uppercase">สิทธิ์</p><p id="hub-role-badge" class="text-sm font-bold text-emerald-400">USER</p></div>
        </div>
      </div>
      <div class="flex flex-col md:flex-row gap-3 justify-between">
        <input id="search-input" oninput="renderHubScripts()" type="text" placeholder="ค้นหาสคริปต์..." class="flex-1 px-4 py-2.5 bg-slate-900/60 border border-slate-850 rounded-xl text-xs focus:outline-none">
        <div id="game-filters" class="flex items-center gap-1.5 overflow-x-auto no-scrollbar"></div>
      </div>
      <div id="scripts-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"></div>
    </div>

    <div id="view-admin" class="hidden space-y-6">
      <div class="bg-gradient-to-r from-indigo-950/60 to-purple-950/40 border border-indigo-500/20 rounded-2xl p-4 flex justify-between items-center">
        <h2 class="text-sm font-bold text-white">☁️ ระบบคลาวด์สำหรับผู้พัฒนา (Cloud Owner Panel)</h2>
        <span class="px-2.5 py-1 bg-indigo-500/20 text-indigo-300 border border-indigo-500/30 rounded-lg text-[10px] font-black">ROOT CLOUD</span>
      </div>
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div class="bg-slate-900/40 border border-slate-850 rounded-2xl p-5 space-y-4">
          <h3 id="form-title" class="text-xs font-bold uppercase tracking-wider text-indigo-300 border-b border-slate-800 pb-2">ลงสคริปต์ใหม่เข้าระบบคลาวด์</h3>
          <form id="script-form" onsubmit="saveScriptData(event)" class="space-y-3">
            <input type="hidden" id="form-id">
            <input type="text" id="form-title-input" placeholder="ชื่อสคริปต์..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded-lg text-xs" required>
            <div class="grid grid-cols-2 gap-2">
              <input type="text" id="form-game-input" placeholder="ชื่อเกม..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded-lg text-xs" required>
              <input type="text" id="form-cat-input" placeholder="หมวดหมู่..." class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded-lg text-xs">
            </div>
            <textarea id="form-desc-input" placeholder="อธิบายสั้นๆ..." rows="2" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded-lg text-xs resize-none"></textarea>
            <textarea id="form-code-input" placeholder="สคริปต์โค้ด (Lua Code)..." rows="4" class="w-full px-3 py-2 bg-slate-950 border border-slate-850 rounded-lg text-[10px] font-mono" required></textarea>
            <div class="flex gap-2"><button type="submit" class="flex-1 py-2 bg-indigo-600 text-white text-xs font-bold rounded-lg shadow-md">บันทึกข้อมูล</button><button type="button" id="btn-cancel-edit" onclick="resetAdminForm()" class="hidden px-3 py-2 bg-slate-800 text-slate-400 text-xs rounded-lg">ยกเลิก</button></div>
          </form>
        </div>
        <div class="lg:col-span-2 space-y-5">
          <div class="bg-slate-900/40 border border-slate-850 rounded-2xl p-4 overflow-x-auto">
            <table class="w-full text-left text-xs text-slate-400">
              <thead class="text-slate-500 uppercase border-b border-slate-800 bg-slate-950/20"><tr><th class="py-2 px-2">ชื่อสคริปต์</th><th class="py-2 px-2">เกม</th><th class="py-2 px-2 text-right">การจัดการ</th></tr></thead>
              <tbody id="admin-scripts-list"></tbody>
            </table>
          </div>
          <div class="bg-slate-900/40 border border-slate-850 rounded-2xl p-4">
            <h3 class="text-xs font-bold text-slate-300 border-b border-slate-800 pb-2 mb-2">🔑 ทะเบียนรายชื่อคีย์สุ่มชั่วคราว (เซฟบนคลาวด์)</h3>
            <div id="active-keys-list" class="space-y-1.5 max-h-28 overflow-y-auto pr-1"></div>
          </div>
        </div>
      </div>
    </div>
  </main>

  <div id="modal-bypass" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 backdrop-blur-md hidden">
    <div class="bg-slate-900 border border-purple-500/30 rounded-2xl w-full max-w-sm p-6 space-y-4">
      <div id="bypass-step-1" class="text-center space-y-3">
        <h3 class="text-sm font-bold text-white">ขั้นตอนข้ามด่านสุ่มคีย์</h3>
        <p class="text-xs text-slate-400">ระบบจะจำลองการข้ามด่านและบันทึกคีย์ขึ้นฐานข้อมูลเป็นเวลา 5 วินาที</p>
        <button onclick="startBypassTimer()" class="px-5 py-2 bg-purple-600 text-white font-bold text-xs rounded-lg">เริ่มยืนยันตัวตน (Verify)</button>
      </div>
      <div id="bypass-step-2" class="text-center space-y-2 hidden">
        <p class="text-xs text-slate-400">กำลังประมวลผลคีย์ขึ้นระบบ: <span id="bypass-countdown" class="font-bold text-purple-400">5</span> วินาที</p>
      </div>
      <div id="bypass-step-3" class="space-y-3 hidden">
        <div class="bg-slate-950 p-3 rounded-lg border border-emerald-500/25 flex justify-between items-center"><code id="bypass-result-key" class="text-emerald-400 font-mono text-xs font-black">KEY-XXXX</code><button onclick="copyGeneratedKey()" class="px-2 py-1 bg-slate-800 text-[10px] rounded">คัดลอก</button></div>
        <button onclick="autoFillKey()" class="w-full py-2 bg-slate-100 text-slate-900 font-bold text-xs rounded-lg">กรอกลงช่องตรวจสอบอัตโนมัติ</button>
      </div>
    </div>
  </div>

  <div id="modal-script-view" class="fixed inset-0 z-50 bg-black/85 flex items-center justify-center p-4 backdrop-blur-sm hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-lg p-5 space-y-4">
      <div class="flex justify-between items-center border-b border-slate-800 pb-2"><span id="modal-tag-game" class="text-xs font-black text-purple-400"></span><button onclick="closeScriptViewModal()" class="text-slate-400 hover:text-white">✕</button></div>
      <h3 id="modal-title" class="text-sm font-bold text-white"></h3>
      <p id="modal-desc" class="text-xs text-slate-400"></p>
      <div class="bg-slate-950 p-3 rounded-lg font-mono text-[10px] max-h-36 overflow-y-auto relative"><button id="modal-btn-copy" class="absolute top-2 right-2 px-2 py-1 bg-purple-600 text-[9px] text-white rounded">Copy</button><pre><code id="modal-code"></code></pre></div>
    </div>
  </div>

  <div id="modal-delete-confirm" class="fixed inset-0 z-50 bg-black/90 flex items-center justify-center p-4 hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 w-full max-w-xs text-center space-y-4">
      <h3 class="text-xs font-bold text-white">ต้องการลบสคริปต์นี้ใช่หรือไม่?</h3>
      <div class="flex gap-2"><button id="btn-confirm-delete" class="flex-1 py-1.5 bg-red-600 text-white text-xs font-bold rounded-lg">ยืนยัน</button><button onclick="closeDeleteModal()" class="flex-1 py-1.5 bg-slate-800 text-slate-300 text-xs rounded-lg">ยกเลิก</button></div>
    </div>
  </div>

  <footer class="py-4 text-center text-[10px] text-slate-600 border-t border-slate-900"><p>© 2026 CYBER HUB STORE. Persistent Cloud Database</p></footer>
    <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
    import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
    import { getFirestore, doc, setDoc, collection, updateDoc, deleteDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

    const state = { view: 'gate', isAuthenticated: false, isAdmin: false, activeKeys: [], generatedKey: '', selectedGame: 'All', scripts: [] };
    let bypassInterval, bypassCounter = 5, deletingScriptId = null;

    const firebaseConfig = JSON.parse(__firebase_config);
    const app = initializeApp(firebaseConfig);
    const auth = getAuth(app);
    const db = getFirestore(app);
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'cyber-hub';

    const initAuth = async () => {
      if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) { await signInWithCustomToken(auth, __initial_auth_token); } 
      else { await signInAnonymously(auth); }
    };

    onAuthStateChanged(auth, (user) => { if (user) { syncScriptsFromCloud(); syncKeysFromCloud(); } });
    window.addEventListener('DOMContentLoaded', () => { initAuth(); });

    function syncScriptsFromCloud() {
      onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'scripts'), (snapshot) => {
        const cloudScripts = [];
        snapshot.forEach(doc => { cloudScripts.push({ docId: doc.id, ...doc.data() }); });
        state.scripts = cloudScripts.sort((a, b) => b.id - a.id);
        updateScriptCount(); renderGameFilters(); renderHubScripts(); renderAdminPanel();
      });
    }

    function syncKeysFromCloud() {
      onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'keys'), (snapshot) => {
        const cloudKeys = [];
        snapshot.forEach(doc => { cloudKeys.push({ docId: doc.id, ...doc.data() }); });
        state.activeKeys = cloudKeys; renderAdminPanel();
      });
    }

    function showToast(message, type = 'success') {
      const container = document.getElementById('toast-container');
      const toast = document.createElement('div');
      toast.className = `px-4 py-2 rounded-xl text-xs font-bold border ${type === 'error' ? 'bg-red-950 text-red-300 border-red-500/20' : 'bg-emerald-950 text-emerald-300 border-emerald-500/20'}`;
      toast.innerText = message; container.appendChild(toast);
      setTimeout(() => { toast.remove(); }, 2500);
    }

    function switchView(viewName) {
      state.view = viewName;
      document.getElementById('view-gate').classList.add('hidden');
      document.getElementById('view-hub').classList.add('hidden');
      document.getElementById('view-admin').classList.add('hidden');
      document.getElementById('view-' + viewName).classList.remove('hidden');
      renderNavigation();
    }

    function renderNavigation() {
      const navContainer = document.getElementById('nav-actions');
      if (!state.isAuthenticated) return;
      navContainer.innerHTML = `
        <button onclick="window.switchView('hub')" class="text-xs px-2.5 py-1.5 rounded font-bold ${state.view === 'hub' ? 'text-purple-400' : 'text-slate-400'}">คลังสคริปต์</button>
        ${state.isAdmin ? `<button onclick="window.switchView('admin')" class="text-xs px-2.5 py-1.5 rounded font-bold ${state.view === 'admin' ? 'text-indigo-400' : 'text-slate-400'}">จัดการแอดมิน</button>` : ''}
        <button onclick="logoutSession()" class="text-red-400 text-xs px-2 py-1">ออก</button>
      `;
    }

    function logoutSession() { state.isAuthenticated = false; state.isAdmin = false; switchView('gate'); }
    function openSimulatedBypass() { document.getElementById('modal-bypass').classList.remove('hidden'); document.getElementById('bypass-step-1').classList.remove('hidden'); document.getElementById('bypass-step-2').classList.add('hidden'); document.getElementById('bypass-step-3').classList.add('hidden'); }
    function closeSimulatedBypass() { document.getElementById('modal-bypass').classList.add('hidden'); clearInterval(bypassInterval); }

    function startBypassTimer() {
      document.getElementById('bypass-step-1').classList.add('hidden'); document.getElementById('bypass-step-2').classList.remove('hidden');
      bypassCounter = 5; document.getElementById('bypass-countdown').innerText = bypassCounter;
      bypassInterval = setInterval(async () => {
        bypassCounter--; document.getElementById('bypass-countdown').innerText = bypassCounter;
        if (bypassCounter === 0) {
          clearInterval(bypassInterval);
          const newKey = 'KEY-' + Math.random().toString(36).substring(2, 8).toUpperCase();
          state.generatedKey = newKey;
          await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'keys', newKey), { key: newKey, expires: Date.now() + 86400000 });
          document.getElementById('bypass-step-2').classList.add('hidden'); document.getElementById('bypass-step-3').classList.remove('hidden');
          document.getElementById('bypass-result-key').innerText = newKey;
        }
      }, 1000);
    }

    function copyGeneratedKey() { navigator.clipboard.writeText(state.generatedKey); showToast('คัดลอกคีย์แล้ว!'); }
    function autoFillKey() { document.getElementById('key-input').value = state.generatedKey; closeSimulatedBypass(); }

    function verifyAccessKey() {
      const userKey = document.getElementById('key-input').value.trim();
      const isNormalKey = state.activeKeys.some(k => k.key === userKey && k.expires > Date.now());
      if (userKey === "15466675546844776" || userKey === "153227753") {
        state.isAuthenticated = true; state.isAdmin = true; switchView('hub'); showToast('แอดมินเข้าสู่ระบบ!');
      } else if (isNormalKey) {
        state.isAuthenticated = true; state.isAdmin = false; switchView('hub'); showToast('ปลดล็อกคีย์สำเร็จ!');
      } else { showToast('คีย์ไม่ถูกต้อง!', 'error'); }
    }

    function loginAsAdmin() {
      const adminKey = document.getElementById('admin-key-input').value.trim();
      if (adminKey === "15466675546844776" || adminKey === "153227753") {
        state.isAuthenticated = true; state.isAdmin = true; switchView('admin'); showToast('สิทธิ์แอดมินผ่าน!');
      } else { showToast('คีย์ส่วนตัวไม่ถูกต้อง!', 'error'); }
    }

    function updateScriptCount() {
      document.getElementById('hub-script-count').innerText = state.scripts.length;
      document.getElementById('hub-role-badge').innerText = state.isAdmin ? 'ADMIN ROOT' : 'USER';
    }

    function renderGameFilters() {
      const container = document.getElementById('game-filters');
      const games = ['All', ...new Set(state.scripts.map(s => s.game))];
      container.innerHTML = games.map(g => `<button onclick="window.filterByGame('${g}')" class="px-2.5 py-1 text-xs rounded font-bold ${state.selectedGame === g ? 'bg-purple-600 text-white' : 'bg-slate-900 text-slate-400'}">${g === 'All' ? 'ทั้งหมด' : g}</button>`).join('');
    }

    function filterByGame(gameName) { state.selectedGame = gameName; renderGameFilters(); renderHubScripts(); }

    function renderHubScripts() {
      const grid = document.getElementById('scripts-grid');
      const searchTerm = document.getElementById('search-input').value.toLowerCase();
      const filtered = state.scripts.filter(s => (s.title.toLowerCase().includes(searchTerm) || s.game.toLowerCase().includes(searchTerm)) && (state.selectedGame === 'All' || s.game === state.selectedGame));
      
      if (filtered.length === 0) { grid.innerHTML = `<p class="text-xs text-slate-500 col-span-full text-center py-6">ไม่พบสคริปต์ออนไลน์</p>`; return; }
      grid.innerHTML = filtered.map(s => `
        <div class="bg-slate-900/50 border border-slate-850 p-4 rounded-xl flex flex-col justify-between">
          <div><span class="text-[9px] text-purple-400 font-bold">${s.game}</span><h3 class="text-xs font-bold text-white mt-1">${s.title}</h3></div>
          <div class="flex justify-end gap-1.5 pt-4 border-t border-slate-800/40 mt-3">
            <button onclick="window.openScriptView(${s.id})" class="bg-slate-800 text-[10px] px-2.5 py-1 text-slate-200 rounded">ดูโค้ด</button>
            <button onclick="window.quickCopyScript(${s.id})" class="bg-purple-600 text-[10px] px-2.5 py-1 text-white rounded">คัดลอก</button>
          </div>
        </div>
      `).join('');
    }

    async function quickCopyScript(id) {
      const script = state.scripts.find(s => s.id === id);
      if (script) { navigator.clipboard.writeText(script.code); showToast('คัดลอกสคริปต์ลงบอร์ดแล้ว!'); }
    }

    function openScriptView(id) {
      const script = state.scripts.find(s => s.id === id);
      if (script) {
        document.getElementById('modal-tag-game').innerText = script.game; document.getElementById('modal-title').innerText = script.title;
        document.getElementById('modal-desc').innerText = script.description || ''; document.getElementById('modal-code').innerText = script.code;
        document.getElementById('modal-btn-copy').onclick = () => quickCopyScript(script.id);
        document.getElementById('modal-script-view').classList.remove('hidden');
      }
    }

    function closeScriptViewModal() { document.getElementById('modal-script-view').classList.add('hidden'); }

    function renderAdminPanel() {
      if (!state.isAdmin) return;
      document.getElementById('admin-scripts-list').innerHTML = state.scripts.map(s => `
        <tr class="border-b border-slate-800/40 text-[11px]"><td class="py-2 text-white">${s.title}</td><td>${s.game}</td><td class="text-right">
          <button onclick="window.editScriptFromAdmin(${s.id})" class="text-indigo-400 px-1">✏️</button><button onclick="window.askDeleteScript(${s.id})" class="text-red-400 px-1">🗑️</button>
        </td></tr>
      `).join('');
      document.getElementById('active-keys-list').innerHTML = state.activeKeys.map(k => `<div class="bg-slate-950 p-2 border border-slate-850 rounded flex justify-between text-[11px]"><code class="text-yellow-400 font-mono">${k.key}</code><span class="text-slate-500">Active</span></div>`).join('');
    }

    async function saveScriptData(e) {
      e.preventDefault();
      const scriptId = document.getElementById('form-id').value;
      const title = document.getElementById('form-title-input').value.trim();
      const game = document.getElementById('form-game-input').value.trim();
      const category = document.getElementById('form-cat-input').value.trim();
      const description = document.getElementById('form-desc-input').value.trim();
      const code = document.getElementById('form-code-input').value.trim();

      if (scriptId) {
        await updateDoc(doc(db, 'artifacts', appId, 'public', 'data', 'scripts', scriptId), { title, game, category, description, code });
      } else {
        const newId = Date.now().toString();
        await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'scripts', newId), { id: parseInt(newId), title, game, category, description, code, date: new Date().toISOString().split('T')[0] });
      }
      resetAdminForm(); showToast('บันทึกสคริปต์เสร็จสิ้น!');
    }

    function editScriptFromAdmin(id) {
      const script = state.scripts.find(s => s.id === id);
      if (script) {
        document.getElementById('form-id').value = script.id; document.getElementById('form-title-input').value = script.title;
        document.getElementById('form-game-input').value = script.game; document.getElementById('form-cat-input').value = script.category || '';
        document.getElementById('form-desc-input').value = script.description || ''; document.getElementById('form-code-input').value = script.code;
        document.getElementById('btn-cancel-edit').classList.remove('hidden');
      }
    }

    function resetAdminForm() { document.getElementById('script-form').reset(); document.getElementById('form-id').value = ''; document.getElementById('btn-cancel-edit').classList.add('hidden'); }
    function askDeleteScript(id) { deletingScriptId = id; document.getElementById('modal-delete-confirm').classList.remove('hidden'); }
    function closeDeleteModal() { document.getElementById('modal-delete-confirm').classList.add('hidden'); }
    async function confirmDeleteScript() { if (deletingScriptId) { await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'scripts', deletingScriptId.toString())); closeDeleteModal(); showToast('ลบสคริปต์แล้ว'); } }

    document.getElementById('btn-confirm-delete').onclick = confirmDeleteScript;
    window.switchView = switchView; window.openSimulatedBypass = openSimulatedBypass; window.closeSimulatedBypass = closeSimulatedBypass;
    window.startBypassTimer = startBypassTimer; window.copyGeneratedKey = copyGeneratedKey; window.autoFillKey = autoFillKey;
    window.verifyAccessKey = verifyAccessKey; window.loginAsAdmin = loginAsAdmin; window.filterByGame = filterByGame;
    window.openScriptView = openScriptView; window.quickCopyScript = quickCopyScript; window.saveScriptData = saveScriptData;
    window.editScriptFromAdmin = editScriptFromAdmin; window.resetAdminForm = resetAdminForm; window.askDeleteScript = askDeleteScript;
    window.closeDeleteModal = closeDeleteModal; window.logoutSession = logoutSession; window.closeScriptViewModal = closeScriptViewModal;
  </script>
</body>
</html>
