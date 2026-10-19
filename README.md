<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>動物呼吸大救援 - 曠野繪本風格</title>
    <!-- 引入圓潤且具藝術感的 Nunito 體 -->
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@700;800;900&display=swap" rel="stylesheet">
    <!-- 引入 QRCode.js 本地生成庫 (避免外部 API 請求失敗) -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <style>
        :root {
            --ink-black: #2b231d; /* 深黑棕色手繪墨線 */
            --paper-bg: #e2c99a; /* 大地暖黃復古紙張色 */
            --card-bg: #f9f2e3; /* 暖米白色卡片 */
            --sun-red: #d95e38; /* 手繪太陽橙紅 */
            --river-teal: #68b0ab; /* 曠野江河藍綠色 */
            --desert-green: #88a768; /* 仙人掌草綠色 */
            --accent-yellow: #f1c40f;
            --accent-orange: #e67e22;
        }

        body {
            margin: 0;
            padding: 0;
            font-family: 'Nunito', 'Comic Sans MS', '微軟正黑體', sans-serif;
            background-color: var(--paper-bg);
            /* 模擬復古繪本斑駁紙張與微粒質感 */
            background-image: 
                radial-gradient(circle, rgba(139, 108, 66, 0.12) 1px, transparent 0.5px),
                linear-gradient(to bottom, rgba(226, 201, 154, 0.8), rgba(210, 178, 120, 0.95));
            background-size: 20px 20px, 100% 100%;
            color: var(--ink-black);
            touch-action: manipulation;
            -webkit-overflow-scrolling: touch;
            text-align: center;
            min-height: 100vh;
            position: relative;
            overflow-x: hidden;
        }

        .decoration-sun {
            position: fixed;
            top: 15px;
            left: 20px;
            font-size: 55px;
            z-index: 0;
            filter: drop-shadow(3px 3px 0px var(--ink-black));
            animation: sunPulse 4s ease-in-out infinite alternate;
            user-select: none;
            pointer-events: none;
        }

        .decoration-butterfly {
            position: fixed;
            top: 80px;
            right: 40px;
            font-size: 35px;
            z-index: 0;
            animation: butterflyFloat 6s ease-in-out infinite alternate;
            user-select: none;
            pointer-events: none;
        }

        @keyframes sunPulse {
            0% { transform: scale(1) rotate(-3deg); }
            100% { transform: scale(1.08) rotate(5deg); }
        }

        @keyframes butterflyFloat {
            0% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-15px) rotate(10deg); }
            100% { transform: translateY(-5px) rotate(-8deg); }
        }

        #app {
            max-width: 850px;
            margin: 0 auto;
            padding: 20px;
            box-sizing: border-box;
            position: relative;
            z-index: 1;
        }

        .screen {
            display: none;
            background: var(--card-bg);
            padding: 30px 25px;
            border-radius: 255px 15px 225px 15px/15px 225px 15px 255px;
            border: 4px solid var(--ink-black);
            box-shadow: 6px 8px 0px var(--ink-black);
            animation: popIn 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            margin-bottom: 20px;
            position: relative;
        }

        .screen.active {
            display: block;
        }

        @keyframes popIn {
            from { opacity: 0; transform: scale(0.95) translateY(10px); }
            to { opacity: 1; transform: scale(1) translateY(0); }
        }

        h1 { 
            color: var(--sun-red); 
            font-size: 2.5em;
            font-weight: 900;
            text-shadow: 3px 3px 0px var(--ink-black);
            margin-top: 0;
            letter-spacing: 2px;
            -webkit-text-stroke: 1px var(--ink-black);
        }

        h2 { 
            color: var(--sun-red);
            font-size: 1.9em;
            display: inline-block;
            position: relative;
            z-index: 1;
            font-weight: 900;
        }

        h2::after {
            content: '';
            position: absolute;
            bottom: 2px;
            left: -8px;
            right: -8px;
            height: 14px;
            background-color: var(--accent-yellow);
            z-index: -1;
            border-radius: 8px;
            transform: rotate(-1.5deg);
            border: 2px solid var(--ink-black);
        }
        
        button {
            background-color: #f39c12;
            border: 3.5px solid var(--ink-black);
            border-radius: 20px;
            padding: 12px 30px;
            font-size: 22px;
            font-weight: 900;
            color: #fff;
            text-shadow: 1.5px 1.5px 0px var(--ink-black);
            cursor: pointer;
            margin: 15px 10px;
            box-shadow: 4px 5px 0px var(--ink-black);
            transition: all 0.15s ease;
            font-family: inherit;
        }

        button:hover {
            transform: translate(2px, 2px);
            box-shadow: 2px 3px 0px var(--ink-black);
            background-color: #e67e22;
        }

        button:active { 
            transform: translate(4px, 5px); 
            box-shadow: 0px 0px 0px var(--ink-black);
        }

        button * { pointer-events: none; }

        canvas {
            background-color: #fdf8eb;
            border: 4px solid var(--ink-black);
            border-radius: 15px 225px 15px 255px / 255px 15px 225px 15px;
            box-shadow: inset 0 0 15px rgba(139, 108, 66, 0.15), 5px 7px 0px var(--ink-black);
            max-width: 100%;
            height: auto;
            touch-action: none;
            cursor: pointer;
        }

        #inventory { 
            font-size: 22px; 
            font-weight: 900; 
            color: var(--ink-black);
            background: #fff;
            display: inline-block;
            padding: 8px 25px;
            border: 3.5px solid var(--ink-black);
            border-radius: 30px;
            margin-bottom: 15px;
            box-shadow: 3px 4px 0px var(--ink-black);
            transform: rotate(-1deg);
        }

        .flex-container { 
            display: flex; 
            flex-wrap: wrap; 
            justify-content: center; 
            gap: 15px; 
            margin-top: 20px; 
            padding: 10px;
        }
        
        .card {
            border: 3.5px solid var(--ink-black);
            border-radius: 18px 12px 20px 14px;
            padding: 15px 20px;
            font-size: 22px;
            font-weight: 800;
            cursor: pointer;
            background: var(--card-bg);
            user-select: none;
            box-shadow: 4px 5px 0px var(--ink-black);
            transition: all 0.2s ease-out;
            position: relative;
            min-width: 110px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .card * { pointer-events: none; }
        
        .card:hover {
            transform: translateY(-5px) rotate(1.5deg);
        }
        
        .card.selected { 
            border-color: var(--sun-red); 
            background: #ffe399; 
            transform: translateY(-5px) scale(1.06);
            box-shadow: 5px 7px 0px var(--ink-black);
        }
        
        .card.paired { 
            border-color: var(--ink-black); 
            background: #d4e6c3; 
            opacity: 0.95; 
            pointer-events: none;
            transform: scale(0.95);
            box-shadow: 2px 2px 0px var(--ink-black);
        }

        .habitat {
            width: 45%;
            min-height: 220px;
            border: 4px solid var(--ink-black);
            border-radius: 25px 12px 20px 15px;
            display: inline-block;
            vertical-align: top;
            margin: 10px 2%;
            padding: 15px;
            box-sizing: border-box;
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
            box-shadow: 5px 7px 0px var(--ink-black);
            position: relative;
        }

        .habitat:hover {
            transform: translateY(-4px);
            box-shadow: 6px 9px 0px var(--ink-black);
        }
        
        #habitat-ocean { 
            background: var(--river-teal);
            background-image: radial-gradient(circle at 10% 20%, rgba(255, 255, 255, 0.2) 0%, transparent 20%);
        }

        #habitat-forest { 
            background: var(--desert-green);
            background-image: radial-gradient(circle at 90% 80%, rgba(255, 255, 255, 0.2) 0%, transparent 20%);
        }

        .habitat h3 {
            background: #fff;
            border: 3px solid var(--ink-black);
            border-radius: 20px;
            padding: 6px 18px;
            display: inline-block;
            margin-top: 0;
            box-shadow: 3px 3px 0px var(--ink-black);
            font-weight: 900;
        }

        .residents-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 8px;
            margin-top: 15px;
        }

        .residents-container .card {
            font-size: 16px;
            padding: 8px 12px;
            min-width: auto;
            pointer-events: none;
            box-shadow: 2px 3px 0px var(--ink-black);
        }

        .qr-section { 
            margin-top: 30px; 
            padding: 20px; 
            background: rgba(255,255,255,0.6); 
            border-radius: 15px; 
            border: 3px dashed var(--ink-black);
        }

        .qr-section input { 
            width: 80%; 
            padding: 12px; 
            font-size: 16px; 
            border: 3px solid var(--ink-black);
            border-radius: 12px;
            margin-bottom: 10px; 
            outline: none;
            font-family: inherit;
        }

        .share-btn-group {
            display: flex;
            justify-content: center;
            gap: 10px;
            flex-wrap: wrap;
            margin-top: 5px;
        }

        .share-btn-group button {
            font-size: 16px;
            padding: 8px 18px;
            margin: 5px;
        }

        #qrcode-container {
            margin-top: 15px;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        #qrcode-container img, #qrcode-container canvas {
            border: 4px solid var(--ink-black);
            border-radius: 12px;
            background: #fff;
            padding: 8px;
            box-shadow: 4px 5px 0px var(--ink-black);
        }

        .warning { color: var(--sun-red); font-size: 15px; font-weight: bold; margin-top: 5px; }
        
        #gameover-reason { 
            font-size: 22px; 
            font-weight: bold; 
            color: #b33939; 
            background: #f8d7da; 
            border: 3.5px solid var(--ink-black);
            padding: 15px; 
            border-radius: 15px; 
            box-shadow: 4px 5px 0px var(--ink-black);
        }

        #toast {
            display: none;
            position: fixed;
            bottom: 40px;
            left: 50%;
            transform: translateX(-50%);
            background: var(--ink-black);
            color: #f9f2e3;
            padding: 14px 28px;
            border-radius: 50px;
            border: 3px solid var(--paper-bg);
            font-size: 18px;
            font-weight: bold;
            z-index: 1000;
            box-shadow: 0 10px 25px rgba(0,0,0,0.4);
            animation: slideUp 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }

        @keyframes slideUp {
            from { bottom: 0px; opacity: 0; }
            to { bottom: 40px; opacity: 1; }
        }

        .snail-container {
            position: fixed;
            bottom: 12px;
            left: -90px;
            z-index: 99;
            pointer-events: auto;
            cursor: pointer;
            animation: snailCrawl 42s linear infinite;
            display: flex;
            align-items: center;
            gap: 6px;
            user-select: none;
        }

        .snail-body {
            font-size: 38px;
            display: inline-block;
            animation: snailWiggle 2s ease-in-out infinite alternate;
            filter: drop-shadow(3px 3px 0px var(--ink-black));
        }

        .snail-bubble {
            background: #fff;
            border: 3px solid var(--ink-black);
            border-radius: 14px;
            padding: 5px 12px;
            font-size: 13px;
            font-weight: 900;
            color: var(--ink-black);
            white-space: nowrap;
            box-shadow: 3px 3px 0px var(--ink-black);
            position: relative;
        }

        .snail-bubble::after {
            content: '';
            position: absolute;
            left: -8px;
            top: 50%;
            transform: translateY(-50%);
            border-width: 6px 8px 6px 0;
            border-style: solid;
            border-color: transparent var(--ink-black) transparent transparent;
        }

        @keyframes snailCrawl {
            0% { left: -100px; transform: scaleX(1); }
            48% { left: calc(100vw + 20px); transform: scaleX(1); }
            50% { left: calc(100vw + 20px); transform: scaleX(-1); }
            98% { left: -100px; transform: scaleX(-1); }
            100% { left: -100px; transform: scaleX(1); }
        }

        @keyframes snailWiggle {
            0% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-3px) rotate(4deg); }
            100% { transform: translateY(0px) rotate(-3deg); }
        }

        @media (max-width: 600px) {
            .habitat { width: 95%; margin-bottom: 15px; }
            button { width: 90%; font-size: 18px; }
            .card { width: 40%; font-size: 18px; padding: 10px; }
            .snail-container { bottom: 5px; }
            .snail-body { font-size: 30px; }
            .snail-bubble { font-size: 11px; padding: 3px 7px; }
            .decoration-sun { font-size: 40px; }
            .decoration-butterfly { font-size: 28px; }
        }
    </style>
