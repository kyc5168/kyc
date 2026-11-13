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
            border: 3.5px solid #2d241e;
            box-shadow: 4px 4px 0px #2d241e;
            transition: all 0.15s ease;
        }

        .btn-hand:hover {
            transform: translateY(-2px);
            box-shadow: 6px 6px 0px #2d241e;
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

        .habitat-card {
            border: 3.5px solid #2d241e;
            box-shadow: 5px 5px 0px #2d241e;
            border-radius: 18px;
            transition: all 0.2s ease;
            cursor: pointer;
        }

        .habitat-card:hover {
            transform: translateY(-3px);
            box-shadow: 7px 7px 0px #2d241e;
        }

        .modal-bg {
            background-color: rgba(45, 36, 30, 0.75);
            backdrop-filter: blur(4px);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col items-center p-3 md:p-6">

    <!-- 頂部標題與工具欄 -->
    <header class="w-full max-w-4xl flex justify-between items-center mb-4">
        <div class="flex items-center space-x-3">
            <span class="text-3xl md:text-4xl">🏥</span>
            <div>
                <h1 class="text-xl md:text-2xl font-black tracking-wide text-amber-900">生物呼吸器官救援隊</h1>
                <p class="text-xs md:text-sm text-amber-800 font-bold">小學自然科學互動學習館</p>
            </div>
        </div>
        
        <div class="flex items-center space-x-2">
            <button onclick="openQRModal()" class="btn-hand bg-amber-200 hover:bg-amber-300 px-3 py-2 rounded-xl text-sm font-bold flex items-center gap-1">
                📱 <span>QR Code</span>
            </button>
            <button onclick="restartGame()" class="btn-hand bg-orange-400 hover:bg-orange-500 text-white px-3 py-2 rounded-xl text-sm font-bold">
                🔄 重置
            </button>
        </div>
    </header>

    <!-- 主遊戲容器 -->
    <main class="w-full max-w-4xl paper-card p-4 md:p-6 relative overflow-hidden min-h-[560px] flex flex-col justify-between">
        
        <!-- 訊息提示 Banner -->
        <div id="toast" class="hidden absolute top-4 left-1/2 transform -translate-x-1/2 z-50 bg-amber-900 text-amber-100 font-bold px-6 py-3 rounded-full border-2 border-amber-950 shadow-lg text-center text-sm md:text-base">
            提示訊息
        </div>

        <!-- 第一階段：蒐集醫療裝備 -->
        <div id="stage-1" class="space-y-4">
            <div class="flex justify-between items-center bg-amber-100 p-3 rounded-xl border-2 border-amber-800">
                <div>
                    <h2 class="font-black text-lg text-amber-900">第一階段：蒐集呼吸器裝備</h2>
                    <p class="text-xs md:text-sm text-amber-800 font-bold">點擊空中飄浮的氣泡，蒐集救援動物所需的器官（缺幾個補幾個）！</p>
                </div>
                <div class="text-right">
                    <span class="text-xs font-bold text-amber-800">收集進度</span>
                    <div id="stage1-progress-text" class="text-xl font-black text-orange-600">0 / 5</div>
                </div>
            </div>

            <!-- 背包面板 -->
            <div id="inventory-panel" class="grid grid-cols-3 gap-2 bg-amber-50 p-3 rounded-xl border-2 border-amber-700 text-center">
                <div class="bg-white p-2 rounded-lg border border-amber-300">
                    <span class="text-xl">🫁</span> <span class="font-bold text-xs md:text-sm">肺臟:</span>
                    <span id="count-lung" class="font-black text-orange-600">0</span>
                </div>
                <div class="bg-white p-2 rounded-lg border border-amber-300">
                    <span class="text-xl">🌿</span> <span class="font-bold text-xs md:text-sm">氣管:</span>
                    <span id="count-trachea" class="font-black text-orange-600">0</span>
                </div>
                <div class="bg-white p-2 rounded-lg border border-amber-300">
                    <span class="text-xl">🫧</span> <span class="font-bold text-xs md:text-sm">鰓:</span>
                    <span id="count-gill" class="font-black text-orange-600">0</span>
                </div>
            </div>

            <!-- 氣泡 Canvas 遊戲區 -->
            <div class="relative w-full h-[340px] bg-sky-100 rounded-xl border-2 border-sky-300 overflow-hidden shadow-inner">
                <canvas id="bubbleCanvas" class="w-full h-full cursor-pointer"></canvas>
            </div>
        </div>

        <!-- 第二階段：器官醫療配對 (保留原有完整玩法) -->
        <div id="stage-2" class="hidden space-y-4">
            <div class="bg-amber-100 p-3 rounded-xl border-2 border-amber-800 flex justify-between items-center">
                <div>
                    <h2 class="font-black text-lg text-amber-900">第二階段：進行器官醫療配對</h2>
                    <p class="text-xs md:text-sm text-amber-800 font-bold">點選【生病的動物】與對應的【器官裝備】進行治療！</p>
                </div>
                <div class="text-right">
                    <span class="text-xs font-bold text-amber-800">剩餘未治療</span>
                    <div id="stage2-progress-text" class="text-xl font-black text-orange-600">5 / 5</div>
                </div>
            </div>

            <!-- 選取的器官狀態列 -->
            <div class="grid grid-cols-2 gap-3">
                <!-- 左側：待治療動物選單 -->
                <div class="bg-amber-50 p-3 rounded-xl border-2 border-amber-700">
                    <h3 class="font-black text-amber-900 text-sm mb-2 border-b border-amber-300 pb-1">🐾 待救援動物 (點擊選取)</h3>
                    <div id="animals-container" class="space-y-2 max-h-[300px] overflow-y-auto pr-1"></div>
                </div>

                <!-- 右側：可用器官背包 -->
                <div class="bg-amber-50 p-3 rounded-xl border-2 border-amber-700">
                    <h3 class="font-black text-amber-900 text-sm mb-2 border-b border-amber-300 pb-1">🧰 裝備庫存 (點擊配對)</h3>
                    <div id="organs-container" class="space-y-2 max-h-[300px] overflow-y-auto pr-1"></div>
                </div>
            </div>
        </div>

        <!-- 第三階段：送動物回家 -->
        <div id="stage-3" class="hidden space-y-4">
            <div class="bg-amber-100 p-3 rounded-xl border-2 border-amber-800">
                <h2 class="font-black text-lg text-amber-900">第三階段：送康復動物回家</h2>
                <p class="text-xs md:text-sm text-amber-800 font-bold">先選擇下方配對好的動物，點擊正確的棲息地卡片送牠們回家！</p>
            </div>

            <!-- 配對完成待送回家的動物 -->
            <div id="paired-animals-container" class="flex flex-wrap gap-2 justify-center p-3 bg-amber-50 rounded-xl border-2 border-amber-700 min-h-[90px] items-center">
                <!-- 動態注入已康復動物 -->
            </div>

            <!-- 棲息地區域 -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div id="habitat-ocean" onclick="placeAnimalInHabitat('水生')" class="habitat-card bg-blue-50 border-blue-400 p-4 text-center">
                    <h3 class="text-xl font-black text-blue-900 mb-2">🌊 藍色大海 (水生環境)</h3>
                    <p class="text-xs text-blue-700 mb-3 font-bold">適合用「鰓」或水中輔助呼吸的生物</p>
                    <div id="ocean-residents" class="flex flex-wrap gap-2 justify-center min-h-[100px] p-2 bg-white/70 rounded-lg border border-blue-200"></div>
                </div>

                <div id="habitat-forest" onclick="placeAnimalInHabitat('陸生')" class="habitat-card bg-emerald-50 border-emerald-400 p-4 text-center">
                    <h3 class="text-xl font-black text-emerald-900 mb-2">🌲 森林草地 (陸生環境)</h3>
                    <p class="text-xs text-emerald-700 mb-3 font-bold">適合用「肺」或「氣管」呼吸的生物</p>
                    <div id="forest-residents" class="flex flex-wrap gap-2 justify-center min-h-[100px] p-2 bg-white/70 rounded-lg border border-emerald-200"></div>
                </div>
            </div>
        </div>

        <!-- 第四階段：二氧化碳呼出量與濃度由多到少排序挑戰 -->
        <div id="stage-4" class="hidden space-y-4">
            <div class="bg-amber-100 p-3 rounded-xl border-2 border-amber-800 flex justify-between items-center">
                <div>
                    <h2 class="font-black text-lg text-amber-900">第四階段：二氧化碳排放量與濃度大挑戰</h2>
                    <p class="text-xs md:text-sm text-amber-800 font-bold">請依「呼出的二氧化碳濃度與氣體總量」由【最多 ➔ 最少】依序點擊動物進行排列！</p>
                </div>
                <button onclick="resetStage4Sort()" class="btn-hand bg-amber-300 hover:bg-amber-400 text-amber-950 px-3 py-1.5 rounded-xl text-xs font-black shrink-0">
                    🔄 重新排列
                </button>
            </div>

            <!-- 排序作答區 (1 到 5 名) -->
            <div class="bg-amber-50 p-3 rounded-xl border-2 border-amber-700">
                <div class="flex justify-between items-center text-xs font-black text-amber-900 mb-2 px-1">
                    <span>🔥 最多 / 濃度最高 (第 1 名)</span>
                    <span>🍃 最少 / 濃度最低 (第 5 名)</span>
                </div>
                <div id="co2-slots-container" class="grid grid-cols-5 gap-2">
                    <!-- 動態生成 5 個排序位置 -->
                </div>
            </div>

            <!-- 待排序動物選項池 -->
            <div class="bg-white p-4 rounded-xl border-2 border-amber-300">
                <p class="text-xs font-bold text-amber-800 mb-3 text-center">👇 點擊下方動物放入順序框（可點擊上方格子取消選取）：</p>
                <div id="co2-pool-container" class="flex flex-wrap gap-3 justify-center min-h-[76px] items-center">
                    <!-- 動態生成待排序動物卡片 -->
                </div>
            </div>

            <!-- 提交答案按鈕 -->
            <div class="flex justify-center pt-1">
                <button id="submit-co2-btn" onclick="checkStage4Answer()" class="btn-hand bg-orange-500 hover:bg-orange-600 text-white px-8 py-3 rounded-xl font-black text-base disabled:opacity-50" disabled>
                    ✅ 確認排序並提交
                </button>
            </div>
        </div>

    </main>

    <!-- 勝利通關彈窗 Modal -->
    <div id="victory-modal" class="hidden fixed inset-0 z-50 modal-bg flex items-center justify-center p-4">
        <div class="paper-card bg-amber-50 p-6 max-w-md w-full text-center space-y-4">
            <span class="text-6xl floating-deco inline-block">🏆</span>
            <h2 class="text-2xl font-black text-amber-900">太棒了！全部挑戰大成功！</h2>
            <p class="text-sm font-bold text-amber-800">你不僅救回了所有動物，還精準掌握了呼吸器官與二氧化碳排放量的科學知識！</p>
            
            <div class="bg-white p-3 rounded-xl border border-amber-300 text-left text-xs space-y-1 font-bold text-amber-900">
                <p>✅ 知識小複習：</p>
                <p>• <b>呼吸器官</b>：魚蝦用<b>鰓</b>、昆蟲用<b>氣管</b>、鳥類哺乳類用<b>肺臟</b>。</p>
                <p>• <b>二氧化碳呼出量與濃度</b>：恆溫動物且體型較大者（如<b>小白兔、小麻雀</b>）代謝率高，呼出的 CO₂ 濃度與總量遠高於變溫水生動物與小型昆蟲！</p>
            </div>

            <div class="flex flex-col gap-2 pt-2">
                <button onclick="openQRModal()" class="btn-hand bg-amber-300 hover:bg-amber-400 text-amber-950 py-2.5 rounded-xl font-black text-sm">
                    📱 分享 QR Code 給同學
                </button>
                <button onclick="restartGame()" class="btn-hand bg-orange-500 hover:bg-orange-600 text-white py-2.5 rounded-xl font-black text-sm">
                    🎮 再玩一次
                </button>
            </div>
        </div>
    </div>

    <!-- QR Code 彈窗 Modal -->
    <div id="qr-modal" class="hidden fixed inset-0 z-50 modal-bg flex items-center justify-center p-4">
        <div class="paper-card bg-amber-50 p-6 max-w-sm w-full text-center space-y-4 relative">
            <button onclick="closeQRModal()" class="absolute top-3 right-3 text-gray-500 hover:text-gray-800 font-bold text-xl">✕</button>
            <h3 class="text-lg font-black text-amber-900">📱 掃描 QR Code 開啟遊戲</h3>
            
            <div class="flex justify-center p-3 bg-white rounded-xl border border-amber-300">
                <div id="qrcode-container" class="flex justify-center items-center min-h-[160px]"></div>
            </div>

            <!-- 新增沙盒環境警告提示 -->
            <div id="qr-sandbox-warning" class="hidden bg-red-100 text-red-700 text-xs font-bold p-2 rounded-lg border border-red-300">
                ⚠️ 注意：目前處於預覽環境，暫時網址無法跨裝置開啟（手機會顯示空白）。請將此程式碼發布/上傳至網頁空間後再進行掃描。
            </div>

            <p class="text-xs text-amber-800 font-bold">使用手機鏡頭掃描即可遊玩！</p>
            
            <div class="flex gap-2">
                <button onclick="copyCurrentURL()" class="btn-hand w-full bg-amber-200 hover:bg-amber-300 text-amber-900 py-2 rounded-xl text-xs font-bold">
                    📋 複製連結
                </button>
                <button onclick="closeQRModal()" class="btn-hand w-full bg-amber-800 text-white py-2 rounded-xl text-xs font-bold">
                    關閉
                </button>
            </div>
        </div>
    </div>

    <script>
        // --- 完整動物資料庫 (加入 co2Rank 數值：數值越大代表呼出二氧化碳濃度與總量越多) ---
        const ANIMAL_DATABASE = [
            { id: 'rabbit', name: '小白兔', icon: '🐰', organ: 'lung', habitat: '陸生', co2Rank: 6, desc: '哺乳類恆溫動物，體型較大且肺活量高，代謝旺盛，呼出的二氧化碳量與濃度最高。' },
            { id: 'bird', name: '小麻雀', icon: '🐦', organ: 'lung', habitat: '陸生', co2Rank: 5, desc: '鳥類恆溫動物，飛行與心率極快，呼吸氣體交換率高，呼出二氧化碳濃度次高。' },
            { id: 'shrimp', name: '大龍蝦', icon: '🦞', organ: 'gill', habitat: '水生', co2Rank: 4, desc: '較大型水生甲殼類，體積比小魚大，總呼吸代謝排放的二氧化碳量高於小型魚蟲。' },
            { id: 'fish', name: '小金魚', icon: '🐠', organ: 'gill', habitat: '水生', co2Rank: 3, desc: '變溫水生動物，代謝率較陸生恆溫動物低，水中溶氧與排出二氧化碳濃度較低。' },
            { id: 'grasshopper', name: '小蚱蜢', icon: '🦗', organ: 'trachea', habitat: '陸生', co2Rank: 2, desc: '小型昆蟲，利用氣管系統擴散氣體，體積小、總呼出二氧化碳量較少。' },
            { id: 'bee', name: '小蜜蜂', icon: '🐝', organ: 'trachea', habitat: '陸生', co2Rank: 1, desc: '體型最小的昆蟲，單次呼吸排出的二氧化碳氣體總量最少。' }
        ];

        const ORGAN_NAMES = {
            lung: '肺臟 🫁',
            trachea: '氣管 🌿',
            gill: '鰓 🫧'
        };

        // 遊戲核心狀態 variables
        let currentStage = 1;
        let selectedAnimals = [];
        let collectedInventory = { lung: 0, trachea: 0, gill: 0 };
        let requiredTotalOrgans = { lung: 0, trachea: 0, gill: 0 };
        
        // Stage 2 state (保持原玩法)
        let stage2Animals = [];
        let selectedAnimalId = null;
        let selectedOrganType = null;

        // Stage 3 state
        let curedAnimals = [];
        let selectedCuredAnimalId = null;

        // Stage 4 state (二氧化碳排序)
        let stage4SortedList = [];
        let stage4PoolList = [];

        // Stage 1 Canvas Bubbles
        let canvas, ctx;
        let bubbles = [];
        let animationFrameId = null;

        // Web Audio 音效產生器
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        function playSound(type) {
            try {
                if (audioCtx.state === 'suspended') audioCtx.resume();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                if (type === 'pop') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(400, audioCtx.currentTime);
                    osc.frequency.exponentialRampToValueAtTime(800, audioCtx.currentTime + 0.1);
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.1);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.1);
                } else if (type === 'success') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime);
                    osc.frequency.setValueAtTime(659.25, audioCtx.currentTime + 0.1);
                    gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.3);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.3);
                } else if (type === 'error') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(200, audioCtx.currentTime);
                    osc.frequency.setValueAtTime(150, audioCtx.currentTime + 0.15);
                    gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.25);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.25);
                }
            } catch (e) {
                console.log('Audio Context muted or blocked');
            }
        }

        window.onload = function() {
            initCanvas();
            restartGame();
        };

        function restartGame() {
            currentStage = 1;
            selectedAnimals = shuffleArray([...ANIMAL_DATABASE]).slice(0, 5); // 隨機選 5 隻
            
            // 計算這 5 隻動物需要的器官總量
            requiredTotalOrgans = { lung: 0, trachea: 0, gill: 0 };
            selectedAnimals.forEach(a => requiredTotalOrgans[a.organ]++);

            collectedInventory = { lung: 0, trachea: 0, gill: 0 };
            
            stage2Animals = selectedAnimals.map(a => ({ ...a, isCured: false }));
            curedAnimals = [];
            selectedAnimalId = null;
            selectedOrganType = null;
            selectedCuredAnimalId = null;

            document.getElementById('victory-modal').classList.add('hidden');
            document.getElementById('ocean-residents').innerHTML = '';
            document.getElementById('forest-residents').innerHTML = '';

            updateStageVisibility();
            updateInventoryUI();
            initStage1Bubbles();
        }

        function updateStageVisibility() {
            document.getElementById('stage-1').classList.toggle('hidden', currentStage !== 1);
            document.getElementById('stage-2').classList.toggle('hidden', currentStage !== 2);
            document.getElementById('stage-3').classList.toggle('hidden', currentStage !== 3);
            document.getElementById('stage-4').classList.toggle('hidden', currentStage !== 4);
        }

        function showToast(msg) {
            const toast = document.getElementById('toast');
            toast.innerText = msg;
            toast.classList.remove('hidden');
            clearTimeout(window.toastTimer);
            window.toastTimer = setTimeout(() => {
                toast.classList.add('hidden');
            }, 2500);
        }

        function resizeCanvas() {
            if (!canvas) return;
            const container = canvas.parentElement;
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight;
        }

        function initStage1Bubbles() {
            if (animationFrameId) cancelAnimationFrame(animationFrameId);
            bubbles = [];
            
            // 確保一開始就生成四個包含三種不同器官的氣泡
            const types = ['lung', 'trachea', 'gill'];
            for (let i = 0; i < 4; i++) {
                spawnBubble(types[i % 3]);
            }
            
            animateBubbles();
        }

        function spawnBubble(type) {
            bubbles.push({
                x: Math.random() * (canvas.width - 60) + 30,
                y: canvas.height + 40 + Math.random() * 100,
                radius: 26,
                speed: 1 + Math.random() * 1.5,
                wobble: Math.random() * Math.PI * 2,
                wobbleSpeed: 0.02 + Math.random() * 0.03,
                type: type || ['lung', 'trachea', 'gill'][Math.floor(Math.random() * 3)]
            });
        }

        function animateBubbles() {
            if (currentStage !== 1) return;
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            for (let i = 0; i < bubbles.length; i++) {
                const b = bubbles[i];
                b.y -= b.speed;
                b.wobble += b.wobbleSpeed;
                const currentX = b.x + Math.sin(b.wobble) * 15;

                // 畫氣泡外觀
                ctx.beginPath();
                ctx.arc(currentX, b.y, b.radius, 0, Math.PI * 2);
                ctx.fillStyle = 'rgba(255, 255, 255, 0.65)';
                ctx.fill();
                ctx.lineWidth = 2.5;
                ctx.strokeStyle = 'rgba(255, 255, 255, 0.95)';
                ctx.stroke();

                // 畫氣泡內的器官圖示
                ctx.font = '24px Arial';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                const icon = b.type === 'lung' ? '🫁' : b.type === 'trachea' ? '🌿' : '🫧';
                ctx.fillText(icon, currentX, b.y);

                // 氣泡飄出頂部後回收到底部，並隨機更換種類確保畫面豐富
                if (b.y < -40) {
                    b.y = canvas.height + 40;
                    b.x = Math.random() * (canvas.width - 60) + 30;
                    const types = ['lung', 'trachea', 'gill'];
                    b.type = types[Math.floor(Math.random() * types.length)];
                }
            }

            animationFrameId = requestAnimationFrame(animateBubbles);
        }

        function initCanvas() {
            canvas = document.getElementById('bubbleCanvas');
            ctx = canvas.getContext('2d');
            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);

            canvas.addEventListener('click', handleCanvasClick);
            // 新增：行動裝置觸控支援
            canvas.addEventListener('touchstart', handleCanvasTouch, { passive: false });
        }

        function handleCanvasTouch(e) {
            if (currentStage !== 1) return;
            e.preventDefault(); // 阻止畫面滾動，讓點擊更精準
            const rect = canvas.getBoundingClientRect();
            for (let i = 0; i < e.changedTouches.length; i++) {
                const touch = e.changedTouches[i];
                const clickX = touch.clientX - rect.left;
                const clickY = touch.clientY - rect.top;
                checkBubbleHit(clickX, clickY);
            }
        }

        function handleCanvasClick(e) {
            if (currentStage !== 1) return;
            const rect = canvas.getBoundingClientRect();
            const clickX = e.clientX - rect.left;
            const clickY = e.clientY - rect.top;
            checkBubbleHit(clickX, clickY);
        }

        // 將碰撞偵測獨立出來，供 Click 與 Touch 共同使用
        function checkBubbleHit(clickX, clickY) {
            for (let i = bubbles.length - 1; i >= 0; i--) {
                const b = bubbles[i];
                const currentX = b.x + Math.sin(b.wobble) * 15;
                const dist = Math.hypot(clickX - currentX, clickY - b.y);

                // 稍微放大判定範圍 (+15)，對手機觸控更友善
                if (dist < b.radius + 15) {
                    popBubble(i);
                    break;
                }
            }
        }

        function popBubble(index) {
            const b = bubbles[index];
            const needed = requiredTotalOrgans[b.type];
            const current = collectedInventory[b.type];

            if (current < needed) {
                // 收集該器官
                collectedInventory[b.type]++;
                playSound('pop');
                updateInventoryUI();
                
                // 重置該氣泡回底部
                const allTypes = ['lung', 'trachea', 'gill'];
                b.type = allTypes[Math.floor(Math.random() * allTypes.length)];
                b.y = canvas.height + 40;
                b.x = Math.random() * (canvas.width - 60) + 30;

                checkStage1Progress();
            } else {
                // 該器官已經集滿
                playSound('error');
                showToast(`【${ORGAN_NAMES[b.type]}】已經集滿囉！請尋找其他種類的器官氣泡！`);
            }
        }

        function updateInventoryUI() {
            document.getElementById('count-lung').innerText = `${collectedInventory.lung} / ${requiredTotalOrgans.lung}`;
            document.getElementById('count-trachea').innerText = `${collectedInventory.trachea} / ${requiredTotalOrgans.trachea}`;
            document.getElementById('count-gill').innerText = `${collectedInventory.gill} / ${requiredTotalOrgans.gill}`;

            const totalCollected = collectedInventory.lung + collectedInventory.trachea + collectedInventory.gill;
            document.getElementById('stage1-progress-text').innerText = `${totalCollected} / 5`;
        }

        function checkStage1Progress() {
            const totalCollected = collectedInventory.lung + collectedInventory.trachea + collectedInventory.gill;
            if (totalCollected >= 5) {
                playSound('success');
                showToast('🎉 器官裝備蒐集完成！進入第二階段：器官醫療配對！');
                setTimeout(() => {
                    currentStage = 2;
                    updateStageVisibility();
                    renderStage2();
                }, 1200);
            }
        }

        // --- 第二階段：原版配對玩法 ---
        function renderStage2() {
            const animalsContainer = document.getElementById('animals-container');
            const organsContainer = document.getElementById('organs-container');
            animalsContainer.innerHTML = '';
            organsContainer.innerHTML = '';

            let uncuredCount = stage2Animals.filter(a => !a.isCured).length;
            document.getElementById('stage2-progress-text').innerText = `${uncuredCount} / 5`;

            // 渲染左側動物選單
            stage2Animals.forEach(animal => {
                const div = document.createElement('div');
                div.className = `p-3 rounded-xl border-2 cursor-pointer flex items-center justify-between transition-all ${
                    animal.isCured 
                        ? 'bg-emerald-100 border-emerald-400 opacity-60 cursor-not-allowed' 
                        : selectedAnimalId === animal.id 
                            ? 'bg-amber-200 border-orange-500 pulse-selected' 
                            : 'bg-white border-amber-300 hover:border-amber-500'
                }`;
                div.innerHTML = `
                    <div class="flex items-center space-x-2">
                        <span class="text-2xl">${animal.icon}</span>
                        <div>
                            <div class="font-bold text-sm text-amber-950">${animal.name}</div>
                            <div class="text-xs text-amber-800">${animal.isCured ? '✅ 已配對治療' : '😷 呼吸困難中'}</div>
                        </div>
                    </div>
                `;
                if (!animal.isCured) {
                    div.onclick = () => selectAnimal(animal.id);
                }
                animalsContainer.appendChild(div);
            });

            // 渲染右側器官庫存選單
            ['lung', 'trachea', 'gill'].forEach(organKey => {
                const count = collectedInventory[organKey];
                const div = document.createElement('div');
                div.className = `p-3 rounded-xl border-2 cursor-pointer flex items-center justify-between transition-all ${
                    count === 0 
                        ? 'bg-gray-100 border-gray-300 opacity-40 cursor-not-allowed' 
                        : selectedOrganType === organKey 
                            ? 'bg-amber-200 border-orange-500 pulse-selected' 
                            : 'bg-white border-amber-300 hover:border-amber-500'
                }`;
                div.innerHTML = `
                    <div class="flex items-center space-x-2">
                        <span class="text-2xl">${organKey === 'lung' ? '🫁' : organKey === 'trachea' ? '🌿' : '🫧'}</span>
                        <span class="font-bold text-sm text-amber-950">${ORGAN_NAMES[organKey]}</span>
                    </div>
                    <span class="font-black text-orange-600 bg-amber-100 px-2 py-0.5 rounded-full text-xs">剩餘: ${count}</span>
                `;
                if (count > 0) {
                    div.onclick = () => selectOrgan(organKey);
                }
                organsContainer.appendChild(div);
            });
        }

        function selectAnimal(id) {
            selectedAnimalId = (selectedAnimalId === id) ? null : id;
            renderStage2();
            tryMatchStage2();
        }

        function selectOrgan(type) {
            if (!selectedAnimalId) {
                playSound('error');
                showToast('👉 請先點選左側需要治療的【生病動物】喔！');
                return;
            }
            selectedOrganType = (selectedOrganType === type) ? null : type;
            renderStage2();
            tryMatchStage2();
        }

        function tryMatchStage2() {
            if (!selectedAnimalId || !selectedOrganType) return;

            const animal = stage2Animals.find(a => a.id === selectedAnimalId);

            if (animal.organ === selectedOrganType) {
                // 配對成功！
                playSound('success');
                animal.isCured = true;
                collectedInventory[selectedOrganType]--;
                curedAnimals.push(animal);

                showToast(`✨ 太棒了！【${animal.name}】成功安裝了【${ORGAN_NAMES[selectedOrganType]}】！`);
                selectedAnimalId = null;
                selectedOrganType = null;

                renderStage2();
                checkStage2Progress();
            } else {
                // 配對失敗
                playSound('error');
                showToast(`❌ 配對錯誤！${animal.name} 的器官需求不符。${animal.desc}`);
                selectedOrganType = null;
                renderStage2();
            }
        }

        function checkStage2Progress() {
            const remaining = stage2Animals.filter(a => !a.isCured).length;
            if (remaining === 0) {
                setTimeout(() => {
                    playSound('success');
                    showToast('🎉 第二階段完成！所有人順利康復，進入第三階段：送動物回家！');
                    currentStage = 3;
                    updateStageVisibility();
                    renderStage3();
                }, 1000);
            }
        }

        // --- 第三階段：送動物回家 ---
        function renderStage3() {
            const container = document.getElementById('paired-animals-container');
            container.innerHTML = '';
            
            curedAnimals.forEach(animal => {
                const div = document.createElement('div');
                div.className = `btn-hand p-2.5 bg-white rounded-xl cursor-pointer flex items-center space-x-1 ${
                    selectedCuredAnimalId === animal.id ? 'bg-amber-300 border-orange-500 pulse-selected' : 'border-amber-300'
                }`;
                div.innerHTML = `<span class="text-2xl">${animal.icon}</span><span class="font-bold text-xs">${animal.name}</span>`;
                div.onclick = () => {
                    selectedCuredAnimalId = (selectedCuredAnimalId === animal.id) ? null : animal.id;
                    renderStage3();
                };
                container.appendChild(div);
            });
        }

        function placeAnimalInHabitat(targetHabitat) {
            if (!selectedCuredAnimalId) {
                playSound('error');
                showToast('👉 請先點選上方要送回家的小動物！');
                return;
            }

            const animalIndex = curedAnimals.findIndex(a => a.id === selectedCuredAnimalId);
            if (animalIndex === -1) return;

            const animal = curedAnimals[animalIndex];

            if (animal.habitat === targetHabitat) {
                // 送對棲息地
                playSound('success');
                curedAnimals.splice(animalIndex, 1);
                
                const resContainer = targetHabitat === '水生' ? document.getElementById('ocean-residents') : document.getElementById('forest-residents');
                const tag = document.createElement('div');
                tag.className = 'bg-white/90 border border-amber-300 px-2.5 py-1 rounded-lg text-xs font-bold flex items-center space-x-1 shadow-sm';
                tag.innerHTML = `<span>${animal.icon}</span><span>${animal.name}</span>`;
                resContainer.appendChild(tag);

                showToast(`🏡【${animal.name}】平安回到了${targetHabitat === '水生' ? '藍色大海' : '森林草地'}！`);
                selectedCuredAnimalId = null;
                renderStage3();

                if (curedAnimals.length === 0) {
                    setTimeout(() => {
                        playSound('success');
                        showToast('🎉 所有動物都回家了！進入最終挑戰：二氧化碳排放量排序！');
                        currentStage = 4;
                        updateStageVisibility();
                        initStage4();
                    }, 800);
                }
            } else {
                // 棲息地不符
                playSound('error');
                showToast(`❌ 棲息地不符合喔！【${animal.name}】是${animal.habitat}生物！`);
            }
        }

        // --- 第四階段：二氧化碳呼出量與濃度由多到少排序 ---
        function initStage4() {
            stage4SortedList = [];
            stage4PoolList = shuffleArray([...selectedAnimals]);
            renderStage4();
        }

        function resetStage4Sort() {
            playSound('pop');
            initStage4();
        }

        function renderStage4() {
            const slotsContainer = document.getElementById('co2-slots-container');
            const poolContainer = document.getElementById('co2-pool-container');
            const submitBtn = document.getElementById('submit-co2-btn');

            slotsContainer.innerHTML = '';
            poolContainer.innerHTML = '';

            // 渲染 5 個排序槽位 (由多到少)
            for (let i = 0; i < 5; i++) {
                const animal = stage4SortedList[i];
                const slot = document.createElement('div');
                if (animal) {
                    slot.className = 'btn-hand bg-amber-200 border-orange-500 p-2 rounded-xl flex flex-col items-center justify-center cursor-pointer min-h-[82px]';
                    slot.innerHTML = `
                        <span class="text-[10px] font-black bg-orange-500 text-white px-1.5 rounded-full mb-1">第 ${i + 1} 名</span>
                        <span class="text-2xl">${animal.icon}</span>
                        <span class="font-bold text-xs text-amber-950 mt-0.5">${animal.name}</span>
                    `;
                    slot.onclick = () => removeFromStage4Slot(i);
                } else {
                    slot.className = 'bg-white/70 border-2 border-dashed border-amber-400 p-2 rounded-xl flex flex-col items-center justify-center min-h-[82px] text-amber-400';
                    slot.innerHTML = `
                        <span class="text-xs font-black">第 ${i + 1} 名</span>
                        <span class="text-[11px] mt-1">${i === 0 ? '最多' : i === 4 ? '最少' : '待填入'}</span>
                    `;
                }
                slotsContainer.appendChild(slot);
            }

            // 渲染下方待選動物池
            stage4PoolList.forEach((animal, idx) => {
                const card = document.createElement('div');
                card.className = 'btn-hand bg-white hover:bg-amber-100 border-amber-400 px-3 py-2 rounded-xl flex items-center space-x-1.5 cursor-pointer';
                card.innerHTML = `
                    <span class="text-2xl">${animal.icon}</span>
                    <span class="font-bold text-xs text-amber-950">${animal.name}</span>
                `;
                card.onclick = () => addToStage4Slot(idx);
                poolContainer.appendChild(card);
            });

            // 排滿 5 隻才能送出驗證
            submitBtn.disabled = stage4SortedList.length < 5;
        }

        function addToStage4Slot(poolIndex) {
            if (stage4SortedList.length >= 5) return;
            playSound('pop');
            const [picked] = stage4PoolList.splice(poolIndex, 1);
            stage4SortedList.push(picked);
            renderStage4();
        }

        function removeFromStage4Slot(slotIndex) {
            playSound('pop');
            const [removed] = stage4SortedList.splice(slotIndex, 1);
            stage4PoolList.push(removed);
            renderStage4();
        }

        function checkStage4Answer() {
            if (stage4SortedList.length < 5) return;

            // 檢查是否嚴格由高到低排列 (co2Rank 遞減)
            let isCorrect = true;
            for (let i = 0; i < stage4SortedList.length - 1; i++) {
                if (stage4SortedList[i].co2Rank < stage4SortedList[i + 1].co2Rank) {
                    isCorrect = false;
                    break;
                }
            }

            if (isCorrect) {
                playSound('success');
                showToast('🎉 完全正確！二氧化碳排放量與濃度排序完全精準！');
                setTimeout(() => {
                    document.getElementById('victory-modal').classList.remove('hidden');
                    playSound('success');
                }, 700);
            } else {
                playSound('error');
                const slotsContainer = document.getElementById('co2-slots-container');
                slotsContainer.classList.add('shake-animation');
                setTimeout(() => slotsContainer.classList.remove('shake-animation'), 450);
                showToast('❌ 順序還差一點喔！提示：恆溫動物(兔、鳥)代謝快、體型大，呼出 CO₂ 最多；小型昆蟲最少！');
            }
        }

        // 取得純淨可存取的 URL，避免極長及沙盒參數量溢出
        function getCleanGameURL() {
            try {
                const href = window.location.href;
                
                // 若為預覽沙盒環境的本地網址，回傳佔位符號避免 QRCode 當機
                if (href.startsWith('about:') || href.startsWith('blob:') || href.startsWith('data:')) {
                    return "https://example.com/sandbox-preview";
                }

                const origin = window.location.origin;
                const pathname = window.location.pathname;

                if (origin && origin !== 'null' && !origin.startsWith('file')) {
                    return origin + pathname;
                }
                return href.split('?')[0].split('#')[0];
            } catch (e) {
                return window.location.href;
            }
        }

        function openQRModal() {
            const modal = document.getElementById('qr-modal');
            const qrContainer = document.getElementById('qrcode-container');
            const warning = document.getElementById('qr-sandbox-warning');
            qrContainer.innerHTML = '';
            modal.classList.remove('hidden');

            const targetUrl = getCleanGameURL();
            
            // 檢測是否在預覽環境，若是則顯示紅色警告
            const isSandbox = window.location.href.startsWith('about:') || window.location.href.startsWith('blob:') || window.location.origin === 'null';
            if (warning) {
                warning.classList.toggle('hidden', !isSandbox);
            }

            try {
                new QRCode(qrContainer, {
                    text: targetUrl,
                    width: 160,
                    height: 160,
                    colorDark: '#382d21',
                    colorLight: '#ffffff',
                    correctLevel: QRCode.CorrectLevel.L
                });
            } catch (err) {
                qrContainer.innerHTML = `<p class="text-xs text-amber-900 font-bold p-2">請直接複製連結分享此遊戲！</p>`;
            }
        }

        function closeQRModal() {
            document.getElementById('qr-modal').classList.add('hidden');
        }

        function copyCurrentURL() {
            const url = getCleanGameURL();
            if (navigator.clipboard) {
                navigator.clipboard.writeText(url).then(() => {
                    showToast('📋 網址已成功複製到剪貼簿！');
                }).catch(() => {
                    fallbackCopyText(url);
                });
            } else {
                fallbackCopyText(url);
            }
        }

        function fallbackCopyText(text) {
            const textArea = document.createElement('textarea');
            textArea.value = text;
            document.body.appendChild(textArea);
            textArea.select();
            try {
                document.execCommand('copy');
                showToast('📋 網址已成功複製！');
            } catch (err) {
                showToast('請手動複製網址分享！');
            }
            document.body.removeChild(textArea);
        }

        // 洗牌工具函式
        function shuffleArray(arr) {
            return arr.sort(() => Math.random() - 0.5);
        }
    </script>
</body>
</html>
