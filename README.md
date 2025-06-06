<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Symulator Mebli 3D</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: #f0f0f0;
            overflow: hidden;
        }
        
        #container {
            display: flex;
            height: 100vh;
        }
        
        #sidebar {
            width: 300px;
            min-width: 250px;
            flex-shrink: 0;
            max-width: 400px;
            background: white;
            border-right: 2px solid #ddd;
            padding: 15px;
            overflow-y: auto;
            overflow-x: hidden;
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
            flex-shrink: 0;
        }
        
        #viewport {
            flex: 1;
            position: relative;
            background: white;
        }
        
        h1 {
            font-size: 24px;
            margin-bottom: 20px;
            color: #8B4513;
            text-align: center;
        }
        
        .section {
            margin-bottom: 25px;
        }
        
        .section h3 {
            font-size: 16px;
            margin-bottom: 10px;
            color: #654321;
            border-bottom: 2px solid #8B4513;
            padding-bottom: 5px;
        }
        
        .furniture-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
            margin-bottom: 15px;
        }
        
        .furniture-btn {
            padding: 8px 4px;
            border: 2px solid #8B4513;
            background: white;
            cursor: pointer;
            border-radius: 6px;
            font-size: 10px;
            font-weight: bold;
            color: #8B4513;
            transition: all 0.3s;
            text-align: center;
            min-height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            word-wrap: break-word;
            line-height: 1.2;
        }
        
        .furniture-btn:hover {
            background: #8B4513;
            color: white;
            transform: translateY(-2px);
        }
        
        .furniture-btn.active {
            background: #8B4513;
            color: white;
        }
        
        .wood-options {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
        }
        
        .wood-btn {
            padding: 10px;
            border: 2px solid #D2691E;
            background: white;
            cursor: pointer;
            border-radius: 6px;
            font-size: 11px;
            font-weight: bold;
            color: #D2691E;
            transition: all 0.3s;
            text-align: center;
        }
        
        .wood-btn:hover {
            background: #D2691E;
            color: white;
        }
        
        .wood-btn.active {
            background: #D2691E;
            color: white;
        }
        
        .controls {
            margin-top: 20px;
            padding-top: 20px;
            border-top: 2px solid #ddd;
        }
        
        .control-group {
            margin-bottom: 15px;
        }
        
        .control-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: #654321;
        }
        
        .control-group button {
            width: 100%;
            padding: 10px;
            margin: 2px 0;
            border: 2px solid #8B4513;
            background: white;
            color: #8B4513;
            cursor: pointer;
            border-radius: 6px;
            font-weight: bold;
            transition: all 0.3s;
        }
        
        .control-group button:hover {
            background: #8B4513;
            color: white;
        }
        
        #info {
            position: absolute;
            bottom: 20px;
            left: 20px;
            background: rgba(255,255,255,0.9);
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.2);
            font-weight: bold;
            color: #8B4513;
        }
    </style>
