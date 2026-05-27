<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>3D World for Tablet</title>
    <style>
        body, html { margin: 0; padding: 0; overflow: hidden; width: 100%; height: 100%; user-select: none; touch-action: none; background: #87ceeb; font-family: sans-serif; }
        #canvas-container { width: 100%; height: 100%; position: absolute; top:0; left:0; z-index: 1; }
        
        /* แผงปุ่มควบคุมการเดิน (D-Pad) */
        #control-pad {
            position: absolute; bottom: 30px; left: 30px;
            width: 160px; height: 160px; z-index: 10;
        }
        .btn {
            position: absolute; width: 50px; height: 50px;
            background: rgba(255,255,255,0.4); border: 2px solid #fff;
            border-radius: 10px; color: white; font-weight: bold;
            display: flex; justify-content: center; align-items: center;
            font-size: 1.2rem; active { background: rgba(255,255,255,0.8); color: black; }
        }
        #btn-up    { top: 0; left: 55px; }
        #btn-left  { top: 55px; left: 0; }
        #btn-right { top: 55px; right: 0; }
        #btn-down  { bottom: 0; left: 55px; }

        #ui-hint {
            position: absolute; top: 20px; left: 20px; color: white;
            z-index: 10; pointer-events: none; text-shadow: 1px 1px 3px rgba(0,0,0,0.8);
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>

    <div id="ui-hint">
        <h2>ระบบสัมผัส (Tablet)</h2>
        <p>🕹️ ปุ่มซ้ายล่าง: กดค้างเพื่อเดิน<br>📱 หน้าจอฝั่งขวา: ปัดนิ้วเพื่อหันมองรอบๆ</p>
    </div>

    <div id="control-pad">
        <div id="btn-up" class="btn">W</div>
        <div id="btn-left" class="btn">A</div>
        <div id="btn-right" class="btn">D</div>
        <div id="btn-down" class="btn">S</div>
    </div>

    <div id="canvas-container"></div>

    <script>
        let scene, camera, renderer;
        let moveForward = false, moveBackward = false, moveLeft = false, moveRight = false;
        let yaw = 0, pitch = 0;
        let touchLookId = null, touchLookStartX, touchLookStartY;
        let cars = [];

        init();
        animate();

        function init() {
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0x87ceeb);
            scene.fog = new THREE.FogExp2(0x87ceeb, 0.015);

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(0, 1.6, 0); // ระดับสายตา

            const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
            scene.add(ambientLight);
            const dirLight = new THREE.DirectionalLight(0xffffff, 0.8);
            dirLight.position.set(20, 40, 20);
            scene.add(dirLight);

            createWorld();

            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            document.getElementById('canvas-container').appendChild(renderer.domElement);

            setupTouchEvents();
            window.addEventListener('resize', onWindowResize);
        }

        function createWorld() {
            // พื้นหญ้า
            const ground = new THREE.Mesh(new THREE.PlaneGeometry(1000, 1000), new THREE.MeshStandardMaterial({ color: 0x557a46 }));
            ground.rotation.x = -Math.PI / 2;
            scene.add(ground);

            // บ้าน
            const floor = new THREE.Mesh(new THREE.PlaneGeometry(8, 8), new THREE.MeshStandardMaterial({ color: 0xd2b48c }));
            floor.rotation.x = -Math.PI / 2; floor.position.set(0, 0.01, -2);
            scene.add(floor);

            const wallMat = new THREE.MeshStandardMaterial({ color: 0xf5f5dc });
            const wallBack = new THREE.Mesh(new THREE.BoxGeometry(8, 3, 0.2), wallMat); wallBack.position.set(0, 1.5, -6); scene.add(wallBack);
            const wallLeft = new THREE.Mesh(new THREE.BoxGeometry(0.2, 3, 8), wallMat); wallLeft.position.set(-4, 1.5, -2); scene.add(wallLeft);
            const wallRight = new THREE.Mesh(new THREE.BoxGeometry(0.2, 3, 8), wallMat); wallRight.position.set(4, 1.5, -2); scene.add(wallRight);
            
            // ประตูบ้านด้านหน้า
            const wallFrontLeft = new THREE.Mesh(new THREE.BoxGeometry(3, 3, 0.2), wallMat); wallFrontLeft.position.set(-2.5, 1.5, 2); scene.add(wallFrontLeft);
            const wallFrontRight = new THREE.Mesh(new THREE.BoxGeometry(3, 3, 0.2), wallMat); wallFrontRight.position.set(2.5, 1.5, 2); scene.add(wallFrontRight);

            const roof = new THREE.Mesh(new THREE.ConeGeometry(6, 2, 4), new THREE.MeshStandardMaterial({ color: 0x8b0000 }));
            roof.position.set(0, 4, -2); roof.rotation.y = Math.PI / 4;
            scene.add(roof);

            // ถนน
            const road = new THREE.Mesh(new THREE.PlaneGeometry(1000, 12), new THREE.MeshStandardMaterial({ color: 0x333333 }));
            road.rotation.x = -Math.PI / 2; road.position.set(0, 0.02, 15);
            scene.add(road);

            // รถสัญจร
            for (let i = 0; i < 3; i++) {
                let car = new THREE.Mesh(new THREE.BoxGeometry(4, 1.2, 2), new THREE.MeshStandardMaterial({ color: 0xcc2222 }));
                car.position.set((i * 80) - 80, 0.6, 15);
                car.userData = { speed: 0.15 };
                scene.add(car);
                cars.push(car);
            }
        }

        function setupTouchEvents() {
            // ตั้งค่าปุ่มกดเดิน
            const bindBtn = (id, callback) => {
                const el = document.getElementById(id);
                el.addEventListener('touchstart', (e) => { e.preventDefault(); callback(true); });
                el.addEventListener('touchend', () => callback(false));
            };

            bindBtn('btn-up', (val) => moveForward = val);
            bindBtn('btn-down', (val) => moveBackward = val);
            bindBtn('btn-left', (val) => moveLeft = val);
            bindBtn('btn-right', (val) => moveRight = val);

            // ตั้งค่าพื้นที่หันหน้าจอ (ฝั่งขวา)
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
                        pitch -= dy * 0.005;
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

            // หมุนกล้องตามนิ้วปัด
            const target = new THREE.Vector3(
                Math.sin(yaw) * Math.cos(pitch),
                Math.sin(pitch),
                Math.cos(yaw) * Math.cos(pitch)
            );
            camera.lookAt(camera.position.clone().add(target));

            // คำนวณการเดิน
            const speed = 0.1;
            const forward = new THREE.Vector3(target.x, 0, target.z).normalize();
            const right = new THREE.Vector3().crossVectors(camera.up, forward).normalize();

            if (moveForward) camera.position.addScaledVector(forward, speed);
            if (moveBackward) camera.position.addScaledVector(forward, -speed);
            if (moveLeft) camera.position.addScaledVector(right, -speed);
            if (moveRight) camera.position.addScaledVector(right, speed);
            camera.position.y = 1.6; // ล็อกความสูง

            // รถวิ่ง
            cars.forEach(car => {
                car.position.x += car.userData.speed;
                if (car.position.x > 150) car.position.x = -150;
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
