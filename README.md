<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>3D Night Rain World - Kid View</title>
    <style>
        body, html { margin: 0; padding: 0; overflow: hidden; width: 100%; height: 100%; user-select: none; touch-action: none; background: #050510; font-family: sans-serif; }
        #canvas-container { width: 100%; height: 100%; position: absolute; top:0; left:0; z-index: 1; }
        
        /* ปุ่มควบคุมการเดิน (D-Pad) */
        #control-pad { position: absolute; bottom: 30px; left: 30px; width: 160px; height: 160px; z-index: 10; }
        .btn {
            position: absolute; width: 50px; height: 50px; background: rgba(255,255,255,0.2);
            border: 2px solid rgba(255,255,255,0.5); border-radius: 50%; color: white; font-weight: bold;
            display: flex; justify-content: center; align-items: center; font-size: 1.2rem;
        }
        .btn:active { background: rgba(255,255,255,0.6); color: black; }
        #btn-up    { top: 0; left: 55px; }
        #btn-left  { top: 55px; left: 0; }
        #btn-right { top: 55px; right: 0; }
        #btn-down  { bottom: 0; left: 55px; }

        /* ปุ่มปฏิสัมพันธ์ เช่น เปิดประตู */
        #action-btn {
            position: absolute; bottom: 40px; right: 40px; width: 70px; height: 70px;
            background: rgba(0, 255, 136, 0.3); border: 2px solid #00ff88; border-radius: 50%;
            color: white; font-weight: bold; display: flex; justify-content: center; align-items: center;
            z-index: 10; font-size: 0.9rem; text-align: center;
        }
        #action-btn:active { background: rgba(0, 255, 136, 0.7); }

        #ui-hint { position: absolute; top: 20px; left: 20px; color: white; z-index: 10; pointer-events: none; text-shadow: 1px 1px 3px rgba(0,0,0,0.8); }
        #ui-hint h3 { margin: 0; color: #00ff88; }
        #ui-hint p { margin: 5px 0 0 0; font-size: 0.8rem; opacity: 0.8; }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>

    <div id="ui-hint">
        <h3>บรรยากาศคืนฝนตก (มุมมองเด็ก)</h3>
        <p>🕹️ ปุ่มซ้าย: เดิน | 📱 จอฝั่งขวา: ปัดเพื่อหันมอง<br>🚪 เดินไปใกล้ประตูหน้าบ้านแล้วกดปุ่ม "เปิด/ปิด" ด้านขวาได้เลย!</p>
    </div>

    <div id="control-pad">
        <div id="btn-up" class="btn">W</div>
        <div id="btn-left" class="btn">A</div>
        <div id="btn-right" class="btn">D</div>
        <div id="btn-down" class="btn">S</div>
    </div>

    <div id="action-btn">เปิด/<br>ปิด</div>

    <div id="canvas-container"></div>

    <script>
        let scene, camera, renderer;
        let moveForward = false, moveBackward = false, moveLeft = false, moveRight = false;
        let yaw = 0, pitch = 0;
        let touchLookId = null, touchLookStartX, touchLookStartY;
        
        // ระบบฝนและรถ
        let rainParticles, rainCount = 1500;
        let cars = [];
        const carSpeed = 0.2;

        // โมเดลบ้านและประตู
        let doorPivot;
        let isDoorOpen = false;
        let targetDoorRotation = 0;

        init();
        animate();

        function init() {
            scene = new THREE.Scene();
            // ปรับเป็นตอนกลางคืนมืดๆ สีน้ำเงินเข้ม
            scene.background = new THREE.Color(0x020208);
            scene.fog = new THREE.FogExp2(0x020208, 0.03);

            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            
            // !!! ปรับตัวเราเล็กลงเป็นเด็ก: ลดระดับสายตา (ความสูงกล้อง) ลงเหลือ 0.8 หน่วย (จากเดิม 1.6)
            camera.position.set(0, 0.8, 0); 

            // แสงสว่างสลัวๆ ตอนกลางคืน (Ambient แสงจันทร์)
            const ambientLight = new THREE.AmbientLight(0x111133, 0.5);
            scene.add(ambientLight);

            createWorld();
            createRain();

            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            // เปิดระบบเงาและแสงสมจริง
            renderer.shadowMap.enabled = true;
            document.getElementById('canvas-container').appendChild(renderer.domElement);

            setupTouchEvents();
            window.addEventListener('resize', onWindowResize);
        }

        function createWorld() {
            // พื้นดินเปียกฝนตอนกลางคืน (สีเข้มสะท้อนแสงนิดๆ)
            const ground = new THREE.Mesh(
                new THREE.PlaneGeometry(1000, 1000), 
                new THREE.MeshStandardMaterial({ color: 0x152215, roughness: 0.2 })
            );
            ground.rotation.x = -Math.PI / 2;
            scene.add(ground);

            // ==========================================
            // [ อัปเกรดบ้านหรู 2 ชั้น + ห้องใต้หลังคา ]
            // ==========================================
            const houseGroup = new THREE.Group();
            houseGroup.position.set(0, 0, -2);

            const wallMat = new THREE.MeshStandardMaterial({ color: 0xcccccc, roughness: 0.5 }); // ผนังปูนสีเทา
            const interiorMat = new THREE.MeshStandardMaterial({ color: 0xeee8aa }); // ผนังในบ้านสีครีม
            const woodMat = new THREE.MeshStandardMaterial({ color: 0x5c4033 }); // เฟอร์นิเจอร์ไม้

            // 1. โครงสร้างกำแพงรอบบ้าน (ขนาดใหญ่ขึ้น กว้าง 10 x ลึก 12 x สูง 6)
            const wallBack = new THREE.Mesh(new THREE.BoxGeometry(10, 6, 0.2), wallMat); wallBack.position.set(0, 3, -6); houseGroup.add(wallBack);
            const wallLeft = new THREE.Mesh(new THREE.BoxGeometry(0.2, 6, 12), wallMat); wallLeft.position.set(-5, 3, 0); houseGroup.add(wallLeft);
            const wallRight = new THREE.Mesh(new THREE.BoxGeometry(0.2, 6, 12), wallMat); wallRight.position.set(5, 3, 0); houseGroup.add(wallRight);
            
            // หน้าบ้าน (เว้นรูตรงกลางไว้ใส่ประตู)
            const wallFrontL = new THREE.Mesh(new THREE.BoxGeometry(4, 6, 0.2), wallMat); wallFrontL.position.set(-3, 3, 6); houseGroup.add(wallFrontL);
            const wallFrontR = new THREE.Mesh(new THREE.BoxGeometry(4, 6, 0.2), wallMat); wallFrontR.position.set(3, 3, 6); houseGroup.add(wallFrontR);
            const wallFrontTop = new THREE.Mesh(new THREE.BoxGeometry(2, 3.8, 0.2), wallMat); wallFrontTop.position.set(0, 4.1, 6); houseGroup.add(wallFrontTop);

            // 2. แบ่งสัดส่วนห้องชั้น 1 (ห้องครัวอยู่ซ้าย ห้องนอนอยู่ขวา)
            const dividerWall = new THREE.Mesh(new THREE.BoxGeometry(0.2, 3, 8), interiorMat); dividerWall.position.set(0, 1.5, -1); houseGroup.add(dividerWall);

            // โซนห้องครัว (ซ้าย) -> เคาน์เตอร์ครัวทำอาหาร
            const kitchenCounter = new THREE.Mesh(new THREE.BoxGeometry(1.5, 0.8, 4), woodMat); kitchenCounter.position.set(-4, 0.4, -2); houseGroup.add(kitchenCounter);
            
            // โซนห้องนอน (ขวา) -> เตียงนอน + หมอน
            const bed = new THREE.Mesh(new THREE.BoxGeometry(3, 0.5, 4), woodMat); bed.position.set(3, 0.25, -2); houseGroup.add(bed);
            const pillow = new THREE.Mesh(new THREE.BoxGeometry(2, 0.15, 0.8), new THREE.MeshStandardMaterial({color:0xffffff})); pillow.position.set(3, 0.55, -3.5); houseGroup.add(pillow);

            // 3. พื้นชั้น 2 (เพดานชั้น 1) และ โซนห้องใต้หลังคา
            const floor2 = new THREE.Mesh(new THREE.PlaneGeometry(10, 12), new THREE.MeshStandardMaterial({ color: 0x3e2723 }));
            floor2.rotation.x = -Math.PI / 2; floor2.position.set(0, 3, 0); houseGroup.add(floor2);

            // บันไดชันๆ ขึ้นชั้น 2 สำหรับเด็ก (กล่องสี่เหลี่ยมต่อกัน)
            for(let i=0; i<6; i++) {
                const step = new THREE.Mesh(new THREE.BoxGeometry(1.5, 0.1, 0.4), woodMat);
                step.position.set(-0.8, 0.5 + (i*0.4), 2 - (i*0.4));
                houseGroup.add(step);
            }

            // หลังคาทรงสามเหลี่ยมขนาดใหญ่ ครอบเป็น "ห้องใต้หลังคา"
            const roofGeo = new THREE.ConeGeometry(8, 4, 4);
            const roofMat = new THREE.MeshStandardMaterial({ color: 0x331111 });
            const roof = new THREE.Mesh(roofGeo, roofMat);
            roof.position.set(0, 7.5, 0); roof.rotation.y = Math.PI / 4;
            houseGroup.add(roof);

            // ไฟในบ้าน (หลอดไฟนีออนสลัวๆ บรรยากาศอบอุ่น)
            const houseLight = new THREE.PointLight(0xffaa44, 1, 10);
            houseLight.position.set(0, 2.5, 0);
            houseGroup.add(houseLight);

            // 4. [ ระบบประตูเปิดได้จริง ]
            doorPivot = new THREE.Group();
            doorPivot.position.set(-1, 0, 6); // ยึดบานพับไว้ที่ขอบประตูขวา
            
            const doorMesh = new THREE.Mesh(new THREE.BoxGeometry(2, 2.2, 0.1), woodMat);
            doorMesh.position.set(1, 1.1, 0); // เลื่อนเนื้อประตูให้อมจุดหมุน บานพับจะอยู่ฝั่งซ้ายพอดี
            doorPivot.add(doorMesh);
            houseGroup.add(doorPivot);

            scene.add(houseGroup);

            // ==========================================
            // [ อัปเกรดถนนและโมเดลรถสปอร์ตมีไฟหน้า ]
            // ==========================================
            const road = new THREE.Mesh(new THREE.PlaneGeometry(1000, 16), new THREE.MeshStandardMaterial({ color: 0x1a1a1a, roughness: 0.3 }));
            road.rotation.x = -Math.PI / 2; road.position.set(0, 0.02, 22);
            scene.add(road);

            // สร้างรถสัญจร 2 คัน
            spawnAdvancedCar(-50, 19.5, 1); // เลนซ้าย วิ่งไปขวา
            spawnAdvancedCar(50, 24.5, -1); // เลนขวา วิ่งไปซ้าย
        }

        function spawnAdvancedCar(x, z, direction) {
            const carGroup = new THREE.Group();
            
            // ตัวถังรถสปอร์ต (ทรงกล่องซ้อนกันดูดีขึ้น)
            const body = new THREE.Mesh(new THREE.BoxGeometry(5, 0.8, 2.2), new THREE.MeshStandardMaterial({ color: direction > 0 ? 0xcc2222 : 0x2222cc, roughness: 0.1 }));
            body.position.y = 0.5;
            carGroup.add(body);

            const cabin = new THREE.Mesh(new THREE.BoxGeometry(2.5, 0.6, 1.8), new THREE.MeshStandardMaterial({ color: 0x111111, roughness: 0.1 }));
            cabin.position.set(-0.2, 1.1, 0);
            carGroup.add(cabin);

            // ล้อแม็กซ์ดำ
            const wheelGeo = new THREE.CylinderGeometry(0.4, 0.4, 0.4, 16);
            const wheelMat = new THREE.MeshStandardMaterial({ color: 0x0a0a0a, roughness: 0.5 });
            const wPositions = [[-1.5, 0.4, 1.1], [1.5, 0.4, 1.1], [-1.5, 0.4, -1.1], [1.5, 0.4, -1.1]];
            wPositions.forEach(pos => {
                const wheel = new THREE.Mesh(wheelGeo, wheelMat);
                wheel.rotation.x = Math.PI / 2; wheel.position.set(pos[0], pos[1], pos[2]);
                carGroup.add(wheel);
            });

            // !!! [ เพิ่มไฟหน้ารถยนต์ส่องสว่างตอนกลางคืน ] !!!
            // ไฟหน้าดวงซ้ายและขวา (เม็ดสีเหลืองนวล)
            const lightMeshGeo = new THREE.BoxGeometry(0.1, 0.2, 0.3);
            const lightMeshMat = new THREE.MeshBasicMaterial({ color: 0xffffff });
            
            const headlightL = new THREE.Mesh(lightMeshGeo, lightMeshMat); headlightL.position.set(2.5, 0.6, 0.8); carGroup.add(headlightL);
            const headlightR = new THREE.Mesh(lightMeshGeo, lightMeshMat); headlightR.position.set(2.5, 0.6, -0.8); carGroup.add(headlightR);

            // ลำแสงไฟหน้ารถยนต์ ส่องทะลุความมืด (SpotLight)
            const spotLight = new THREE.SpotLight(0xffffff, 4, 30, Math.PI / 5, 0.5, 1);
            spotLight.position.set(2.5, 0.6, 0);
            
            // สร้างเป้าหมายให้ลำแสงส่องไปข้างหน้ารถ
            const lightTarget = new THREE.Object3D();
            lightTarget.position.set(10, 0.6, 0);
            carGroup.add(lightTarget);
            spotLight.target = lightTarget;
            
            carGroup.add(spotLight);

            carGroup.position.set(x, 0, z);
            carGroup.userData = { direction: direction };
            
            // หันทิศทางรถให้ถูกเลน
            if(direction < 0) carGroup.rotation.y = Math.PI;

            scene.add(carGroup);
            cars.push(carGroup);
        }

        // ==========================================
        // [ ระบบเอฟเฟกต์ฝนตกกลางคืน ]
        // ==========================================
        function createRain() {
            const rainGeo = new THREE.BufferGeometry();
            const rainPositions = new Float32Array(rainCount * 3);

            for (let i = 0; i < rainCount * 3; i += 3) {
                rainPositions[i] = (Math.random() - 0.5) * 80;      // X กว้างกระจายตัว
                rainPositions[i + 1] = Math.random() * 40;          // Y ความสูงฟ้า
                rainPositions[i + 2] = (Math.random() - 0.5) * 80;  // Z
            }

            rainGeo.setAttribute('position', new THREE.BufferAttribute(rainPositions, 3));
            
            // หน้าตาเม็ดฝนสีขาวฟ้าโปร่งแสง
            const rainMat = new THREE.PointsMaterial({
                color: 0xaaaaee,
                size: 0.1,
                transparent: true,
                opacity: 0.6
            });

            rainParticles = new THREE.Points(rainGeo, rainMat);
            scene.add(rainParticles);
        }

        function setupTouchEvents() {
            // ผูกปุ่มกดเดิน
            const bindBtn = (id, callback) => {
                const el = document.getElementById(id);
                el.addEventListener('touchstart', (e) => { e.preventDefault(); callback(true); });
                el.addEventListener('touchend', () => callback(false));
            };

            bindBtn('btn-up', (val) => moveForward = val);
            bindBtn('btn-down', (val) => moveBackward = val);
            bindBtn('btn-left', (val) => moveLeft = val);
            bindBtn('btn-right', (val) => moveRight = val);

            // ปุ่มแอคชั่น เปิด/ปิดประตู
            document.getElementById('action-btn').addEventListener('touchstart', (e) => {
                e.preventDefault();
                // เช็กระยะห่างระหว่างตัวเด็กกับหน้าประตูบ้าน
                const distToDoor = camera.position.distanceTo(new THREE.Vector3(0, 0.8, 4));
                if (distToDoor < 4) { // ถ้าอยู่ใกล้ประตูไม่เกิน 4 หน่วย
                    isDoorOpen = !isDoorOpen;
                    targetDoorRotation = isDoorOpen ? Math.PI / 2 : 0; // เปิดอ้าออก 90 องศา
                }
            });

            // หมุนหน้าจอ (ฝั่งขวา)
            window.addEventListener('touchstart', (e) => {
                for (let i = 0; i < e.changedTouches.length; i++) {
                    const t = e.changedTouches[i];
                    if (t.clientX > window.innerWidth / 2 && touchLookId === null) {
                        touchLookId = t.identifier; touchLookStartX = t.clientX; touchLookStartY = t.clientY;
                    }
                }
            });

            window.addEventListener('touchmove', (e) => {
                for (let i = 0; i < e.touches.length; i++) {
                    const t = e.touches[i];
                    if (t.identifier === touchLookId) {
                        let dx = t.clientX - touchLookStartX;
                        let dy = t.clientY - touchLookStartY;
                        yaw -= dx * 0.005; pitch -= dy * 0.005;
                        pitch = Math.max(-Math.PI/3, Math.min(Math.PI/3, pitch));
                        touchLookStartX = t.clientX; touchLookStartY = t.clientY;
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

            // 1. หมุนมุมมองกล้อง
            const target = new THREE.Vector3(Math.sin(yaw) * Math.cos(pitch), Math.sin(pitch), Math.cos(yaw) * Math.cos(pitch));
            camera.lookAt(camera.position.clone().add(target));

            // 2. คำนวณเดิน (ล็อกความสูงไว้ที่ 0.8 เสมอเพราะตัวเราเป็นเด็ก)
            const speed = 0.08;
            const forward = new THREE.Vector3(target.x, 0, target.z).normalize();
            const right = new THREE.Vector3().crossVectors(camera.up, forward).normalize();

            if (moveForward) camera.position.addScaledVector(forward, speed);
            if (moveBackward) camera.position.addScaledVector(forward, -speed);
            if (moveLeft) camera.position.addScaledVector(right, -speed);
            if (moveRight) camera.position.addScaledVector(right, speed);
            camera.position.y = 0.8; 

            // 3. อนิเมชั่นประตูเปิดค่อยๆ สมูทวนลูปเล็งเป้า
            doorPivot.rotation.y += (targetDoorRotation - doorPivot.rotation.y) * 0.1;

            // 4. แอนิเมชันฝันตก (เลื่อนตำแหน่ง Y เม็ดฝนลงด้านล่างต่อเนื่อง)
            const positions = rainParticles.geometry.attributes.position.array;
            for (let i = 1; i < positions.length; i += 3) {
                positions[i] -= 0.4; // ความเร็วเม็ดฝนตกดิ่งลง
                if (positions[i] < 0) { // ตกกระทบพื้นแล้ววาร์ปกลับไปเกิดใหม่บนฟ้า
                    positions[i] = 40;
                }
            }
            rainParticles.geometry.attributes.position.needsUpdate = true;

            // 5. รถวิ่งสัญจรพร้อมส่องไฟหน้าสว่างไสวบนถนน
            cars.forEach(car => {
                car.position.x += carSpeed * car.userData.direction;
                if (car.position.x > 80 && car.userData.direction === 1) car.position.x = -80;
                if (car.position.x < -80 && car.userData.direction === -1) car.position.x = 80;
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