</head>
<body>
    <div id="container">
        <div id="sidebar">
            <h1>🪵 Symulator Mebli</h1>
            
            <div class="section">
                <h3>Rodzaj Mebla</h3>
                <div class="furniture-grid">
                    <button class="furniture-btn active" data-furniture="table">Stół</button>
                    <button class="furniture-btn" data-furniture="bed">Łóżko</button>
                    <button class="furniture-btn" data-furniture="chair">Krzesło</button>
                    <button class="furniture-btn" data-furniture="wardrobe">Szafa</button>
                    <button class="furniture-btn" data-furniture="desk">Biurko</button>
                    <button class="furniture-btn" data-furniture="shelf">Półka</button>
                    <button class="furniture-btn" data-furniture="bookshelf">Regał</button>
                    <button class="furniture-btn" data-furniture="nightstand">Szafka Nocna</button>
                    <button class="furniture-btn" data-furniture="coffeetable">Stolik Kawowy</button>
                </div>
            </div>
            
            <div class="section">
                <h3>Rodzaj Drewna</h3>
                <div class="wood-options">
                    <button class="wood-btn active" data-wood="oak">Dąb</button>
                    <button class="wood-btn" data-wood="pine">Sosna</button>
                    <button class="wood-btn" data-wood="mahogany">Mahoń</button>
                    <button class="wood-btn" data-wood="walnut">Orzech</button>
                    <button class="wood-btn" data-wood="cherry">Wiśnia</button>
                    <button class="wood-btn" data-wood="birch">Brzoza</button>
                </div>
            </div>
            
            <div class="controls">
                <div class="control-group">
                    <label>Widok</label>
                    <button onclick="resetCamera()">Resetuj Kamerę</button>
                    <button onclick="toggleBackground()">Zmień Tło</button>
                </div>
                
                <div class="control-group">
                    <label>Obrót</label>
                    <button onclick="autoRotate = !autoRotate">
                        <span id="rotateText">Włącz Auto-Obrót</span>
                    </button>
                </div>
            </div>
        </div>
        
        <div id="viewport"></div>
        <div id="info">
            <div>Mebel: <span id="current-furniture">Stół</span></div>
            <div>Drewno: <span id="current-wood">Dąb</span></div>
        </div>
    </div>

    <script>
        // Global variables
        let scene, camera, renderer, currentFurniture;
        let autoRotate = false;
        let isWhiteBackground = true;
        
        // Wood textures and colors
        const woodProperties = {
            oak: { color: 0xDEB887, name: 'Dąb' },
            pine: { color: 0xF4A460, name: 'Sosna' },
            mahogany: { color: 0x8B4513, name: 'Mahoń' },
            walnut: { color: 0x654321, name: 'Orzech' },
            cherry: { color: 0xDC143C, name: 'Wiśnia' },
            birch: { color: 0xFFF8DC, name: 'Brzoza' }
        };
        
        let currentWoodType = 'oak';
        let currentFurnitureType = 'table';
        
        // Initialize 3D scene
        function init() {
            // Scene
            scene = new THREE.Scene();
            scene.background = new THREE.Color(0xffffff);
            
            // Camera
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(5, 5, 5);
            camera.lookAt(0, 0, 0);
            
            // Renderer
            renderer = new THREE.WebGLRenderer({ antialias: true });
            renderer.setSize(window.innerWidth - 300, window.innerHeight);
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;
            document.getElementById('viewport').appendChild(renderer.domElement);
            
            // Lighting
            const ambientLight = new THREE.AmbientLight(0x404040, 0.6);
            scene.add(ambientLight);
            
            const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
            directionalLight.position.set(10, 10, 5);
            directionalLight.castShadow = true;
            directionalLight.shadow.mapSize.width = 2048;
            directionalLight.shadow.mapSize.height = 2048;
            scene.add(directionalLight);
            
            // No ground - removed as requested
            
            // Create initial furniture
            createFurniture(currentFurnitureType);
            
            // Mouse controls
            setupMouseControls();
            
            // Start animation loop
            animate();
        }
        
        // Create furniture based on type
        function createFurniture(type) {
            // Remove existing furniture
            if (currentFurniture) {
                scene.remove(currentFurniture);
            }
            
            const wood = woodProperties[currentWoodType];
            const material = new THREE.MeshLambertMaterial({ color: wood.color });
            
            switch(type) {
                case 'table':
                    currentFurniture = createTable(material);
                    break;
                case 'bed':
                    currentFurniture = createBed(material);
                    break;
                case 'chair':
                    currentFurniture = createChair(material);
                    break;
                case 'wardrobe':
                    currentFurniture = createWardrobe(material);
                    break;
                case 'desk':
                    currentFurniture = createDesk(material);
                    break;
                case 'shelf':
                    currentFurniture = createShelf(material);
                    break;
                case 'bookshelf':
                    currentFurniture = createBookshelf(material);
                    break;
                case 'nightstand':
                    currentFurniture = createNightstand(material);
                    break;
                case 'coffeetable':
                    currentFurniture = createCoffeeTable(material);
                    break;
            }
            
            if (currentFurniture) {
                currentFurniture.castShadow = true;
                currentFurniture.receiveShadow = true;
                scene.add(currentFurniture);
            }
        }
        
        // Furniture creation functions
        function createTable(material) {
            const group = new THREE.Group();
            
            // Table top
            const topGeometry = new THREE.BoxGeometry(4, 0.2, 2);
            const top = new THREE.Mesh(topGeometry, material);
            top.position.y = 1.5;
            top.castShadow = true;
            group.add(top);
            
            // Table legs
            const legGeometry = new THREE.BoxGeometry(0.2, 1.5, 0.2);
            const positions = [
                [-1.8, 0.75, -0.8],
                [1.8, 0.75, -0.8],
                [-1.8, 0.75, 0.8],
                [1.8, 0.75, 0.8]
            ];
            
            positions.forEach(pos => {
                const leg = new THREE.Mesh(legGeometry, material);
                leg.position.set(...pos);
                leg.castShadow = true;
                group.add(leg);
            });
            
            return group;
        }
        
        function createBookshelf(material) {
            const group = new THREE.Group();
            
            // Back panel
            const backGeometry = new THREE.BoxGeometry(3, 5, 0.1);
            const back = new THREE.Mesh(backGeometry, material);
            back.position.set(0, 2.5, -0.45);
            back.castShadow = true;
            group.add(back);
            
            // Side panels
            const sideGeometry = new THREE.BoxGeometry(0.1, 5, 1);
            const leftSide = new THREE.Mesh(sideGeometry, material);
            leftSide.position.set(-1.45, 2.5, 0);
            leftSide.castShadow = true;
            group.add(leftSide);
            
            const rightSide = new THREE.Mesh(sideGeometry, material);
            rightSide.position.set(1.45, 2.5, 0);
            rightSide.castShadow = true;
            group.add(rightSide);
            
            // Top and bottom
            const horizontalGeometry = new THREE.BoxGeometry(3, 0.1, 1);
            const top = new THREE.Mesh(horizontalGeometry, material);
            top.position.set(0, 4.95, 0);
            top.castShadow = true;
            group.add(top);
            
            const bottom = new THREE.Mesh(horizontalGeometry, material);
            bottom.position.set(0, 0.05, 0);
            bottom.castShadow = true;
            group.add(bottom);
            
            // Shelves
            const shelfGeometry = new THREE.BoxGeometry(2.8, 0.1, 1);
            const shelfPositions = [1, 2, 3, 4];
            
            shelfPositions.forEach(y => {
                const shelf = new THREE.Mesh(shelfGeometry, material);
                shelf.position.set(0, y, 0);
                shelf.castShadow = true;
                group.add(shelf);
            });
            
            return group;
        }
        
        function createNightstand(material) {
            const group = new THREE.Group();
            
            // Main body
            const bodyGeometry = new THREE.BoxGeometry(1.5, 2, 1.2);
            const body = new THREE.Mesh(bodyGeometry, material);
            body.position.y = 1;
            body.castShadow = true;
            group.add(body);
            
            // Top
            const topGeometry = new THREE.BoxGeometry(1.6, 0.1, 1.3);
            const top = new THREE.Mesh(topGeometry, material);
            top.position.y = 2.05;
            top.castShadow = true;
            group.add(top);
            
            // Drawer
            const drawerGeometry = new THREE.BoxGeometry(1.3, 0.4, 1);
            const drawer = new THREE.Mesh(drawerGeometry, material);
            drawer.position.set(0, 1.5, 0.65);
            drawer.castShadow = true;
            group.add(drawer);
            
            // Drawer handle
            const handleGeometry = new THREE.BoxGeometry(0.3, 0.05, 0.05);
            const handle = new THREE.Mesh(handleGeometry, material);
            handle.position.set(0, 1.5, 1.18);
            handle.castShadow = true;
            group.add(handle);
            
            // Door
            const doorGeometry = new THREE.BoxGeometry(1.3, 1.2, 0.05);
            const door = new THREE.Mesh(doorGeometry, material);
            door.position.set(0, 0.6, 0.65);
            door.castShadow = true;
            group.add(door);
            
            // Door handle
            const doorHandle = new THREE.Mesh(handleGeometry, material);
            doorHandle.position.set(0.4, 0.6, 0.72);
            doorHandle.castShadow = true;
            group.add(doorHandle);
            
            return group;
        }
        
        function createCoffeeTable(material) {
            const group = new THREE.Group();
            
            // Table top
            const topGeometry = new THREE.BoxGeometry(3, 0.15, 1.5);
            const top = new THREE.Mesh(topGeometry, material);
            top.position.y = 0.8;
            top.castShadow = true;
            group.add(top);
            
            // Legs
            const legGeometry = new THREE.BoxGeometry(0.15, 0.8, 0.15);
            const legPositions = [
                [-1.3, 0.4, -0.6],
                [1.3, 0.4, -0.6],
                [-1.3, 0.4, 0.6],
                [1.3, 0.4, 0.6]
            ];
            
            legPositions.forEach(pos => {
                const leg = new THREE.Mesh(legGeometry, material);
                leg.position.set(...pos);
                leg.castShadow = true;
                group.add(leg);
            });
            
            // Lower shelf
            const shelfGeometry = new THREE.BoxGeometry(2.8, 0.1, 1.3);
            const shelf = new THREE.Mesh(shelfGeometry, material);
            shelf.position.y = 0.3;
            shelf.castShadow = true;
            group.add(shelf);
            
            return group;
        }
        
        function createBed(material) {
            const group = new THREE.Group();
            
            // Mattress
            const mattressGeometry = new THREE.BoxGeometry(4, 0.3, 6);
            const mattressMaterial = new THREE.MeshLambertMaterial({ color: 0xffffff });
            const mattress = new THREE.Mesh(mattressGeometry, mattressMaterial);
            mattress.position.y = 0.5;
            mattress.castShadow = true;
            group.add(mattress);
            
            // Bed frame
            const frameGeometry = new THREE.BoxGeometry(4.2, 0.2, 6.2);
            const frame = new THREE.Mesh(frameGeometry, material);
            frame.position.y = 0.3;
            frame.castShadow = true;
            group.add(frame);
            
            // Headboard
            const headboardGeometry = new THREE.BoxGeometry(4.2, 2, 0.2);
            const headboard = new THREE.Mesh(headboardGeometry, material);
            headboard.position.set(0, 1.3, -3);
            headboard.castShadow = true;
            group.add(headboard);
            
            return group;
        }
        
        function createChair(material) {
            const group = new THREE.Group();
            
            // Seat
            const seatGeometry = new THREE.BoxGeometry(1, 0.1, 1);
            const seat = new THREE.Mesh(seatGeometry, material);
            seat.position.y = 1;
            seat.castShadow = true;
            group.add(seat);
            
            // Backrest
            const backGeometry = new THREE.BoxGeometry(1, 1.5, 0.1);
            const back = new THREE.Mesh(backGeometry, material);
            back.position.set(0, 1.75, -0.45);
            back.castShadow = true;
            group.add(back);
            
            // Legs
            const legGeometry = new THREE.BoxGeometry(0.1, 1, 0.1);
            const legPositions = [
                [-0.4, 0.5, -0.4],
                [0.4, 0.5, -0.4],
                [-0.4, 0.5, 0.4],
                [0.4, 0.5, 0.4]
            ];
            
            legPositions.forEach(pos => {
                const leg = new THREE.Mesh(legGeometry, material);
                leg.position.set(...pos);
                leg.castShadow = true;
                group.add(leg);
            });
            
            return group;
        }
        
        function createWardrobe(material) {
            const group = new THREE.Group();
            
            // Main body - 100% wood
            const bodyGeometry = new THREE.BoxGeometry(3, 4, 1.5);
            const body = new THREE.Mesh(bodyGeometry, material);
            body.position.y = 2;
            body.castShadow = true;
            group.add(body);
            
            // Left door - wood
            const doorGeometry = new THREE.BoxGeometry(1.4, 3.8, 0.05);
            const leftDoor = new THREE.Mesh(doorGeometry, material);
            leftDoor.position.set(-0.7, 2, 0.78);
            leftDoor.castShadow = true;
            group.add(leftDoor);
            
            // Right door - wood
            const rightDoor = new THREE.Mesh(doorGeometry, material);
            rightDoor.position.set(0.7, 2, 0.78);
            rightDoor.castShadow = true;
            group.add(rightDoor);
            
            // Door handles - wood
            const handleGeometry = new THREE.BoxGeometry(0.05, 0.3, 0.05);
            const leftHandle = new THREE.Mesh(handleGeometry, material);
            leftHandle.position.set(-0.3, 2, 0.82);
            leftHandle.castShadow = true;
            group.add(leftHandle);
            
            const rightHandle = new THREE.Mesh(handleGeometry, material);
            rightHandle.position.set(0.3, 2, 0.82);
            rightHandle.castShadow = true;
            group.add(rightHandle);
            
            // Internal shelves - wood
            const shelfGeometry = new THREE.BoxGeometry(2.8, 0.05, 1.4);
            const shelf1 = new THREE.Mesh(shelfGeometry, material);
            shelf1.position.set(0, 2.5, 0);
            shelf1.castShadow = true;
            group.add(shelf1);
            
            const shelf2 = new THREE.Mesh(shelfGeometry, material);
            shelf2.position.set(0, 1.5, 0);
            shelf2.castShadow = true;
            group.add(shelf2);
            
            return group;
        }
        
        function createDesk(material) {
            const group = new THREE.Group();
            
            // Desktop
            const topGeometry = new THREE.BoxGeometry(4, 0.1, 2);
            const top = new THREE.Mesh(topGeometry, material);
            top.position.y = 1.5;
            top.castShadow = true;
            group.add(top);
            
            // Legs
            const legGeometry = new THREE.BoxGeometry(0.1, 1.5, 0.1);
            const legPositions = [
                [-1.9, 0.75, -0.9],
                [1.9, 0.75, -0.9],
                [-1.9, 0.75, 0.9],
                [1.9, 0.75, 0.9]
            ];
            
            legPositions.forEach(pos => {
                const leg = new THREE.Mesh(legGeometry, material);
                leg.position.set(...pos);
                leg.castShadow = true;
                group.add(leg);
            });
            
            // Drawer
            const drawerGeometry = new THREE.BoxGeometry(1.5, 0.3, 1.8);
            const drawer = new THREE.Mesh(drawerGeometry, material);
            drawer.position.set(-1, 1.2, 0);
            drawer.castShadow = true;
            group.add(drawer);
            
            return group;
        }
        
        function createShelf(material) {
            const group = new THREE.Group();
            
            // Shelves
            const shelfGeometry = new THREE.BoxGeometry(3, 0.1, 1);
            const shelfPositions = [0.5, 1.2, 1.9, 2.6];
            
            shelfPositions.forEach(y => {
                const shelf = new THREE.Mesh(shelfGeometry, material);
                shelf.position.y = y;
                shelf.castShadow = true;
                group.add(shelf);
            });
            
            // Side panels
            const sideGeometry = new THREE.BoxGeometry(0.1, 3, 1);
            const leftSide = new THREE.Mesh(sideGeometry, material);
            leftSide.position.set(-1.45, 1.55, 0);
            leftSide.castShadow = true;
            group.add(leftSide);
            
            const rightSide = new THREE.Mesh(sideGeometry, material);
            rightSide.position.set(1.45, 1.55, 0);
            rightSide.castShadow = true;
            group.add(rightSide);
            
            // Back panel
            const backGeometry = new THREE.BoxGeometry(3, 3, 0.1);
            const back = new THREE.Mesh(backGeometry, material);
            back.position.set(0, 1.55, -0.45);
            back.castShadow = true;
            group.add(back);
            
            return group;
        }
        
        // Mouse controls
        let mouseDown = false;
        let mouseX = 0;
        let mouseY = 0;
        
        function setupMouseControls() {
            const viewport = document.getElementById('viewport');
            
            viewport.addEventListener('mousedown', (e) => {
                mouseDown = true;
                mouseX = e.clientX;
                mouseY = e.clientY;
            });
            
            viewport.addEventListener('mouseup', () => {
                mouseDown = false;
            });
            
            viewport.addEventListener('mousemove', (e) => {
                if (!mouseDown) return;
                
                const deltaX = e.clientX - mouseX;
                const deltaY = e.clientY - mouseY;
                
                // Rotate camera around the scene
                const spherical = new THREE.Spherical();
                spherical.setFromVector3(camera.position);
                spherical.theta -= deltaX * 0.01;
                spherical.phi += deltaY * 0.01;
                spherical.phi = Math.max(0.1, Math.min(Math.PI - 0.1, spherical.phi));
                
                camera.position.setFromSpherical(spherical);
                camera.lookAt(0, 1, 0);
                
                mouseX = e.clientX;
                mouseY = e.clientY;
            });
            
            viewport.addEventListener('wheel', (e) => {
                const distance = camera.position.distanceTo(new THREE.Vector3(0, 1, 0));
                const newDistance = Math.max(2, Math.min(15, distance + e.deltaY * 0.01));
                camera.position.normalize().multiplyScalar(newDistance);
            });
        }
        
        // Animation loop
        function animate() {
            requestAnimationFrame(animate);
            
            if (autoRotate && currentFurniture) {
                currentFurniture.rotation.y += 0.01;
            }
            
            renderer.render(scene, camera);
        }
        
        // Event listeners
        document.querySelectorAll('.furniture-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                document.querySelectorAll('.furniture-btn').forEach(b => b.classList.remove('active'));
                e.target.classList.add('active');
                
                currentFurnitureType = e.target.dataset.furniture;
                createFurniture(currentFurnitureType);
                
                const furnitureNames = {
                    table: 'Stół',
                    bed: 'Łóżko',
                    chair: 'Krzesło',
                    wardrobe: 'Szafa',
                    desk: 'Biurko',
                    shelf: 'Półka',
                    bookshelf: 'Regał',
                    nightstand: 'Szafka Nocna',
                    coffeetable: 'Stolik Kawowy'
                };
                
                document.getElementById('current-furniture').textContent = furnitureNames[currentFurnitureType];
            });
        });
        
        document.querySelectorAll('.wood-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                document.querySelectorAll('.wood-btn').forEach(b => b.classList.remove('active'));
                e.target.classList.add('active');
                
                currentWoodType = e.target.dataset.wood;
                createFurniture(currentFurnitureType);
                
                document.getElementById('current-wood').textContent = woodProperties[currentWoodType].name;
            });
        });
        
        // Control functions
        function resetCamera() {
            camera.position.set(5, 5, 5);
            camera.lookAt(0, 1, 0);
        }
        
        function toggleBackground() {
            isWhiteBackground = !isWhiteBackground;
            scene.background = new THREE.Color(isWhiteBackground ? 0xffffff : 0x000000);
        }
        
        // Auto-rotate toggle
        function toggleAutoRotate() {
            autoRotate = !autoRotate;
            document.getElementById('rotateText').textContent = autoRotate ? 'Wyłącz Auto-Obrót' : 'Włącz Auto-Obrót';
        }
        
        // Resize handler
        window.addEventListener('resize', () => {
            const sidebarWidth = document.getElementById('sidebar').offsetWidth;
            camera.aspect = (window.innerWidth - sidebarWidth) / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth - sidebarWidth, window.innerHeight);
        });
        
        // Initialize the application
        init();
    </script>
</body>
</html>
