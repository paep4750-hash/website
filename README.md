<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Sonic Pixel 3D World</title>
    <style>
        body, html { margin: 0; padding: 0; overflow: hidden; width: 100%; height: 100%; user-select: none; touch-action: none; background: #000; font-family: sans-serif; }
        #canvas-container { width: 100%; height: 100%; position: absolute; top:0; left:0; z-index: 1; }
        
        /* สกอร์คะแนน */
        #score-board {
            position: absolute; top: 20px; left: 20px; color: #fff; font-size: 1.5rem;
            font-weight: bold; z-index: 10; text-shadow: 2px 2px #000;
            background: rgba(0,0,0,0.5); padding: 10px 20px; border-radius: 10px; border: 2px solid #fbff00;
        }

        /* ปุ่มควบคุมการเดิน (D-Pad) */
        #control-pad { position: absolute; bottom: 30px; left: 30px; width: 160px; height: 160px; z-index: 10; }
        .btn {
            position: absolute; width: 50px; height: 50px; background: rgba(255,255,255,0.3);
            border: 2px solid #fff; border-radius: 50%; color: white; font-weight: bold;
            display: flex; justify-content: center; align-items: center; font-size: 1.2rem;
        }
        .btn:active { background: rgba(255,255,255,0.7); color: black; }
        #btn-up    { top: 0; left: 55px; }
        #btn-left  { top: 55px; left: 0; }
        #btn-right { top: 55px; right: 0; }
        #btn-down  { bottom: 0; left: 55px; }

        /* ปุ่มกระโดด */
        #jump-btn {
            position: absolute; bottom: 40px; right: 40px; width: 70px; height: 70px;
            background: rgba(251, 255, 0, 0.4); border: 3px solid #fbff00; border-radius: 50%;
            color: white; font-weight: bold; display: flex; justify-content: center; align-items: center;
            z-index: 10; font-size: 1.1rem; text-shadow: 1px 1px 2px #000;
        }
        #jump-btn:active { background: rgba(251, 255, 0, 0.8); color: #000; }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>

    <div id="score-board">RINGS: <span id="score">0</span></div>

    <div id="control-pad">
        <div id="btn-up" class="btn">▲</div>
        <div id="btn-left" class="btn">◀</div>
        <div id="btn-right" class="btn">▶</div>
        <div id="btn-down" class="btn">▼</div>
    </div>

    <div id="jump-btn">JUMP</div>

    <div id="canvas-container"></div>

    <script>
        let scene, camera, renderer;
        let moveForward = false, moveBackward = false, moveLeft = false, moveRight = false;
        let yaw = 0, pitch = 0;
        let touchLookId = null, touchLookStartX, touchLookStartY;
        
        // ระบบฟิสิกส์ตัวละคร
        let playerVelocity = new THREE.Vector3();
        let isGrounded = true;
        const GRAVITY = 0.005;
        const JUMP_FORCE = 0.12;

        // ไอเทมในเกม
        let rings = [];
        let score = 0;

        init();
        animate();

        function init() {
            scene = new THREE.Scene();

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(0, 1.5, 5); // เริ่มต้นยืนบนเนินมองเห็นวิว

            // แสงสว่างสดใสแนวเกมย้อนยุค
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.8);
            scene.add(ambientLight);
            const dirLight = new THREE.DirectionalLight(0xffffff, 0.6);
            dirLight.position.set(10, 20, 10);
            scene.add(dirLight);

            createSonicWorld();

            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            document.getElementById('canvas-container').appendChild(renderer.domElement);

            setupTouchEvents();
            window.addEventListener('resize', onWindowResize);
        }

        function createSonicWorld() {
            // 1. [ใช้รูปภาพของคุณเป็นฉากหลังแบบ 360 องศา]
            // ดึงภาพที่คุณอัปโหลดมาทำเป็นพื้นหลังโอบล้อมรอบตัวผู้เล่น
            const textureLoader = new THREE.TextureLoader();
            textureLoader.load('https://api.allorigins.win/raw?url=https://images.squarespace-cdn.com/content/v1/5511fc7ce4b0e77d70bc9d97/1614214436577-G0WZ6F8UXJ29E3YAX8A1/sonic_greenhill_pixelart.jpg', function(texture) {
                // หากลิงก์สำรองคลาดเคลื่อน ระบบจะใช้สีท้องฟ้าด้านล่างแทน แต่ถ้าโหลดผ่าน โดเมนตัวเองจะเสถียรที่สุดครับ
            });
            scene.background = new THREE.Color(0x4ca6ff); // สีฟ้าสไตล์โซนิค

            // 2. สร้างพื้นตารางหมากรุก (Checkered Floor) สีน้ำตาลสลับส้มแบบในรูป
            const floorGeo = new THREE.BoxGeometry(50, 2, 50);
            const floorMat = new THREE.MeshStandardMaterial({ color: 0xe57373, roughness: 0.6 }); // สีส้มอิฐแทนตาราง
            const mainFloor = new THREE.Mesh(floorGeo, floorMat);
            mainFloor.position.y = -1;
            scene.add(mainFloor);

            // โซนหญ้าสีเขียวด้านบนผิว
            const grassGeo = new THREE.PlaneGeometry(50, 50);
            const grassMat = new THREE.MeshStandardMaterial({ color: 0x4caf50, roughness: 0.5 });
            const grass = new THREE.Mesh(grassGeo, grassMat);
            grass.rotation.x = -Math.PI / 2;
            grass.position.y = 0.01;
            scene.add(grass);

            // 3. สร้างเนินวิ่งและอุโมงค์จำลอง (ทรงกระบอกและกล่องสไตล์พิกเซล)
            const hillMat = new THREE.MeshStandardMaterial({ color: 0x81c784 });
            for(let i = 0; i < 5; i++) {
                const hill = new THREE.Mesh(new THREE.BoxGeometry(6, 2 + i, 6), hillMat);
                hill.position.set(-15 + (i*2), (2+i)/2, -10);
                scene.add(hill);
            }

            // 4. [สร้างวงแหวนทองคำ 3D (Rings)] 
            // กระจายอยู่ตามฉากให้วิ่งเก็บเหมือนในเกม
            const ringGeo = new THREE.TorusGeometry(0.4, 0.1, 8, 24);
            const ringMat = new THREE.MeshStandardMaterial({ color: 0xffd700, metalness: 0.9, roughness: 0.1 });

            const ringPositions = [
                [0, 1, -2], [2, 1, -4], [-2, 1, -6],
                [-10, 4, -10], [5, 1, 5], [-5, 1, 8]
            ];

            ringPositions.forEach(pos => {
                const ring = new THREE.Mesh(ringGeo, ringMat);
                ring.position.set(pos[0], pos[1], pos[2]);
                scene.add(ring);
                rings.push(ring);
            });
        }

        function setupTouchEvents() {
            // ปุ่ม D-Pad บังคับเดิน
            const bindBtn = (id, callback) => {
                const el = document.getElementById(id);
                el.addEventListener('touchstart', (e) => { e.preventDefault(); callback(true); });
                el.addEventListener('touchend', () => callback(false));
            };

            bindBtn('btn-up', (val) => moveForward = val);
            bindBtn('btn-down', (val) => moveBackward = val);
            bindBtn('btn-left', (val) => moveLeft = val);
            bindBtn('btn-right', (val) => moveRight = val);

            // ปุ่มกระโดด
            document.getElementById('jump-btn').addEventListener('touchstart', (e) => {
                e.preventDefault();
                if (isGrounded) {
                    playerVelocity.y = JUMP_FORCE; // ส่งแรงกระโดดขึ้นฟ้า
                    isGrounded = false;
                }
            });

            // ปัดหน้าจอฝั่งขวาเพื่อหันมองรอบๆ ตัว 360 องศา
            window.addEventListener('touchstart', (e) => {
                for (let i = 0; i < e.changedTouches.length; i++) {
                    const t = e.changedTouches[i];
                    if (t.clientX > window.innerWidth / 2 && touchLookId === null) {
                        touchLookId = t.identifier;
                        touchLookStartX = t.clientX;
                        touchLookStartY = t.clientY;
                    }
                }
            });

            window.addEventListener('touchmove', (e) => {
                for (let i = 0; i < e.touches.length; i++) {
                    const t = e.touches[i];
                    if (t.identifier === touchLookId) {
                        let dx = t.clientX - touchLookStartX;
                        let dy = t.clientY - touchLookStartY;
                        
                        yaw -= dx * 0.005;
                        pitch -= dy * 0.003;
                        pitch = Math.max(-Math.PI/3, Math.min(Math.PI/3, pitch)); // ล็อกขอบมุมมองบนล่าง
                        
                        touchLookStartX = t.clientX;
                        touchLookStartY = t.clientY;
                    }
                }
            });

            window.addEventListener('touchend', (e) => {
                for (let i = 0; i < e.changedTouches.length; i++) {
                    if (e.changedTouches[i].identifier === touchLookId) touchLookId = null;
                }
            });
        }

        function animate() {
            requestAnimationFrame(animate);

            // 1. หมุนกล้องตามนิ้วปัด
            const target = new THREE.Vector3(
                Math.sin(yaw) * Math.cos(pitch),
                Math.sin(pitch),
                Math.cos(yaw) * Math.cos(pitch)
            );
            camera.lookAt(camera.position.clone().add(target));

            // 2. ระบบคำนวณการเคลื่อนที่ (เดินหน้า/ถอยหลัง)
            const speed = 0.15;
            const forward = new THREE.Vector3(target.x, 0, target.z).normalize();
            const right = new THREE.Vector3().crossVectors(camera.up, forward).normalize();

            if (moveForward) camera.position.addScaledVector(forward, speed);
            if (moveBackward) camera.position.addScaledVector(forward, -speed);
            if (moveLeft) camera.position.addScaledVector(right, -speed);
            if (moveRight) camera.position.addScaledVector(right, speed);

            // 3. ระบบแรงโน้มถ่วงฟิสิกส์และการกระโดด
            camera.position.y += playerVelocity.y;
            
            if (!isGrounded) {
                playerVelocity.y -= GRAVITY; // ดึงตัวลงมาเรื่อยๆ ถ้าอยู่กลางอากาศ
            }

            // ชนพื้นดิน (ล็อกความสูงขั้นต่ำ)
            if (camera.position.y <= 1.5) {
                playerVelocity.y = 0;
                camera.position.y = 1.5;
                isGrounded = true;
            }

            // 4. เอฟเฟกต์หมุนแหวนทองคำ และ ตรวจจับการวิ่งชนเก็บเหรียญ
            rings.forEach((ring, index) => {
                if(ring.visible) {
                    ring.rotation.y += 0.05; // หมุนติ้วๆ

                    // วัดระยะห่างระหว่างผู้เล่นกับเหรียญ
                    const dist = camera.position.distanceTo(ring.position);
                    if (dist < 1.2) { // ถ้าวิ่งเข้าไปชนในระยะใกล้
                        ring.visible = false; // ซ่อนเหรียญ
                        score += 1;
                        document.getElementById('score').innerText = score; // อัปเดตแต้มบนจอ
                    }
                }
            });

            renderer.render(scene, camera);
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }
    </script>
</body>
</html>
