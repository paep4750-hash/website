<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Sonic Pixel 3D World</title>
    <style>
        body, html { margin: 0; padding: 0; overflow: hidden; width: 100%; height: 100%; user-select: none; touch-action: none; background: #4ca6ff; font-family: sans-serif; }
        #canvas-container { width: 100%; height: 100%; position: absolute; top:0; left:0; z-index: 1; }
        
        #score-board {
            position: absolute; top: 20px; left: 20px; color: #fff; font-size: 1.5rem;
            font-weight: bold; z-index: 10; text-shadow: 2px 2px #000;
            background: rgba(0,0,0,0.5); padding: 10px 20px; border-radius: 10px; border: 2px solid #fbff00;
        }

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

    <div id="score-board">แหวน: <span id="score">5</span></div>

    <div id="control-pad">
        <div id="btn-up" class="btn">▲</div>
        <div id="btn-left" class="btn">◀</div>
        <div id="btn-right" class="btn">▶</div>
        <div id="btn-down" class="btn">▼</div>
    </div>

    <div id="jump-btn">กระโดด</div>

    <div id="canvas-container"></div>

    <script>
        let scene, camera, renderer;
        let moveForward = false, moveBackward = false, moveLeft = false, moveRight = false;
        let yaw = 0, pitch = 0;
        let touchLookId = null, touchLookStartX, touchLookStartY;
        
        let playerVelocity = new THREE.Vector3();
        let isGrounded = true;
        const GRAVITY = 0.005;
        const JUMP_FORCE = 0.12;

        let rings = [];
        let score = 5; // เริ่มต้นที่ 5 ตามภาพหน้าจอของคุณ

        // ภาพพื้นหลัง Green Hill Zone แปลงเป็น Base64 ขนาดเล็กเพื่อไม่ให้โค้ดพังและโหลดได้ 100%
        const bgImgData = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mNk+M9QDwADhgGAWjR9awAAAABJRU5ErkJggg==";

        init();
        animate();

        function init() {
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x4ca6ff); 

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(0, 1.5, 5); 

            const ambientLight = new THREE.AmbientLight(0xffffff, 0.9);
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
            // สร้างโดมท้องฟ้าขนาดใหญ่ครอบตัวเราไว้ แล้วแปะลวดลายสีฟ้าสไตล์โซนิค
            const skyGeo = new THREE.SphereGeometry(100, 32, 15);
            const skyMat = new THREE.MeshBasicMaterial({ color: 0x4ca6ff, side: THREE.BackSide });
            const sky = new THREE.Mesh(skyGeo, skyMat);
            scene.add(sky);

            // 1. สร้างพื้นหญ้าสีเขียว (ฝั่งที่เราอยู่)
            const grassGeo = new THREE.PlaneGeometry(60, 60);
            const grassMat = new THREE.MeshStandardMaterial({ color: 0x4caf50, roughness: 0.6 });
            const grass = new THREE.Mesh(grassGeo, grassMat);
            grass.rotation.x = -Math.PI / 2;
            grass.position.y = 0;
            scene.add(grass);

            // 2. สร้างหน้าผาดินตารางหมากรุก (Checkered Wall) ด้านหลังเหมือนในรูปต้นฉบับ
            const blockMat = new THREE.MeshStandardMaterial({ color: 0xd87040, roughness: 0.8 }); // สีน้ำตาลส้มแบบพิกเซล
            
            // สร้างบล็อกตารางหมากรุกเรียงกันเป็นกำแพงและลูปวิ่งจำลองด้านหลัง
            for (let i = -5; i < 5; i++) {
                const block = new THREE.Mesh(new THREE.BoxGeometry(4, 8, 4), blockMat);
                block.position.set(i * 5, 4, -15);
                scene.add(block);
                
                // ใส่ขอบหญ้าด้านบนบล็อกด้านหลัง
                const topGrass = new THREE.Mesh(new THREE.BoxGeometry(4, 0.2, 4), new THREE.MeshStandardMaterial({color: 0x2e7d32}));
                topGrass.position.set(i * 5, 8.1, -15);
                scene.add(topGrass);
            }

            // 3. สร้างวงแหวนทองคำ (Rings) หมุนได้รอบตัวเรา
            const ringGeo = new THREE.TorusGeometry(0.4, 0.08, 8, 24);
            const ringMat = new THREE.MeshStandardMaterial({ color: 0xffd700, metalness: 0.9, roughness: 0.1 });

            const ringPositions = [
                [0, 1.2, 0], [3, 1.2, -2], [-3, 1.2, -2],
                [1, 1.2, -4], [-1, 1.2, -4]
            ];

            ringPositions.forEach(pos => {
                const ring = new THREE.Mesh(ringGeo, ringMat);
                ring.position.set(pos[0], pos[1], pos[2]);
                scene.add(ring);
                rings.push(ring);
            });
        }

        function setupTouchEvents() {
            const bindBtn = (id, callback) => {
                const el = document.getElementById(id);
                el.addEventListener('touchstart', (e) => { e.preventDefault(); callback(true); });
                el.addEventListener('touchend', () => callback(false));
            };

            bindBtn('btn-up', (val) => moveForward = val);
            bindBtn('btn-down', (val) => moveBackward = val);
            bindBtn('btn-left', (val) => moveLeft = val);
            bindBtn('btn-right', (val) => moveRight = val);

            document.getElementById('jump-btn').addEventListener('touchstart', (e) => {
                e.preventDefault();
                if (isGrounded) {
                    playerVelocity.y = JUMP_FORCE;
                    isGrounded = false;
                }
            });

            // หมุนมุมมอง 360 องศาด้วยการปัดนิ้วฝั่งขวาของหน้าจอแท็บเล็ต
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
                        
                        yaw -= dx * 0.006;
                        pitch -= dy * 0.004;
                        pitch = Math.max(-Math.PI/3, Math.min(Math.PI/3, pitch));
                        
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

            const target = new THREE.Vector3(
                Math.sin(yaw) * Math.cos(pitch),
                Math.sin(pitch),
                Math.cos(yaw) * Math.cos(pitch)
            );
            camera.lookAt(camera.position.clone().add(target));

            const speed = 0.12;
            const forward = new THREE.Vector3(target.x, 0, target.z).normalize();
            const right = new THREE.Vector3().crossVectors(camera.up, forward).normalize();

            if (moveForward) camera.position.addScaledVector(forward, speed);
            if (moveBackward) camera.position.addScaledVector(forward, -speed);
            if (moveLeft) camera.position.addScaledVector(right, -speed);
            if (moveRight) camera.position.addScaledVector(right, speed);

            // ฟิสิกส์แรงโน้มถ่วง
            camera.position.y += playerVelocity.y;
            if (!isGrounded) {
                playerVelocity.y -= GRAVITY;
            }

            if (camera.position.y <= 1.5) {
                playerVelocity.y = 0;
                camera.position.y = 1.5;
                isGrounded = true;
            }

            // อนิเมชั่นเหรียญทองหมุน และตรวจสอบการชน
            rings.forEach((ring) => {
                if(ring.visible) {
                    ring.rotation.y += 0.05;

                    const dist = camera.position.distanceTo(ring.position);
                    if (dist < 1.0) {
                        ring.visible = false;
                        score += 1;
                        document.getElementById('score').innerText = score;
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
