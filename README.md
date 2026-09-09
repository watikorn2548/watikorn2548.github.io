# watikorn2548.github.io

<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Model Interactive with Three.js</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden; /* ซ่อน Scrollbar */
            background-color: #1a1a1a;
            font-family: Arial, sans-serif;
        }

        #info {
            position: absolute;
            top: 20px;
            left: 20px;
            color: #ffffff;
            background: rgba(0, 0, 0, 0.6);
            padding: 10px 15px;
            border-radius: 8px;
            pointer-events: none; /* เพื่อไม่ให้บังการลากเม้าส์บน Canvas */
            font-size: 14px;
        }

        /* ปรับขนาด Canvas ให้เต็มจอ */
        canvas {
            display: block;
            width: 100vw;
            height: 100vh;
        }
    </style>

    <!-- นำเข้า Three.js และ OrbitControls ผ่าน CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
</head>
<body>

    <div id="info">
        📌 <strong>วิธีควบคุม (Interactive):</strong><br>
        • คลิกซ้าย + ลาก: หมุนมุมมอง (Rotate)<br>
        • คลิกขวา + ลาก: เลื่อนมุมมอง (Pan)<br>
        • สกอร์ลเม้าส์: ขยาย/ย่อ (Zoom)
    </div>

    <script>
        // 1. สร้าง Scene (ฉาก)
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x1a1a2e); // สีพื้นหลัง

        // 2. สร้าง Camera (กล้อง)
        const camera = new THREE.PerspectiveCamera(
            75, // Field of view
            window.innerWidth / window.innerHeight, // Aspect ratio
            0.1, // Near clipping plane
            1000 // Far clipping plane
        );
        camera.position.set(3, 3, 5);

        // 3. สร้าง Renderer (ตัววาดภาพ)
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(window.devicePixelRatio);
        renderer.shadowMap.enabled = true; // เปิดใช้งานแสงเงา
        document.body.appendChild(renderer.domElement);

        // 4. เพิ่ม OrbitControls (ระบบ Interactive ตอบสนองการใช้เม้าส์)
        const controls = new THREE.OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true; // เพิ่มความนุ่มนวลเวลาหมุน
        controls.dampingFactor = 0.05;

        // 5. สร้างแสง (Lighting)
        // แสงสว่างทั่วไป (Ambient Light)
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.4);
        scene.add(ambientLight);

        // แสงส่องทิศทาง (Directional Light - สร้างเงา)
        const dirLight = new THREE.DirectionalLight(0xffffff, 0.8);
        dirLight.position.set(5, 10, 7);
        dirLight.castShadow = true;
        scene.add(dirLight);

        // 6. สร้างโมเดล 3D ตัวอย่าง (รูปทรงเรขาคณิตสามเหลี่ยมหลายเหลี่ยม / TorusKnot)
        const geometry = new THREE.TorusKnotGeometry(1, 0.3, 100, 16);
        const material = new THREE.MeshStandardMaterial({
            color: 0x00fff0,
            roughness: 0.2,
            metalness: 0.5
        });
        const mesh = new THREE.Mesh(geometry, material);
        mesh.castShadow = true;
        mesh.position.y = 0.5;
        scene.add(mesh);

        // สร้างพื้นหลังรองรับเงา (Ground Grid)
        const planeGeometry = new THREE.PlaneGeometry(10, 10);
        const planeMaterial = new THREE.ShadowMaterial({ opacity: 0.3 });
        const plane = new THREE.Mesh(planeGeometry, planeMaterial);
        plane.rotation.x = -Math.PI / 2;
        plane.position.y = -1;
        plane.receiveShadow = true;
        scene.add(plane);

        // เพิ่มเส้นตารางที่พื้น (Grid Helper)
        const gridHelper = new THREE.GridHelper(10, 10, 0x444444, 0x222222);
        gridHelper.position.y = -1;
        scene.add(gridHelper);

        // 7. Animation Loop (การอัปเดตเฟรมภาพ)
        function animate() {
            requestAnimationFrame(animate);

            // หมุนโมเดลเล็กน้อยแบบอัตโนมัติ
            mesh.rotation.x += 0.005;
            mesh.rotation.y += 0.005;

            // อัปเดต OrbitControls
            controls.update();

            // วาดฉากใหม่
            renderer.render(scene, camera);
        }
        animate();

        // 8. รองรับการเปลี่ยนขนาดหน้าจอ (Responsive)
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