</head>
<body>

<div class="decoration-sun" title="手繪太陽">☀️</div>
<div class="decoration-butterfly" title="曠野蝴蝶">🦋</div>

<div id="app">
    <!-- 首頁與說明 -->
    <div id="screen-intro" class="screen active">
        <h1>☀️ 曠野繪本：動物呼吸救援 ☀️</h1>
        <p style="font-size: 1.25em; line-height: 1.6; font-weight: 700;">
            「看哪，我要做一件新事！在曠野開道路，在沙漠開江河。」<br>
            突然發生的時空風暴把可愛的動物吹離了棲地，牠們現在急需呼吸裝備！
        </p>
        <p style="font-size: 1.1em; background: #ffeaa7; padding: 15px; border-radius: 18px; border: 3px dashed var(--ink-black); font-weight: bold;">
            🧑‍🌾 小小救援隊任務：<br>
            收集呼吸器官裝備，為小動物進行醫療配對，並把牠們送回溫暖的江河與森林！
        </p>
        <p style="color: var(--sun-red); font-weight: 900; font-size: 1.1em;">
            ⚠️ 提醒：從第二階段起，只要配對錯誤或送錯家，任務就會失敗喔！
        </p>
        <button onclick="startGame()">🚀 啟程！救援任務</button>

        <div class="qr-section">
            <h3 style="margin-top: 0;">📱 平板掃描與遊戲分享區</h3>
            <p style="font-size: 14px; color: #555;">如果已上傳至 GitHub Pages，請貼上公開網址生成 QR Code：</p>
            <input type="text" id="urlInput" placeholder="https://你的帳號.github.io/專案名稱/">
            <br>
            <div class="share-btn-group">
                <button onclick="generateQR()">產生 QR Code</button>
                <button onclick="copyShareUrl()" style="background-color: var(--river-teal);">📋 複製遊戲連結</button>
            </div>
            <div id="qrWarning" class="warning"></div>
            <div id="qrcode-container"></div>
        </div>
    </div>

    <!-- 第一階段 -->
    <div id="screen-phase1" class="screen">
        <h2>第一階段：搶救呼吸器官！</h2>
        <p style="font-size: 1.2em; font-weight: 700;">點擊從空中飄落的裝備泡泡，收集 5 個器官裝備！</p>
        <div id="inventory">🎒 裝備包：0 / 5</div>
        <br>
        <canvas id="gameCanvas" width="650" height="400"></canvas>
    </div>

    <!-- 第二階段 -->
    <div id="screen-phase2" class="screen">
        <h2>第二階段：醫療配對</h2>
        <p style="font-size: 1.15em; font-weight: 700; margin-bottom: 5px;">點擊受困動物，再點選專屬於牠的呼吸器官！</p>
        
        <div style="background: rgba(217, 94, 56, 0.12); padding: 15px; border-radius: 18px; border: 3px dashed var(--sun-red); margin-top: 20px;">
            <h3 style="margin-top: 0; color: var(--sun-red); font-weight: 900;">😿 等待救援的動物</h3>
            <div id="animal-container" class="flex-container"></div>
        </div>

        <div style="background: rgba(104, 176, 171, 0.15); padding: 15px; border-radius: 18px; border: 3px dashed var(--river-teal); margin-top: 20px;">
            <h3 style="margin-top: 0; color: var(--ink-black); font-weight: 900;">📦 收集到的呼吸裝備</h3>
            <div id="organ-container" class="flex-container"></div>
        </div>
        
        <button id="btn-next-phase" style="display:none; margin-top: 25px;" onclick="goToPhase3()">
            ➡️ 裝備完畢！準備送動物回家
        </button>
    </div>

    <!-- 第三階段 -->
    <div id="screen-phase3" class="screen">
        <h2>第三階段：送動物回棲地</h2>
        <p style="font-size: 1.15em; font-weight: 700;">點擊下方的動物，再點擊對應的家園（江河或森林）！</p>
        
        <div id="paired-animals-container" class="flex-container" style="background: #fff; border: 3.5px solid var(--ink-black); border-radius: 18px; padding: 15px; margin-bottom: 25px; box-shadow: inset 2px 2px 5px rgba(0,0,0,0.08);"></div>

        <div id="habitat-ocean" class="habitat" onclick="placeAnimal('水生')">
            <h3>🌊 開江河（水生棲地）</h3>
            <p style="color: #fff; text-shadow: 1px 1px 0px var(--ink-black); font-size: 14px; font-weight: bold; margin: 5px 0 0 0;">魚類與水生動物的家</p>
            <div id="ocean-residents" class="residents-container"></div>
        </div>
        <div id="habitat-forest" class="habitat" onclick="placeAnimal('陸生')">
            <h3>🌵 開道路（陸生棲地）</h3>
            <p style="color: #fff; text-shadow: 1px 1px 0px var(--ink-black); font-size: 14px; font-weight: bold; margin: 5px 0 0 0;">陸生動物與昆蟲的家</p>
            <div id="forest-residents" class="residents-container"></div>
        </div>
    </div>

    <!-- 成功與失敗畫面 -->
    <div id="screen-victory" class="screen">
        <h1 style="font-size: 3em; margin-bottom: 10px; color: var(--sun-red);">🎉 曠野救援大成功！ 🎉</h1>
        <p style="font-size: 1.3em; font-weight: 700;">太棒了！所有的動物都順利裝上了呼吸器官，<br>並在沙漠與江河中回到了溫暖的家！</p>
        <div style="font-size: 70px; margin: 20px 0; animation: bounce 2s infinite;">☀️🏡🐕🐠🦋🌵</div>
        <button onclick="location.reload()">🔄 再次開啟救援任務</button>
    </div>

    <div id="screen-gameover" class="screen">
        <h1 style="font-size: 3em; margin-bottom: 10px; color: #b33939;">💥 哎呀，救援中斷了！ 💥</h1>
        <p id="gameover-reason"></p>
        <div style="font-size: 70px; margin: 20px 0;">😢🚑❌</div>
        <button onclick="location.reload()">🔄 重新開始挑戰</button>
    </div>
