<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>生物呼吸器官救援大冒險</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka+One&family=Noto+Sans+TC:wght@500;700;900&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <style>
        body {
            font-family: 'Noto Sans TC', 'Fredoka One', sans-serif;
            background-color: #f4ebd0;
            background-image: 
                radial-gradient(#d6c598 1.5px, transparent 0),
                radial-gradient(#d6c598 1.5px, #f4ebd0 50%);
            background-size: 20px 20px;
            color: #382d21;
            touch-action: manipulation;
            user-select: none;
            overflow-x: hidden;
        }

        .paper-card {
            background-color: #fbf6e9;
            border: 3.5px solid #2d241e;
            box-shadow: 6px 6px 0px #2d241e;
            border-radius: 20px;
        }

        .btn-hand {
            border: 3px solid #2d241e;
            box-shadow: 4px 4px 0px #2d241e;
            transition: all 0.15s ease;
        }

        .btn-hand:active {
            transform: translate(2px, 2px);
            box-shadow: 2px 2px 0px #2d241e;
        }

        .shake-animation {
            animation: shake 0.4s ease-in-out;
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            20%, 60% { transform: translateX(-8px); }
            40%, 80% { transform: translateX(8px); }
        }

        @keyframes gentleFloat {
            0% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-8px) rotate(3deg); }
            100% { transform: translateY(0px) rotate(0deg); }
        }

        .floating-deco {
            animation: gentleFloat 4s ease-in-out infinite;
        }

        .pulse-selected {
            animation: pulseGlow 1.2s infinite ease-in-out alternate;
        }

        @keyframes pulseGlow {
            0% { transform: scale(1.02); box-shadow: 0 0 0 4px #e67e22; }
            100% { transform: scale(1.06); box-shadow: 0 0 0 8px rgba(230, 126, 34, 0.4); }
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between items-center p-3 sm:p-5 relative">

    <div class="fixed top-3 left-4 text-4xl sm:text-5xl floating-deco pointer-events-none z-0">☀️</div>
    <div class="fixed top-12 right-6 text-3xl sm:text-4xl floating-deco pointer-events-none z-0" style="animation-delay: 1.5s;">🦋</div>

    <!-- Header Controls & HUD -->
    <header class="w-full max-w-4xl flex justify-between items-center paper-card px-4 sm:px-6 py-3 z-10 mb-4 relative">
        <div class="flex items-center gap-3">
            <span class="text-3xl sm:text-4xl">🫁</span>
            <div>
                <h1 class="font-black text-base sm:text-xl text-[#382d21] leading-tight">生物呼吸器官救援隊</h1>
                <p class="text-xs text-[#6b5847] font-bold">互動科學探險</p>
            </div>
        </div>

        <div class="flex items-center gap-4 sm:gap-6">
            <div class="text-center">
                <div class="text-[10px] sm:text-xs text-[#6b5847] font-bold">得分</div>
                <div id="hudScore" class="text-lg sm:text-2xl font-black text-[#d9534f]">0</div>
            </div>
            <div class="text-center">
                <div class="text-[10px] sm:text-xs text-[#6b5847] font-bold">救援成功</div>
                <div id="hudRescued" class="text-lg sm:text-2xl font-black text-[#2b8a3e]">0 / 5</div>
            </div>
            <button onclick="showQRCodeModal()" title="分享 QR Code" class="btn-hand bg-[#e9ecef] hover:bg-[#dee2e6] text-[#2d241e] p-2 rounded-xl text-lg cursor-pointer">
                📱
            </button>
            <button onclick="toggleSound()" id="btnAudio" class="btn-hand bg-[#e9ecef] hover:bg-[#dee2e6] text-[#2d241e] p-2 rounded-xl text-lg cursor-pointer">
                🔊
            </button>
        </div>
    </header>

    <main class="w-full max-w-4xl flex-1 flex flex-col items-center justify-center relative z-10 my-auto">

        <!-- START SCREEN -->
        <div id="startScreen" class="paper-card w-full p-6 sm:p-8 text-center flex flex-col items-center gap-5">
            <div class="text-6xl sm:text-7xl floating-deco">🫁</div>
            <div>
                <h2 class="text-2xl sm:text-4xl font-black text-[#2d241e] mb-2">
                    生物呼吸器官救援大冒險！
                </h2>
                <p class="text-[#5c4a3a] text-xs sm:text-base max-w-xl font-bold leading-relaxed">
                    生病的動物們需要正確的呼吸器官！第一階段：收集需求的器官氣泡；第二階段：為動物完成準確的器官醫療配對！
                </p>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 w-full max-w-2xl text-left my-2">
                <div class="bg-[#f0e3ce] p-3 rounded-xl border-2 border-[#2d241e]">
                    <div class="text-[#c0392b] font-black text-sm sm:text-base mb-1">🫁 肺臟 (Lung)</div>
                    <p class="text-xs text-[#4a3b2c] font-bold">哺乳類、鳥類、爬蟲類與成蛙等陸生脊椎動物。</p>
                </div>
                <div class="bg-[#d4edd5] p-3 rounded-xl border-2 border-[#2d241e]">
                    <div class="text-[#27ae60] font-black text-sm sm:text-base mb-1">🌿 氣管 (Trachea)</div>
                    <p class="text-xs text-[#4a3b2c] font-bold">昆蟲如蝴蝶、蝗蟲、蜜蜂等陸生節肢動物。</p>
                </div>
                <div class="bg-[#d0ebff] p-3 rounded-xl border-2 border-[#2d241e]">
                    <div class="text-[#2980b9] font-black text-sm sm:text-base mb-1">🫧 鰓 (Gill)</div>
                    <p class="text-xs text-[#4a3b2c] font-bold">魚類、螃蟹、蝦子等獲取水中溶解氧的生物。</p>
                </div>
            </div>

            <button onclick="startGame()" class="btn-hand bg-[#e67e22] hover:bg-[#d35400] text-white font-black text-lg sm:text-xl px-8 py-3 rounded-2xl cursor-pointer mt-2">
                🚀 開始救援任務
            </button>
        </div>

        <!-- STAGE 1: BUBBLE COLLECTION STAGE -->
        <div id="stage1Screen" class="w-full flex-col items-center justify-center hidden">
            <div class="paper-card w-full p-4 mb-3 flex justify-between items-center">
                <div>
                    <h3 class="text-base sm:text-lg font-black text-[#2d241e]">第一階段：蒐集裝備氣泡</h3>
                    <p class="text-xs text-[#6b5847] font-bold">觀察各種器官氣泡，點擊收集這局救援任務需要的 5 個裝備！</p>
                </div>
                <div class="flex items-center gap-2 bg-[#f0e3ce] px-3 py-1.5 rounded-xl border-2 border-[#2d241e]">
                    <span class="text-xs font-bold text-[#6b5847]">背包進度:</span>
                    <div class="text-lg font-black text-[#27ae60]"><span id="collectedCount">0</span> / 5</div>
                </div>
            </div>

            <div class="relative w-full h-[360px] sm:h-[400px] paper-card overflow-hidden bg-[#faf3e0]">
                <canvas id="bubbleCanvas" class="w-full h-full block"></canvas>

                <!-- Inventory display bar at bottom -->
                <div class="absolute bottom-3 left-3 right-3 bg-[#fbf6e9]/95 backdrop-blur p-2.5 sm:p-3 rounded-xl border-2 border-[#2d241e] flex items-center justify-between">
                    <div class="text-xs sm:text-sm text-[#2d241e] font-black mr-2 flex items-center gap-1">
                        <span>🎒</span> <span>已收集裝備:</span>
                    </div>
                    <div id="inventoryContainer" class="flex gap-2 overflow-x-auto">
                        <!-- Inventory slots dynamically populated -->
                    </div>
                </div>
            </div>
        </div>

        <!-- STAGE 2: MATCHING STAGE -->
        <div id="stage2Screen" class="w-full flex-col items-center justify-center hidden">
            <div class="paper-card w-full p-4 mb-3 text-center">
                <h3 class="text-xl sm:text-2xl font-black text-[#2d241e] mb-1">第二階段：器官醫療配對</h3>
                <p id="stage2Instruction" class="text-xs sm:text-sm font-bold text-[#e67e22]">👇 請點擊選擇一隻待救援動物，再選取對應器官配對！</p>
            </div>

            <div class="paper-card w-full p-4 sm:p-6 space-y-5" id="matchingArea">
                <!-- Animal Row -->
                <div>
                    <div class="text-xs sm:text-sm font-black text-[#8e44ad] mb-2 flex items-center gap-1">
                        <span>😿</span> <span>1. 受困動物區 (點擊選擇)</span>
                    </div>
                    <div id="animalContainer" class="grid grid-cols-5 gap-2 sm:gap-3">
                        <!-- Animals rendered here -->
                    </div>
                </div>

                <!-- Organ Inventory Row -->
                <div>
                    <div class="text-xs sm:text-sm font-black text-[#2980b9] mb-2 flex items-center gap-1">
                        <span>📦</span> <span>2. 收集到的呼吸器官 (點擊進行配對)</span>
                    </div>
                    <div id="organInventoryContainer" class="grid grid-cols-5 gap-2 sm:gap-3">
                        <!-- Collected items rendered here -->
                    </div>
                </div>
            </div>
        </div>

        <!-- Feedback Notification Toast -->
        <div id="toastNotification" class="fixed top-20 z-50 bg-[#c0392b] text-white font-black text-sm sm:text-base px-6 py-3 rounded-2xl border-3 border-[#2d241e] shadow-lg transition-all duration-300 opacity-0 pointer-events-none transform -translate-y-4 text-center max-w-md">
            <span id="toastText">訊息提示</span>
        </div>

        <!-- GAME OVER / SUCCESS MODAL -->
        <div id="resultModal" class="fixed inset-0 bg-[#2d241e]/75 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
            <div class="paper-card max-w-md w-full p-6 sm:p-8 text-center flex flex-col items-center gap-5">
                <div class="text-6xl floating-deco">🎉</div>
                <h2 class="text-2xl sm:text-3xl font-black text-[#2d241e]">大獲全勝！救援成功！</h2>
                <p class="text-[#5c4a3a] text-xs sm:text-sm font-bold leading-relaxed">
                    太棒了！你完美辨識了所有生物的呼吸器官，成功救治了所有受困的動物！
                </p>

                <div class="bg-[#f0e3ce] w-full p-4 rounded-xl border-2 border-[#2d241e]">
                    <div class="flex justify-between items-center text-base font-black text-[#2d241e]">
                        <span>救援得分：</span>
                        <span id="finalScore" class="text-2xl text-[#27ae60]">100 分</span>
                    </div>
                </div>

                <div class="flex gap-2 w-full">
                    <button onclick="showQRCodeModal()" class="btn-hand bg-[#3498db] hover:bg-[#2980b9] text-white font-black px-4 py-3 rounded-xl w-1/2 text-sm sm:text-base cursor-pointer">
                        📱 分享 QR Code
                    </button>
                    <button onclick="restartGame()" class="btn-hand bg-[#e67e22] hover:bg-[#d35400] text-white font-black px-4 py-3 rounded-xl w-1/2 text-sm sm:text-base cursor-pointer">
                        🔄 再次救援
                    </button>
                </div>
            </div>
        </div>

        <div id="qrModal" class="fixed inset-0 bg-[#2d241e]/75 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
            <div class="paper-card max-w-sm w-full p-6 text-center flex flex-col items-center gap-4 relative">
                <button onclick="closeQRCodeModal()" class="absolute top-3 right-4 text-xl font-black text-[#2d241e] hover:opacity-70">✕</button>
                <div class="text-4xl floating-deco">📱</div>
                <h3 class="text-xl sm:text-2xl font-black text-[#2d241e]">掃描 QR Code 輕鬆玩！</h3>
                <p class="text-xs text-[#6b5847] font-bold">使用手機或平板掃描下方條碼或複製連結即可開啟遊戲</p>

                <div class="p-4 bg-white rounded-xl border-2 border-[#2d241e] flex justify-center items-center my-2 shadow-inner min-h-[200px] w-[200px]">
                    <div id="qrcode" class="flex justify-center items-center"></div>
                </div>

                <div class="w-full flex items-center gap-2 bg-[#f0e3ce] p-2 rounded-xl border-2 border-[#2d241e]">
                    <p class="text-[10px] text-[#2d241e] font-bold truncate flex-1 text-left px-1" id="qrUrlText"></p>
                    <button onclick="copyGameUrl()" class="btn-hand bg-[#3498db] hover:bg-[#2980b9] text-white text-xs font-black px-2.5 py-1 rounded-lg shrink-0">
                        📋 複製
                    </button>
                </div>

                <button onclick="closeQRCodeModal()" class="btn-hand bg-[#e67e22] hover:bg-[#d35400] text-white font-black px-6 py-2 rounded-xl w-full text-sm cursor-pointer mt-1">
                    關閉
                </button>
            </div>
        </div>

    </main>

    <script>
        const ANIMAL_DATABASE = [
            { id: 'dog', name: '小狗', icon: '🐶', organ: 'lung' },
            { id: 'cat', name: '小貓', icon: '🐱', organ: 'lung' },
            { id: 'human', name: '人類', icon: '🧑', organ: 'lung' },
            { id: 'butterfly', name: '蝴蝶', icon: '🦋', organ: 'trachea' },
            { id: 'grasshopper', name: '蝗蟲', icon: '🦗', organ: 'trachea' },
            { id: 'bee', name: '蜜蜂', icon: '🐝', organ: 'trachea' },
            { id: 'shark', name: '鯊魚', icon: '🦈', organ: 'gill' },
            { id: 'goldfish', name: '金魚', icon: '🐠', organ: 'gill' },
            { id: 'crab', name: '螃蟹', icon: '🦀', organ: 'gill' }
        ];

        const ORGAN_META = {
            lung: { name: '肺臟', icon: '🫁', bgColor: '#fce4ec', textColor: '#c2185b' },
            trachea: { name: '氣管', icon: '🌿', bgColor: '#e8f5e9', textColor: '#2e7d32' },
            gill: { name: '鰓', icon: '🫧', bgColor: '#e1f5fe', textColor: '#0288d1' }
        };

        const ALL_ORGAN_TYPES = ['lung', 'trachea', 'gill'];

        let soundEnabled = true;
        let audioCtx = null;

        function getAudioContext() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
            return audioCtx;
        }

        function playPopSound() {
            if (!soundEnabled) return;
            try {
                const ctx = getAudioContext();
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(350, ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(750, ctx.currentTime + 0.08);
                gain.gain.setValueAtTime(0.25, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.08);
                osc.connect(gain);
                gain.connect(ctx.destination);
                osc.start();
                osc.stop(ctx.currentTime + 0.08);
            } catch (e) {}
        }

        function playSuccessSound() {
            if (!soundEnabled) return;
            try {
                const ctx = getAudioContext();
                const notes = [523.25, 659.25, 783.99, 1046.50];
                notes.forEach((freq, i) => {
                    const osc = ctx.createOscillator();
                    const gain = ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.value = freq;
                    gain.gain.setValueAtTime(0.15, ctx.currentTime + i * 0.07);
                    gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + i * 0.07 + 0.2);
                    osc.connect(gain);
                    gain.connect(ctx.destination);
                    osc.start(ctx.currentTime + i * 0.07);
                    osc.stop(ctx.currentTime + i * 0.07 + 0.2);
                });
            } catch (e) {}
        }

        function playErrorSound() {
            if (!soundEnabled) return;
            try {
                const ctx = getAudioContext();
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(220, ctx.currentTime);
                osc.frequency.setValueAtTime(160, ctx.currentTime + 0.1);
                gain.gain.setValueAtTime(0.2, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.25);
                osc.connect(gain);
                gain.connect(ctx.destination);
                osc.start();
                osc.stop(ctx.currentTime + 0.25);
            } catch (e) {}
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            document.getElementById('btnAudio').innerText = soundEnabled ? '🔊' : '🔇';
        }

        let currentAnimals = [];
        let targetOrganCounts = { lung: 0, trachea: 0, gill: 0 };
        let collectedCounts = { lung: 0, trachea: 0, gill: 0 };
        let collectedInventory = [];
        let score = 0;
        let rescuedCount = 0;
        let selectedAnimalIndex = null;
        let selectedOrganIndex = null;

        const canvas = document.getElementById('bubbleCanvas');
        const ctx = canvas.getContext('2d');
        let bubbles = [];
        let animationFrame = null;

        function resizeCanvas() {
            if (canvas && canvas.parentElement) {
                const rect = canvas.parentElement.getBoundingClientRect();
                const dpr = window.devicePixelRatio || 1;
                canvas.width = rect.width * dpr;
                canvas.height = rect.height * dpr;
                ctx.scale(dpr, dpr);
            }
        }

        window.addEventListener('resize', resizeCanvas);

        function showToast(msg, isError = true) {
            const toast = document.getElementById('toastNotification');
            const toastText = document.getElementById('toastText');
            toastText.innerText = msg;
            toast.style.backgroundColor = isError ? '#c0392b' : '#27ae60';
            toast.classList.remove('opacity-0', 'pointer-events-none', '-translate-y-4');
            toast.classList.add('opacity-100', 'translate-y-2');

            setTimeout(() => {
                toast.classList.remove('opacity-100', 'translate-y-2');
                toast.classList.add('opacity-0', 'pointer-events-none', '-translate-y-4');
            }, 2600);
        }

        function startGame() {
            getAudioContext();
            score = 0;
            rescuedCount = 0;
            collectedInventory = [];
            selectedAnimalIndex = null;
            selectedOrganIndex = null;

            targetOrganCounts = { lung: 0, trachea: 0, gill: 0 };
            collectedCounts = { lung: 0, trachea: 0, gill: 0 };

            document.getElementById('hudScore').innerText = score;
            document.getElementById('hudRescued').innerText = '0 / 5';

            // Pick 5 random animals
            const shuffled = [...ANIMAL_DATABASE].sort(() => 0.5 - Math.random());
            currentAnimals = shuffled.slice(0, 5).map(a => ({ ...a, rescued: false }));

            // Calculate exact target counts needed for these 5 animals
            currentAnimals.forEach(a => {
                targetOrganCounts[a.organ] = (targetOrganCounts[a.organ] || 0) + 1;
            });

            document.getElementById('startScreen').classList.add('hidden');
            document.getElementById('stage2Screen').classList.add('hidden');
            document.getElementById('resultModal').classList.add('hidden');
            document.getElementById('stage1Screen').classList.remove('hidden');
            document.getElementById('stage1Screen').classList.add('flex');

            setTimeout(() => {
                resizeCanvas();
                initStage1();
            }, 50);
        }

        function initStage1() {
            updateInventoryUI();
            bubbles = [];

            // Spawn 4 fixed bubbles covering all organ types continuously
            const rect = canvas.parentElement.getBoundingClientRect();
            const radius = 38;

            // Types to spawn: Guaranteed covering lung, trachea, gill + 1 extra
            const initialTypes = ['lung', 'trachea', 'gill', ALL_ORGAN_TYPES[Math.floor(Math.random() * 3)]];

            initialTypes.forEach((type, idx) => {
                bubbles.push({
                    x: (rect.width / 4) * idx + rect.width / 8,
                    y: rect.height + radius + (idx * 50),
                    radius: radius,
                    type: type,
                    vx: (Math.random() - 0.5) * 1.5,
                    vy: -(Math.random() * 0.8 + 1.2)
                });
            });

            if (animationFrame) cancelAnimationFrame(animationFrame);
            animateStage1();
        }

        function animateStage1() {
            const rect = canvas.parentElement.getBoundingClientRect();
            ctx.clearRect(0, 0, rect.width, rect.height);

            for (let i = 0; i < bubbles.length; i++) {
                const b = bubbles[i];
                b.x += b.vx;
                b.y += b.vy;

                // Bounce walls
                if (b.x - b.radius < 0) {
                    b.x = b.radius;
                    b.vx *= -1;
                } else if (b.x + b.radius > rect.width) {
                    b.x = rect.width - b.radius;
                    b.vx *= -1;
                }

                // Smooth bottom recycling (loop around)
                if (b.y + b.radius < 0) {
                    b.y = rect.height + b.radius;
                    b.x = Math.random() * (rect.width - b.radius * 2) + b.radius;
                    // Rotate organ type to maintain variety
                    const missingTypes = ALL_ORGAN_TYPES.filter(t => collectedCounts[t] < targetOrganCounts[t]);
                    if (missingTypes.length > 0 && Math.random() < 0.6) {
                        b.type = missingTypes[Math.floor(Math.random() * missingTypes.length)];
                    } else {
                        b.type = ALL_ORGAN_TYPES[Math.floor(Math.random() * ALL_ORGAN_TYPES.length)];
                    }
                }

                // Render Bubble
                const meta = ORGAN_META[b.type];
                const isNeededAndMissing = collectedCounts[b.type] < targetOrganCounts[b.type];

                ctx.save();
                ctx.beginPath();
                ctx.arc(b.x, b.y, b.radius, 0, Math.PI * 2);
                ctx.fillStyle = meta.bgColor;
                ctx.fill();
                ctx.lineWidth = 3.5;
                ctx.strokeStyle = '#2d241e';
                ctx.stroke();

                // Gloss Highlight
                ctx.beginPath();
                ctx.arc(b.x - b.radius * 0.3, b.y - b.radius * 0.3, b.radius * 0.25, 0, Math.PI * 2);
                ctx.fillStyle = 'rgba(255, 255, 255, 0.8)';
                ctx.fill();

                // Icon & Label
                ctx.font = '24px sans-serif';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(meta.icon, b.x, b.y - 6);

                ctx.font = '900 12px "Noto Sans TC", sans-serif';
                ctx.fillStyle = meta.textColor;
                ctx.fillText(meta.name, b.x, b.y + 18);
                ctx.restore();
            }

            if (collectedInventory.length < 5) {
                animationFrame = requestAnimationFrame(animateStage1);
            } else {
                setTimeout(transitionToStage2, 400);
            }
        }

        function handleBubbleClick(clickX, clickY) {
            if (collectedInventory.length >= 5) return;

            for (let i = bubbles.length - 1; i >= 0; i--) {
                const b = bubbles[i];
                const dist = Math.hypot(b.x - clickX, b.y - clickY);

                if (dist < b.radius + 10) {
                    const type = b.type;
                    const meta = ORGAN_META[type];

                    // Check if this organ is still needed in current task
                    if (collectedCounts[type] < targetOrganCounts[type]) {
                        playPopSound();
                        collectedCounts[type]++;
                        collectedInventory.push({ type: type, used: false });

                        // Recycle this bubble to a new type
                        b.y = canvas.parentElement.getBoundingClientRect().height + b.radius;
                        b.x = Math.random() * (canvas.parentElement.getBoundingClientRect().width - b.radius * 2) + b.radius;
                        b.type = ALL_ORGAN_TYPES[Math.floor(Math.random() * ALL_ORGAN_TYPES.length)];

                        updateInventoryUI();
                    } else {
                        // Already full or not needed
                        playErrorSound();
                        if (targetOrganCounts[type] === 0) {
                            showToast(`💡 這局救援不需要【${meta.name}】喔！請選擇其他器官！`, true);
                        } else {
                            showToast(`💡 【${meta.name}】已經收集足夠囉 (${collectedCounts[type]}/${targetOrganCounts[type]})！請收集其他器官！`, true);
                        }
                    }
                    break;
                }
            }
        }

        canvas.addEventListener('pointerdown', (e) => {
            const rect = canvas.getBoundingClientRect();
            handleBubbleClick(e.clientX - rect.left, e.clientY - rect.top);
        });

        function updateInventoryUI() {
            document.getElementById('collectedCount').innerText = collectedInventory.length;
            const container = document.getElementById('inventoryContainer');
            container.innerHTML = '';

            for (let i = 0; i < 5; i++) {
                const slot = document.createElement('div');
                slot.className = 'w-10 h-10 sm:w-12 sm:h-12 rounded-xl border-2 border-[#2d241e] flex items-center justify-center text-lg sm:text-xl font-black transition-all';

                if (i < collectedInventory.length) {
                    const item = collectedInventory[i];
                    const meta = ORGAN_META[item.type];
                    slot.style.backgroundColor = meta.bgColor;
                    slot.style.color = meta.textColor;
                    slot.innerHTML = meta.icon;
                    slot.title = meta.name;
                } else {
                    slot.className += ' border-dashed bg-[#e9ecef] opacity-50';
                    slot.innerHTML = `<span class="text-xs text-[#2d241e] font-bold">${i + 1}</span>`;
                }
                container.appendChild(slot);
            }
        }

        function transitionToStage2() {
            if (animationFrame) cancelAnimationFrame(animationFrame);

            document.getElementById('stage1Screen').classList.add('hidden');
            document.getElementById('stage1Screen').classList.remove('flex');
            document.getElementById('stage2Screen').classList.remove('hidden');
            document.getElementById('stage2Screen').classList.add('flex');

            renderStage2();
        }

        function renderStage2() {
            const animalContainer = document.getElementById('animalContainer');
            animalContainer.innerHTML = '';

            currentAnimals.forEach((animal, index) => {
                const card = document.createElement('div');
                const isSelected = selectedAnimalIndex === index;
                const isRescued = animal.rescued;

                card.className = `animal-card paper-card p-2 sm:p-3 flex flex-col items-center justify-center cursor-pointer transition-all ${
                    isSelected ? 'pulse-selected bg-[#ffe8cc]' : ''
                } ${isRescued ? 'opacity-40 grayscale pointer-events-none' : 'hover:scale-105'}`;

                card.innerHTML = `
                    <div class="text-3xl sm:text-4xl mb-1">${isRescued ? '✨' : animal.icon}</div>
                    <div class="font-black text-xs sm:text-sm text-[#2d241e]">${animal.name}</div>
                    <div class="text-[9px] sm:text-[11px] font-bold mt-1 px-2 py-0.5 rounded-full ${isRescued ? 'bg-emerald-200 text-emerald-900' : 'bg-amber-200 text-amber-900'}">
                        ${isRescued ? '救援成功' : '待救援'}
                    </div>
                `;

                if (!isRescued) {
                    card.onclick = () => selectAnimal(index);
                }
                animalContainer.appendChild(card);
            });

            const organContainer = document.getElementById('organInventoryContainer');
            organContainer.innerHTML = '';

            collectedInventory.forEach((item, index) => {
                const card = document.createElement('div');
                const meta = ORGAN_META[item.type];
                const isUsed = item.used;
                const isSelected = selectedOrganIndex === index;

                card.className = `organ-card paper-card p-2 sm:p-3 flex flex-col items-center justify-center cursor-pointer transition-all ${
                    isSelected ? 'pulse-selected' : ''
                } ${isUsed ? 'opacity-30 grayscale pointer-events-none' : 'hover:scale-105'}`;
                card.style.backgroundColor = isUsed ? '#e9ecef' : meta.bgColor;

                card.innerHTML = `
                    <div class="text-3xl sm:text-4xl mb-1">${meta.icon}</div>
                    <div class="font-black text-xs sm:text-sm" style="color: ${meta.textColor}">${meta.name}</div>
                `;

                if (!isUsed) {
                    card.onclick = () => selectOrgan(index);
                }
                organContainer.appendChild(card);
            });

            const instr = document.getElementById('stage2Instruction');
            if (selectedAnimalIndex !== null && selectedOrganIndex === null) {
                instr.innerText = `👉 已選擇：${currentAnimals[selectedAnimalIndex].name}！請點擊下方的呼吸器官進行配對！`;
            } else if (selectedAnimalIndex === null && selectedOrganIndex !== null) {
                instr.innerText = `👉 已選擇器官裝備！請點擊上方的動物進行配對！`;
            } else {
                instr.innerText = `👇 請點擊選擇動物或器官進行醫療配對！`;
            }
        }

        function selectAnimal(index) {
            getAudioContext();
            selectedAnimalIndex = (selectedAnimalIndex === index) ? null : index;
            renderStage2();
            checkAndExecuteMatch();
        }

        function selectOrgan(index) {
            getAudioContext();
            selectedOrganIndex = (selectedOrganIndex === index) ? null : index;
            renderStage2();
            checkAndExecuteMatch();
        }

        function checkAndExecuteMatch() {
            if (selectedAnimalIndex !== null && selectedOrganIndex !== null) {
                const targetAnimal = currentAnimals[selectedAnimalIndex];
                const selectedOrgan = collectedInventory[selectedOrganIndex];

                if (targetAnimal.organ === selectedOrgan.type) {
                    playSuccessSound();
                    targetAnimal.rescued = true;
                    selectedOrgan.used = true;
                    score += 20;
                    rescuedCount++;

                    showToast(`✅ 正確！${targetAnimal.name} 的呼吸器官正是【${ORGAN_META[selectedOrgan.type].name}】！`, false);

                    document.getElementById('hudScore').innerText = score;
                    document.getElementById('hudRescued').innerText = `${rescuedCount} / 5`;

                    selectedAnimalIndex = null;
                    selectedOrganIndex = null;
                    renderStage2();

                    if (rescuedCount === 5) {
                        setTimeout(showSuccessModal, 600);
                    }
                } else {
                    playErrorSound();
                    showToast(`❌ 配對錯誤！${targetAnimal.name} 的器官不是【${ORGAN_META[selectedOrgan.type].name}】哦！`, true);
                    
                    const area = document.getElementById('matchingArea');
                    area.classList.add('shake-animation');
                    setTimeout(() => area.classList.remove('shake-animation'), 400);

                    selectedAnimalIndex = null;
                    selectedOrganIndex = null;
                    renderStage2();
                }
            }
        }

        function showSuccessModal() {
            document.getElementById('finalScore').innerText = score + ' 分';
            document.getElementById('resultModal').classList.remove('hidden');
        }

        function restartGame() {
            document.getElementById('resultModal').classList.add('hidden');
            startGame();
        }

        let qrcodeInstance = null;

        function showQRCodeModal() {
            getAudioContext();
            const qrModal = document.getElementById('qrModal');
            const qrContainer = document.getElementById('qrcode');
            const qrUrlText = document.getElementById('qrUrlText');
            
            let currentUrl = window.location.href;
            
            // 如果網址過長（超過 800 字元），進行安全性處理避免 QRCode.js 產生溢位空白
            if (currentUrl.length > 800) {
                // 如果含有 data:text/html 或長 query，擷取有效前段或提示
                if (currentUrl.startsWith('data:')) {
                    currentUrl = window.location.origin || 'https://localhost';
                }
            }

            qrUrlText.innerText = currentUrl;

            // 清空舊的 QR Code
            qrContainer.innerHTML = '';

            try {
                // 使用 QRCode 產生器，調降容錯率提升長字串相容度
                qrcodeInstance = new QRCode(qrContainer, {
                    text: currentUrl,
                    width: 170,
                    height: 170,
                    colorDark : "#2d241e",
                    colorLight : "#ffffff",
                    correctLevel : QRCode.CorrectLevel.L
                });
            } catch (err) {
                console.error("QRCode generation error:", err);
                qrContainer.innerHTML = '<span class="text-xs text-red-600 font-bold p-2">連結過長無法生成條碼<br>請點選上方【複製】按鈕分享連結</span>';
            }

            qrModal.classList.remove('hidden');
        }

        function copyGameUrl() {
            const currentUrl = window.location.href;
            if (navigator.clipboard && navigator.clipboard.writeText) {
                navigator.clipboard.writeText(currentUrl).then(() => {
                    showToast("📋 遊戲網址已複製到剪貼簿！", false);
                }).catch(() => {
                    fallbackCopy(currentUrl);
                });
            } else {
                fallbackCopy(currentUrl);
            }
        }

        function fallbackCopy(text) {
            const textarea = document.createElement('textarea');
            textarea.value = text;
            textarea.style.position = 'fixed';
            textarea.style.opacity = '0';
            document.body.appendChild(textarea);
            textarea.select();
            try {
                document.execCommand('copy');
                showToast("📋 遊戲網址已複製到剪貼簿！", false);
            } catch (err) {
                showToast("❌ 複製失敗，請手動複製連結", true);
            }
            document.body.removeChild(textarea);
        }

        function closeQRCodeModal() {
            document.getElementById('qrModal').classList.add('hidden');
        }
    </script>
</body>
</html>
