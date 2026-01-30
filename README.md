# <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Happy Birthday!</title>
    
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;1,400&family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap" rel="stylesheet">
    
    <script src="https://cdn.tailwindcss.com"></script>
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
    
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

    <style>
        /* --- Global Reset & Variables --- */
        :root {
            --font-heading: 'Playfair Display', serif;
            --font-body: 'Plus Jakarta Sans', sans-serif;
            --glass-bg: rgba(12, 10, 9, 0.65);
            --glass-border: rgba(255, 255, 255, 0.1);
            --glass-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
        }

        body {
            margin: 0;
            overflow: hidden; /* Prevent scroll */
            font-family: var(--font-body);
            background-color: #0c0a09;
            color: #f3f4f6;
            height: 100vh;
            width: 100vw;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        /* --- Aurora Background Animation --- */
        .aurora-bg {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -2;
            background: radial-gradient(circle at 15% 50%, hsla(333,70%,55%,.4), transparent 25%),
                        radial-gradient(circle at 85% 30%, hsla(282, 82%, 54%, .3), transparent 25%),
                        radial-gradient(circle at 50% 50%, hsla(210, 89%, 60%, .3), transparent 50%),
                        radial-gradient(circle at 50% 80%, hsla(35, 90%, 60%, .3), transparent 50%);
            background-size: 200% 200%;
            animation: auroraBreath 15s ease infinite alternate;
            filter: blur(40px);
        }

        @keyframes auroraBreath {
            0% { background-position: 0% 50%; transform: scale(1); }
            50% { background-position: 100% 50%; transform: scale(1.1); }
            100% { background-position: 0% 50%; transform: scale(1); }
        }

        /* --- Three.js Canvas --- */
        #heart-canvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
            pointer-events: none;
        }

        /* --- Glassmorphism Card Styles --- */
        .glass-card {
            background: var(--glass-bg);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border);
            box-shadow: var(--glass-shadow);
            border-radius: 24px;
            padding: 2.5rem;
            width: 90%;
            max-width: 600px; /* Standard width */
            position: relative;
            transform-style: preserve-3d;
            overflow: hidden;
        }

        /* For the Bento Grid step, we might need a bit more width on desktop */
        .glass-card.wide {
            max-width: 800px;
        }

        /* --- Typography & Gradients --- */
        h1, h2 {
            font-family: var(--font-heading);
            background: linear-gradient(to right, #fbcfe8, #fff, #f472b6);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            line-height: 1.2;
        }

        .pulsate {
            animation: pulse-animation 2s infinite;
        }

        @keyframes pulse-animation {
            0% { transform: scale(1); opacity: 1; text-shadow: 0 0 10px rgba(244, 114, 182, 0.5); }
            50% { transform: scale(1.1); opacity: 0.9; text-shadow: 0 0 20px rgba(244, 114, 182, 0.8); }
            100% { transform: scale(1); opacity: 1; text-shadow: 0 0 10px rgba(244, 114, 182, 0.5); }
        }

        /* --- Button Styles --- */
        .btn-primary {
            background: linear-gradient(135deg, #ec4899, #ef4444);
            color: white;
            font-weight: 700;
            padding: 12px 36px;
            border-radius: 9999px; /* Pill shape */
            border: none;
            cursor: pointer;
            font-family: var(--font-body);
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            box-shadow: 0 0 15px rgba(236, 72, 153, 0.5);
            position: relative;
            overflow: hidden;
            letter-spacing: 0.5px;
        }

        .btn-primary:hover {
            transform: scale(1.05) translateY(-2px);
            box-shadow: 0 0 25px rgba(236, 72, 153, 0.8);
        }

        .btn-primary:active {
            transform: scale(0.98) translateY(0);
        }

        .btn-primary:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            filter: grayscale(100%);
        }

        /* --- Progress Bar --- */
        .progress-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 6px;
            background: rgba(255, 255, 255, 0.1);
            z-index: 50;
        }
        
        .progress-bar {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, #ec4899, #ef4444);
            transition: width 0.8s ease-in-out;
            box-shadow: 0 0 10px rgba(236, 72, 153, 0.7);
        }

        /* --- Utility & Layout --- */
        .step {
            display: none; /* Hidden by default */
            flex-direction: column;
            align-items: center;
            text-align: center;
            gap: 1.5rem;
            width: 100%;
        }

        .step.active {
            display: flex;
        }

        /* Bento Grid Specifics */
        .bento-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 1rem;
            width: 100%;
            text-align: left;
        }
        
        @media (min-width: 768px) {
            .bento-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        .bento-card {
            background: rgba(255,255,255,0.03);
            border: 1px solid rgba(255,255,255,0.05);
            border-radius: 16px;
            padding: 1.5rem;
            transition: transform 0.3s ease;
        }
        
        .bento-card:hover {
            transform: translateY(-3px);
            background: rgba(255,255,255,0.05);
        }

        .bento-span-2 {
            grid-column: span 1;
        }
        
        @media (min-width: 768px) {
            .bento-span-2 {
                grid-column: span 2;
            }
        }

        .hidden-final-message {
            opacity: 0;
            transform: translateY(20px);
            pointer-events: none;
            transition: all 1s ease;
        }
        
        .visible-final-message {
            opacity: 1;
            transform: translateY(0);
            pointer-events: auto;
        }

    </style>
</head>
<body>

    <div class="aurora-bg"></div>
    <canvas id="heart-canvas"></canvas>
    
    <div class="progress-container">
        <div class="progress-bar" id="progressBar"></div>
    </div>

    <div class="glass-card" id="mainCard">
        
        <div class="step active" id="step1">
            <div class="text-6xl md:text-8xl mb-2 pulsate">❤️</div>
            <h1 class="text-4xl md:text-5xl font-bold mb-2">Hey My Bundi k Laddu,</h1>
            <p class="text-gray-300 text-lg md:text-xl font-light">
                aapke special day k liye chotu sa special gift
            </p>
            <button class="btn-primary mt-4" onclick="nextStep(2)">chaloo...!</button>
        </div>

        <div class="step" id="step2">
            <div class="text-6xl md:text-8xl mb-2 animate-bounce">🎉</div>
            <h1 class="text-4xl md:text-5xl font-bold mb-2">Happy Birthday!</h1>
            <p class="text-gray-300 text-lg md:text-xl leading-relaxed">
                aapki life ka ek or nyaa saaal..! Bass is saal se meri jesi pyaari ldko mil gai aapko! Hihi!
            </p>
            <button class="btn-primary mt-4" onclick="nextStep(3)">There's more...</button>
        </div>

        <div class="step" id="step3">
            <h1 class="text-3xl md:text-4xl font-bold mb-6">aap hamesha ese hi rahee..!</h1>
            
            <div class="bento-grid">
                <div class="bento-card bento-span-2">
                    <h3 class="text-xl text-pink-300 font-serif mb-2">✨ Your Unmatched Kindness</h3>
                    <p class="text-sm text-gray-400">hamesha logo ko hsaaate raho ! Khush rakho jese hamesha hi rakhte ho</p>
                </div>
                
                <div class="bento-card">
                    <h3 class="text-xl text-pink-300 font-serif mb-2">😊 That Smile</h3>
                    <p class="text-sm text-gray-400">sigma or mature chorke pookie and hihihaha bane rahe!.</p>
                </div>

                <div class="bento-card bento-span-2">
                    <h3 class="text-xl text-pink-300 font-serif mb-2">🌟 Your Radiant Spirit</h3>
                    <p class="text-sm text-gray-400">passion, career and dreams ko kabhi nah bhoole ! Mujhe bhi nhi bhoole ! Mast raho bindaaas raho mere raho</p>
                </div>
            </div>
            
            <button class="btn-primary mt-6" onclick="nextStep(4)">One last thing...</button>
        </div>

        <div class="step" id="step4">
            <div class="text-6xl md:text-8xl mb-2">🎂</div>
            <h1 class="text-4xl md:text-5xl font-bold mb-2">My Wish For You</h1>
            <p class="text-gray-300 text-lg md:text-xl leading-relaxed mb-6">
                ye saal aane wala saal uske baad aane wala saal sb mst jaaye aapke ! Hmaari aasha h aapko aapke sapne haasil ho and mai bhi ! Hihi! Jaldi aapki job lage khwaaise poori ho fir duniya bhi to ghummi h
            </p>
            
            <button id="celebrateBtn" class="btn-primary text-xl px-10 py-4" onclick="celebrate()">Celebrate!</button>

            <div id="finalMessage" class="hidden-final-message absolute inset-0 flex flex-col items-center justify-center bg-black/80 backdrop-blur-md rounded-2xl p-6 z-10" style="display: none;">
                <h1 class="text-4xl md:text-6xl font-bold text-center leading-tight mb-4">
                    Happy Birthday,<br>my cutie!<br> <span class="text-pink-500">My SIR JII</span>
                </h1>
                <div class="text-6xl mt-4 animate-bounce">💖</div>
            </div>
        </div>

    </div>

    <script>
        // --- 1. State Management ---
        let currentStep = 1;
        const totalSteps = 4;
        let heartsSwirling = false;

        // --- 2. Three.js Background Logic ---
        const initThreeJS = () => {
            const canvas = document.getElementById('heart-canvas');
            const scene = new THREE.Scene();
            
            // Perspective Camera
            const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.z = 30;

            const renderer = new THREE.WebGLRenderer({ canvas: canvas, alpha: true, antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(window.devicePixelRatio);

            // Create Heart Shape
            const shape = new THREE.Shape();
            const x = 0, y = 0;
            shape.moveTo(x + 5, y + 5);
            shape.bezierCurveTo(x + 5, y + 5, x + 4, y, x, y);
            shape.bezierCurveTo(x - 6, y, x - 6, y + 7, x - 6, y + 7);
            shape.bezierCurveTo(x - 6, y + 11, x - 3, y + 15.4, x + 5, y + 19);
            shape.bezierCurveTo(x + 12, y + 15.4, x + 16, y + 11, x + 16, y + 7);
            shape.bezierCurveTo(x + 16, y + 7, x + 16, y, x + 10, y);
            shape.bezierCurveTo(x + 7, y, x + 5, y + 5, x + 5, y + 5);

            const geometry = new THREE.ExtrudeGeometry(shape, {
                depth: 2,
                bevelEnabled: true,
                bevelSegments: 2,
                steps: 2,
                bevelSize: 1,
                bevelThickness: 1
            });

            // Make hearts centered
            geometry.center();

            const material = new THREE.MeshPhysicalMaterial({
                color: 0xff3366,
                metalness: 0.1,
                roughness: 0.2,
                clearcoat: 1.0,
                clearcoatRoughness: 0.1,
                transparent: true,
                opacity: 0.9
            });

            // Lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
            scene.add(ambientLight);
            
            const pointLight = new THREE.PointLight(0xffffff, 1);
            pointLight.position.set(10, 10, 20);
            scene.add(pointLight);

            // Create Heart Array
            const hearts = [];
            const numHearts = 25;

            for(let i=0; i<numHearts; i++) {
                const mesh = new THREE.Mesh(geometry, material);
                // Random positions
                mesh.position.x = (Math.random() - 0.5) * 60;
                mesh.position.y = (Math.random() - 0.5) * 40;
                mesh.position.z = (Math.random() - 0.5) * 20 - 10;
                
                // Random scale
                const s = Math.random() * 0.3 + 0.1;
                mesh.scale.set(s, s, s);
                
                // Random Rotation
                mesh.rotation.x = Math.random() * Math.PI;
                mesh.rotation.y = Math.random() * Math.PI;

                // Custom properties for animation
                mesh.userData = {
                    speedY: Math.random() * 0.05 + 0.02,
                    speedRot: Math.random() * 0.02,
                    wobbleSpeed: Math.random() * 2,
                    initialX: mesh.position.x,
                    flyAwayDelay: Math.random() * 1 // delay for end animation
                };

                scene.add(mesh);
                hearts.push(mesh);
            }

            // Animation Loop
            const clock = new THREE.Clock();

            const animate = () => {
                requestAnimationFrame(animate);
                const time = clock.getElapsedTime();

                hearts.forEach(h => {
                    if (!heartsSwirling) {
                        // Standard Float Animation
                        h.position.y += h.userData.speedY;
                        h.rotation.x += h.userData.speedRot;
                        h.rotation.y += h.userData.speedRot;
                        
                        // Gentle Sine Wave on X
                        h.position.x = h.userData.initialX + Math.sin(time * 0.5 + h.userData.wobbleSpeed) * 2;

                        // Reset height if goes too high
                        if(h.position.y > 25) {
                            h.position.y = -25;
                        }
                    } else {
                        // Finale Animation: Swirl Outwards and Up
                        h.position.y += 0.3; // Fast up
                        h.position.z += 0.1; // Come closer then fade
                        h.rotation.y += 0.1; // Fast spin
                        
                        // Expand outwards
                        if (h.position.x > 0) h.position.x += 0.2;
                        else h.position.x -= 0.2;

                        // Fade out opacity logic requires modifying material which is shared
                        // For simplicity, we just fly them off screen
                    }
                });

                renderer.render(scene, camera);
            };

            animate();

            // Handle Window Resize
            window.addEventListener('resize', () => {
                camera.aspect = window.innerWidth / window.innerHeight;
                camera.updateProjectionMatrix();
                renderer.setSize(window.innerWidth, window.innerHeight);
            });
        };

        // --- 3. Step Navigation Logic (GSAP) ---
        const updateProgressBar = () => {
            const percentage = (currentStep / totalSteps) * 100;
            gsap.to('#progressBar', { width: percentage + '%', duration: 0.5 });
        };

        const nextStep = (stepNumber) => {
            const currentEl = document.querySelector('.step.active');
            const nextEl = document.getElementById('step' + stepNumber);
            const mainCard = document.getElementById('mainCard');

            // Determine if layout needs to change (Bento grid is wider)
            if (stepNumber === 3) {
                mainCard.classList.add('wide');
            } else {
                mainCard.classList.remove('wide');
            }

            // Animate Current Step Out
            gsap.to(currentEl, {
                opacity: 0,
                scale: 0.9,
                y: -20,
                duration: 0.5,
                onComplete: () => {
                    currentEl.classList.remove('active');
                    
                    // Animate Next Step In
                    nextEl.classList.add('active');
                    gsap.fromTo(nextEl, 
                        { opacity: 0, scale: 0.9, y: 20 },
                        { opacity: 1, scale: 1, y: 0, duration: 0.6, ease: "back.out(1.7)" }
                    );

                    // Stagger children of next step
                    gsap.fromTo(nextEl.children,
                        { opacity: 0, y: 10 },
                        { opacity: 1, y: 0, duration: 0.5, stagger: 0.1, delay: 0.1 }
                    );
                }
            });

            currentStep = stepNumber;
            updateProgressBar();
        };

        // --- 4. Celebration Logic ---
        const celebrate = () => {
            const btn = document.getElementById('celebrateBtn');
            const finalMsg = document.getElementById('finalMessage');
            
            // 1. Animate Button Out
            gsap.to(btn, {
                scale: 1.2,
                opacity: 0,
                duration: 0.5,
                onComplete: () => {
                    btn.style.display = 'none';
                    
                    // 2. Show Final Message
                    finalMsg.style.display = 'flex';
                    gsap.to(finalMsg, {
                        opacity: 1,
                        y: 0,
                        duration: 1,
                        ease: "power2.out"
                    });
                }
            });

            // 3. Trigger Confetti
            fireConfetti();
            
            // 4. Trigger Hearts Fly Away
            heartsSwirling = true;
        };

        const fireConfetti = () => {
            // Powerful burst from bottom corners
            const duration = 5 * 1000;
            const animationEnd = Date.now() + duration;
            const defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 100 };

            const randomInRange = (min, max) => Math.random() * (max - min) + min;

            // Initial Burst
            confetti({ ...defaults, particleCount: 100, origin: { y: 0.9, x: 0.1 } });
            confetti({ ...defaults, particleCount: 100, origin: { y: 0.9, x: 0.9 } });

            // Emoji Confetti
            const emojis = ['❤️', '🎈', '✨', '🥰', '🔥'];
            
            const interval = setInterval(function() {
                const timeLeft = animationEnd - Date.now();

                if (timeLeft <= 0) {
                    return clearInterval(interval);
                }

                const particleCount = 50 * (timeLeft / duration);

                // continuous gentle stream
                confetti(Object.assign({}, defaults, { 
                    particleCount, 
                    origin: { x: randomInRange(0.1, 0.3), y: Math.random() - 0.2 } 
                }));
                confetti(Object.assign({}, defaults, { 
                    particleCount, 
                    origin: { x: randomInRange(0.7, 0.9), y: Math.random() - 0.2 } 
                }));
                
                // Occasional Emoji blast
                if(Math.random() > 0.7) {
                     confetti({
                        particleCount: 10,
                        scalar: 3, // make emojis bigger
                        shapes: ['text'],
                        shapeOptions: {
                            text: {
                                value: emojis[Math.floor(Math.random() * emojis.length)]
                            }
                        },
                        spread: 100,
                        origin: { y: 0.6, x: 0.5 }
                    });
                }

            }, 250);
        };

        // --- Initialize ---
        window.onload = () => {
            initThreeJS();
            updateProgressBar();
            
            // Initial Animation for Step 1
            gsap.fromTo('#step1', 
                { opacity: 0, scale: 0.8 }, 
                { opacity: 1, scale: 1, duration: 1, ease: "elastic.out(1, 0.5)" }
            );
        };

    </script>
</body>
</html>