</div>

<!-- 慢悠悠爬行的繪本風小蝸牛 -->
<div class="snail-container" onclick="showToast('🐌 小蝸牛說：我有肺器官喔！就算慢慢爬也會到家的～')">
    <span class="snail-body">🐌</span>
    <span class="snail-bubble">吸～呼～慢慢爬...🐌</span>
</div>

<div id="toast"></div>

<script>
    const allAnimals = [
        { name: '小狗', type: '陸生', organ: '肺', emoji: '🐕' },
        { name: '小貓', type: '陸生', organ: '肺', emoji: '🐈' },
        { name: '小蝸牛', type: '陸生', organ: '肺', emoji: '🐌' },
        { name: '人類', type: '陸生', organ: '肺', emoji: '🧑' },
        { name: '金魚', type: '水生', organ: '鰓', emoji: '🐠' },
        { name: '吳郭魚', type: '水生', organ: '鰓', emoji: '🐟' },
        { name: '鯊魚', type: '水生', organ: '鰓', emoji: '🦈' },
        { name: '蝗蟲', type: '陸生', organ: '氣管', emoji: '🦗' },
        { name: '蝴蝶', type: '陸生', organ: '氣管', emoji: '🦋' },
        { name: '蜜蜂', type: '陸生', organ: '氣管', emoji: '🐝' }
    ];
    
    const organInfo = {
        '肺': { emoji: '🫁', color: '#f3a683' },
        '鰓': { emoji: '🫧', color: '#778beb' },
        '氣管': { emoji: '🌿', color: '#78e08f' }
    };
    const organTypes = Object.keys(organInfo);
    
    let collectedOrgans = [];
    let selectedAnimals = [];
    let activeAnimalCard = null;
    let activeOrganCard = null;
    let pairedCount = 0;
    
    let activePairedAnimal = null;
    let rescuedCount = 0;
    let qrInstance = null;

    function showToast(message) {
        const toast = document.getElementById('toast');
        toast.innerText = message;
        toast.style.display = 'block';
        toast.style.animation = 'none';
        toast.offsetHeight;
        toast.style.animation = 'slideUp 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275)';
        setTimeout(() => toast.style.display = 'none', 2800);
    }

    function showScreen(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    function triggerGameOver(reason) {
        document.getElementById('gameover-reason').innerText = reason;
        showScreen('screen-gameover');
    }

    window.onload = function() {
        let currentUrl = window.location.href;
        // 避免抓取到 Google Sites 內部暫存的沙盒網址
        if (currentUrl.includes('googleusercontent.com') || currentUrl.includes('google.com/embed')) {
            currentUrl = ''; // 清空預設值，提示使用者輸入真正的部署網址
            document.getElementById('qrWarning').innerText = "💡 提示：若是在 Google 協作平台中，請先將遊戲上傳至 GitHub Pages，並將網址貼在上方欄位以生成正確的 QR Code！";
        } else {
            try {
                if (window.self !== window.top && window.top.location.href) {
                    currentUrl = window.top.location.href;
                }
            } catch (e) {
                currentUrl = window.location.href;
            }
        }

        const urlInput = document.getElementById('urlInput');
        if (urlInput && currentUrl) {
            urlInput.value = currentUrl;
            generateQR();
        }
    };
    
    function generateQR() {
        const urlInput = document.getElementById('urlInput');
        let url = urlInput.value.trim();
        const warning = document.getElementById('qrWarning');
        const qrContainer = document.getElementById('qrcode-container');

        if (!url) {
            warning.innerText = "請輸入有效的公開網址（例如 GitHub Pages 網址）！";
            qrContainer.innerHTML = '';
            return;
        }

        if (url.startsWith('file://')) {
            warning.innerText = "⚠️ 目前為本機檔案，平板無法掃描！請先上傳至 GitHub Pages。";
            qrContainer.innerHTML = '';
            return;
        }

        warning.innerText = "";
        qrContainer.innerHTML = '';

        try {
            new QRCode(qrContainer, {
                text: url,
                width: 180,
                height: 180,
                colorDark : "#2b231d",
                colorLight : "#ffffff",
                correctLevel : QRCode.CorrectLevel.M
            });
        } catch (err) {
            console.error("QR Code 生成異常:", err);
            warning.innerText = "生成失敗，請再試一次。";
        }
    }

    function copyShareUrl() {
        const urlInput = document.getElementById('urlInput');
        urlInput.select();
        urlInput.setSelectionRange(0, 99999);

        try {
            const successful = document.execCommand('copy');
            if (successful) {
                showToast('📋 連結已成功複製到剪貼簿！');
            } else {
                showToast('⚠️ 複製失敗，請手動選取文字複製');
            }
        } catch (err) {
            showToast('⚠️ 複製失敗，請手動選取文字複製');
        }
    }

    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    let gameLoopId;
    let items = [];
    let isPhase1Running = false;

    function startGame() {
        showScreen('screen-phase1');
        collectedOrgans = [];
        items = [];
        isPhase1Running = true;
        document.getElementById('inventory').innerText = `🎒 裝備包：0 / 5`;
        
        const appWidth = document.getElementById('app').clientWidth;
        canvas.width = Math.min(650, appWidth - 40);

        spawnItem();
        gameLoop();
    }

    function spawnItem() {
        if (!isPhase1Running) return;
        const type = organTypes[Math.floor(Math.random() * organTypes.length)];
        items.push({
            type: type,
            x: Math.random() * (canvas.width - 90) + 45,
            y: -50,
            speed: 1.0 + Math.random() * 1.4,
            radius: 36,
            wobbleOffset: Math.random() * Math.PI * 2
        });
        setTimeout(spawnItem, 1100);
    }

    function gameLoop() {
        if (!isPhase1Running) return;
        
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        ctx.strokeStyle = "rgba(139, 108, 66, 0.15)";
        ctx.lineWidth = 2;
        ctx.beginPath();
        ctx.moveTo(10, 200); ctx.quadraticCurveTo(200, 180, 400, 220);
        ctx.stroke();
        
        for (let i = items.length - 1; i >= 0; i--) {
            let item = items[i];
            item.y += item.speed;
            let currentX = item.x + Math.sin((item.y / 35) + item.wobbleOffset) * 18;
            
            ctx.beginPath();
            ctx.arc(currentX, item.y, item.radius, 0, Math.PI * 2);
            ctx.fillStyle = organInfo[item.type].color;
            ctx.fill();
            
            ctx.lineWidth = 3.5;
            ctx.strokeStyle = '#2b231d';
            ctx.stroke();
            
            ctx.beginPath();
            ctx.arc(currentX - 10, item.y - 12, 8, 0, Math.PI * 2);
            ctx.fillStyle = 'rgba(255, 255, 255, 0.65)';
            ctx.fill();

            ctx.font = '26px sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(organInfo[item.type].emoji, currentX, item.y - 4);

            ctx.font = 'bold 13px Nunito, sans-serif';
            ctx.fillStyle = '#2b231d';
            ctx.fillText(item.type, currentX, item.y + 18);

            if (item.y > canvas.height + 50) {
                items.splice(i, 1);
            }
        }

        gameLoopId = requestAnimationFrame(gameLoop);
    }

    function handleCanvasClick(e) {
        if (!isPhase1Running) return;
        
        const rect = canvas.getBoundingClientRect();
        const scaleX = canvas.width / rect.width;
        const scaleY = canvas.height / rect.height;
        
        let clientX = e.clientX;
        let clientY = e.clientY;

        if (e.touches && e.touches.length > 0) {
            clientX = e.touches[0].clientX;
            clientY = e.touches[0].clientY;
        }
        
        const clickX = (clientX - rect.left) * scaleX;
        const clickY = (clientY - rect.top) * scaleY;
        
        for (let i = items.length - 1; i >= 0; i--) {
            let item = items[i];
            let currentX = item.x + Math.sin((item.y / 35) + item.wobbleOffset) * 18;
            let dist = Math.sqrt(Math.pow(clickX - currentX, 2) + Math.pow(clickY - item.y, 2));
            
            if (dist <= item.radius + 18) { 
                collectedOrgans.push(item.type);
                items.splice(i, 1);
                
                document.getElementById('inventory').innerText = `🎒 裝備包：${collectedOrgans.length} / 5`;
                
                if (collectedOrgans.length >= 5) {
                    isPhase1Running = false;
                    cancelAnimationFrame(gameLoopId);
                    showToast('裝備收集完成！進行第二階段配對');
                    setTimeout(startPhase2, 900);
                }
                break;
            }
        }
    }

    canvas.addEventListener('pointerdown', function(e) { e.preventDefault(); handleCanvasClick(e); });
    canvas.addEventListener('touchstart', function(e) { e.preventDefault(); handleCanvasClick(e); }, { passive: false });
    canvas.addEventListener('mousedown', function(e) { handleCanvasClick(e); });

    function startPhase2() {
        showScreen('screen-phase2');
        pairedCount = 0;
        
        let shuffledAnimals = [...allAnimals].sort(() => 0.5 - Math.random());
        selectedAnimals = shuffledAnimals.slice(0, 5);
        
        const animalContainer = document.getElementById('animal-container');
        const organContainer = document.getElementById('organ-container');
        animalContainer.innerHTML = '';
        organContainer.innerHTML = '';
        
        selectedAnimals.forEach((animal) => {
            let div = document.createElement('div');
            div.className = 'card';
            div.dataset.name = animal.name; 
            div.dataset.organ = animal.organ;
            div.innerHTML = `<span style="font-size: 2.2em; margin-bottom: 4px;">${animal.emoji}</span><span>${animal.name}</span>`;
            div.onclick = () => selectAnimalCard(div);
            animalContainer.appendChild(div);
        });
        
        collectedOrgans.forEach((organ) => {
            let div = document.createElement('div');
            div.className = 'card';
            div.style.backgroundColor = organInfo[organ].color;
            div.dataset.organ = organ;
            div.innerHTML = `<span style="font-size: 2.2em; margin-bottom: 4px;">${organInfo[organ].emoji}</span><span>${organ}</span>`;
            div.onclick = () => selectOrganCard(div);
            organContainer.appendChild(div);
        });
    }

    function selectAnimalCard(card) {
        if (card.classList.contains('paired')) return;
        document.querySelectorAll('#animal-container .card').forEach(c => c.classList.remove('selected'));
        card.classList.add('selected');
        activeAnimalCard = card;
        checkMatch();
    }

    function selectOrganCard(card) {
        if (card.classList.contains('paired')) return;
        document.querySelectorAll('#organ-container .card').forEach(c => c.classList.remove('selected'));
        card.classList.add('selected');
        activeOrganCard = card;
        checkMatch();
    }

    function checkMatch() {
        if (activeAnimalCard && activeOrganCard) {
            let animalOrgan = activeAnimalCard.dataset.organ;
            let animalName = activeAnimalCard.dataset.name;
            let selectedOrgan = activeOrganCard.dataset.organ;
            
            if (animalOrgan === selectedOrgan) {
                activeAnimalCard.classList.add('paired');
                activeAnimalCard.classList.remove('selected');
                
                let emoji = organInfo[selectedOrgan].emoji;
                activeAnimalCard.innerHTML += `<div style="font-size: 14px; color: #1e5a22; font-weight:900; margin-top:4px;">✅ 裝備 ${emoji}</div>`;
                
                let matchedOrganCard = activeOrganCard;
                matchedOrganCard.style.transform = 'scale(0)';
                matchedOrganCard.style.opacity = '0';
                setTimeout(() => matchedOrganCard.style.display = 'none', 200);
                
                activeAnimalCard = null;
                activeOrganCard = null;
                pairedCount++;
                
                showToast('✨ 裝備成功！');

                if (pairedCount >= 5) {
                    document.getElementById('btn-next-phase').style.display = 'inline-block';
                }
            } else {
                triggerGameOver(`糟糕！「${animalName}」的呼吸器官不是「${selectedOrgan}」，小動物無法呼吸，救援失敗！`);
            }
        }
    }

    function goToPhase3() {
        showScreen('screen-phase3');
        rescuedCount = 0;
        const container = document.getElementById('paired-animals-container');
        container.innerHTML = '';
        
        selectedAnimals.forEach((animal) => {
            let div = document.createElement('div');
            div.className = 'card';
            div.innerHTML = `<span style="font-size: 1.6em;">${animal.emoji}</span><span>${animal.name}</span>`;
            div.dataset.type = animal.type;
            div.dataset.name = animal.name;
            div.onclick = () => selectPairedAnimal(div);
            container.appendChild(div);
        });
    }

    function selectPairedAnimal(card) {
        if (card.classList.contains('paired')) return;
        document.querySelectorAll('#paired-animals-container .card').forEach(c => c.classList.remove('selected'));
        card.classList.add('selected');
        activePairedAnimal = card;
    }

    function placeAnimal(habitatType) {
        if (!activePairedAnimal) {
            showToast('👉 請先點選上方一隻準備回家的動物！');
            return;
        }
        
        let correctType = activePairedAnimal.dataset.type;
        let animalName = activePairedAnimal.dataset.name;
        
        if (correctType === habitatType) {
            activePairedAnimal.classList.add('paired');
            activePairedAnimal.classList.remove('selected');
            activePairedAnimal.onclick = null;
            
            if (habitatType === '水生') {
                document.getElementById('ocean-residents').appendChild(activePairedAnimal);
            } else {
                document.getElementById('forest-residents').appendChild(activePairedAnimal);
            }
            
            activePairedAnimal = null;
            rescuedCount++;
            
            showToast('🏠 安全送達家園！');

            if (rescuedCount >= 5) {
                setTimeout(() => {
                    showScreen('screen-victory');
                }, 800);
            }
        } else {
            triggerGameOver(`糟糕！送錯地方了，「${habitatType}」環境不適合「${animalName}」，救援失敗！`);
        }
    }
</script>
</body>
</html>
