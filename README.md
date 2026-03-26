<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Pixel Battle | Glassmorphism iOS Style</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }

        body {
            min-height: 100vh;
            background: radial-gradient(circle at 20% 30%, #1a1f2e, #0a0e1a);
            font-family: -apple-system, 'SF Pro Display', 'Segoe UI', 'Poppins', system-ui, sans-serif;
            padding: 20px;
            position: relative;
            overflow-x: auto;
        }

        /* Декоративные блики */
        body::before {
            content: '';
            position: fixed;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255,255,255,0.08) 0%, transparent 70%);
            pointer-events: none;
            animation: rotate 30s linear infinite;
        }

        @keyframes rotate {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }

        .main-wrapper {
            max-width: 1400px;
            margin: 0 auto;
            position: relative;
            z-index: 2;
        }

        /* Стеклянные панели */
        .glass-panel {
            background: rgba(20, 25, 45, 0.55);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-radius: 32px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2), inset 0 1px 0 rgba(255, 255, 255, 0.1);
        }

        .control-bar {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            gap: 16px;
            padding: 16px 24px;
            margin-bottom: 20px;
        }

        /* Клановые кнопки в стиле iOS */
        .clan-buttons {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
        }

        .clan-btn {
            border: none;
            font-weight: 600;
            font-size: 0.9rem;
            padding: 10px 20px;
            border-radius: 40px;
            cursor: pointer;
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
            color: white;
            text-shadow: 0 1px 2px rgba(0,0,0,0.2);
            background: rgba(255,255,255,0.15);
            backdrop-filter: blur(4px);
            border: 1px solid rgba(255,255,255,0.2);
            letter-spacing: 0.3px;
        }

        .clan-btn[data-clan="0"] { background: linear-gradient(135deg, #e34d4d, #c0392b); box-shadow: 0 4px 15px rgba(227,77,77,0.3); }
        .clan-btn[data-clan="1"] { background: linear-gradient(135deg, #4caf50, #2e7d32); box-shadow: 0 4px 15px rgba(76,175,80,0.3); }
        .clan-btn[data-clan="2"] { background: linear-gradient(135deg, #2196f3, #1565c0); box-shadow: 0 4px 15px rgba(33,150,243,0.3); }
        .clan-btn[data-clan="3"] { background: linear-gradient(135deg, #ffc107, #ff8f00); box-shadow: 0 4px 15px rgba(255,193,7,0.3); color: #1a1a2e; text-shadow: none; }
        .clan-btn[data-clan="4"] { background: linear-gradient(135deg, #9c27b0, #6a1b9a); box-shadow: 0 4px 15px rgba(156,39,176,0.3); }

        .clan-btn.active {
            transform: scale(1.05);
            box-shadow: 0 0 0 3px rgba(255,215,0,0.6), 0 8px 25px rgba(0,0,0,0.3);
            filter: brightness(1.05);
        }

        .clan-btn:hover {
            transform: translateY(-2px);
            filter: brightness(1.05);
        }

        /* Таймер */
        .timer-card {
            background: rgba(0, 0, 0, 0.6);
            backdrop-filter: blur(20px);
            padding: 8px 24px;
            border-radius: 60px;
            font-family: 'SF Mono', monospace;
            font-size: 2rem;
            font-weight: 700;
            color: #0ff;
            text-shadow: 0 0 12px #0ff, 0 0 5px #0ff;
            letter-spacing: 2px;
            border: 1px solid rgba(0,255,255,0.3);
        }

        /* Zoom контролы */
        .zoom-controls {
            display: flex;
            gap: 12px;
        }

        .zoom-btn {
            background: rgba(255,255,255,0.15);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255,255,255,0.25);
            padding: 8px 18px;
            border-radius: 40px;
            color: white;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.2s;
            font-size: 1rem;
        }

        .zoom-btn:hover {
            background: rgba(255,255,255,0.3);
            transform: scale(0.96);
        }

        /* Контейнер канваса */
        .canvas-container {
            padding: 20px;
            border-radius: 40px;
            margin-bottom: 20px;
        }

        .zoom-wrapper {
            overflow: auto;
            max-width: 100%;
            max-height: 65vh;
            border-radius: 24px;
            background: rgba(255,255,255,0.05);
            cursor: grab;
            position: relative;
            transition: all 0.2s;
        }

        .zoom-wrapper:active {
            cursor: grabbing;
        }

        canvas {
            display: block;
            margin: 0;
            image-rendering: crisp-edges;
            image-rendering: pixelated;
            border-radius: 20px;
            transition: transform 0.1s ease;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }

        /* Нижняя панель */
        .bottom-bar {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            gap: 16px;
            padding: 16px 24px;
            margin-top: 20px;
        }

        .user-info {
            background: rgba(255,255,255,0.15);
            backdrop-filter: blur(12px);
            padding: 8px 20px;
            border-radius: 40px;
            font-weight: 500;
            font-size: 0.95rem;
            border: 1px solid rgba(255,255,255,0.2);
        }

        .action-buttons {
            display: flex;
            gap: 12px;
        }

        .action-btn {
            background: linear-gradient(135deg, rgba(76,110,245,0.9), rgba(64,90,205,0.9));
            backdrop-filter: blur(20px);
            border: none;
            padding: 10px 28px;
            border-radius: 40px;
            color: white;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 0.9rem;
            box-shadow: 0 4px 15px rgba(76,110,245,0.3);
        }

        .action-btn.logout {
            background: linear-gradient(135deg, rgba(95,58,58,0.9), rgba(70,40,40,0.9));
            box-shadow: 0 4px 15px rgba(150,0,0,0.2);
        }

        .action-btn:hover {
            transform: translateY(-2px);
            filter: brightness(1.05);
        }

        .stats {
            background: rgba(0,0,0,0.5);
            backdrop-filter: blur(12px);
            padding: 8px 18px;
            border-radius: 40px;
            font-size: 0.8rem;
            font-weight: 500;
            letter-spacing: 0.3px;
        }

        .coords-hint {
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(12px);
            padding: 6px 16px;
            border-radius: 40px;
            font-size: 0.8rem;
            font-family: monospace;
        }

        .status-msg {
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(20px);
            border-radius: 60px;
            padding: 12px 24px;
            text-align: center;
            color: #ffd966;
            font-weight: 500;
            margin-top: 16px;
            border: 1px solid rgba(255,255,255,0.15);
        }

        /* Модальное окно iOS стиль */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.7);
            backdrop-filter: blur(24px);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }

        .modal.active {
            display: flex;
        }

        .modal-content {
            background: rgba(30, 35, 55, 0.85);
            backdrop-filter: blur(40px);
            border-radius: 48px;
            padding: 32px;
            width: 90%;
            max-width: 420px;
            color: white;
            border: 1px solid rgba(255,255,255,0.3);
            box-shadow: 0 25px 50px rgba(0,0,0,0.5);
        }

        .modal-content h2 {
            font-size: 1.8rem;
            font-weight: 600;
            margin-bottom: 24px;
            background: linear-gradient(135deg, #fff, #aaccff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }

        .modal-content input {
            width: 100%;
            padding: 14px 18px;
            margin: 12px 0;
            background: rgba(15, 20, 35, 0.8);
            border: 1px solid rgba(255,255,255,0.2);
            border-radius: 28px;
            color: white;
            font-size: 1rem;
            backdrop-filter: blur(8px);
            transition: all 0.2s;
        }

        .modal-content input:focus {
            outline: none;
            border-color: #4c6ef5;
            box-shadow: 0 0 0 3px rgba(76,110,245,0.3);
        }

        .modal-content button {
            width: 100%;
            padding: 14px;
            margin-top: 12px;
            background: linear-gradient(135deg, #4c6ef5, #3b5bdb);
            border: none;
            border-radius: 32px;
            color: white;
            font-weight: 600;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.2s;
        }

        .modal-content button:active {
            transform: scale(0.97);
        }

        .close-modal {
            background: rgba(95,58,58,0.9) !important;
        }

        .demo-note {
            font-size: 0.75rem;
            text-align: center;
            margin-top: 20px;
            color: #ffaa66;
            opacity: 0.9;
        }

        @media (max-width: 780px) {
            .control-bar, .bottom-bar { padding: 12px 16px; }
            .clan-btn { padding: 6px 14px; font-size: 0.75rem; }
            .timer-card { font-size: 1.3rem; padding: 4px 16px; }
            .zoom-btn { padding: 5px 12px; }
        }
    </style>
</head>
<body>
<div class="main-wrapper">
    <!-- Верхняя панель -->
    <div class="glass-panel control-bar">
        <div class="clan-buttons">
            <button class="clan-btn" data-clan="0">🔴 КРАСНЫЙ</button>
            <button class="clan-btn" data-clan="1">🟢 ЗЕЛЕНЫЙ</button>
            <button class="clan-btn" data-clan="2">🔵 СИНИЙ</button>
            <button class="clan-btn" data-clan="3">🟡 ЖЕЛТЫЙ</button>
            <button class="clan-btn" data-clan="4">🟣 ФИОЛЕТ</button>
        </div>
        <div class="timer-card">
            <span id="cooldownSeconds">0</span> сек
        </div>
        <div class="zoom-controls">
            <button class="zoom-btn" id="zoomOut">−</button>
            <button class="zoom-btn" id="resetZoom">◉</button>
            <button class="zoom-btn" id="zoomIn">+</button>
        </div>
    </div>

    <!-- Поле битвы -->
    <div class="glass-panel canvas-container">
        <div class="zoom-wrapper" id="zoomWrapper">
            <canvas id="battleCanvas" width="1000" height="1000"></canvas>
        </div>
    </div>

    <!-- Нижняя панель -->
    <div class="glass-panel bottom-bar">
        <div class="user-info" id="userDisplay">⚔️ Не авторизован</div>
        <div class="action-buttons">
            <button class="action-btn" id="showLoginBtn">🔐 Войти</button>
            <button class="action-btn logout" id="logoutBtn">🚪 Выйти</button>
        </div>
        <div class="stats" id="clanStatsDisplay">📊 загрузка...</div>
        <div class="coords-hint" id="coordsHint">📍 наведи на поле</div>
    </div>
    <div class="status-msg" id="statusMsg">✨ Добро пожаловать в Pixel Battle | Выбери клан и ставь пиксели</div>
</div>

<!-- Модальное окно -->
<div id="authModal" class="modal">
    <div class="modal-content">
        <h2>📧 Вход в битву</h2>
        <input type="email" id="emailInput" placeholder="example@gmail.com" autocomplete="email">
        <input type="password" id="passwordInput" placeholder="Пароль (мин. 4 символа)">
        <button id="sendCodeBtn">📨 Отправить код</button>
        <div id="codeSection" style="display:none; margin-top: 12px;">
            <input type="text" id="codeInput" placeholder="Введите код 123456">
            <button id="verifyCodeBtn">✅ Подтвердить</button>
        </div>
        <button id="closeModalBtn" class="close-modal">✖️ Закрыть</button>
        <div class="demo-note">
            ⚡ Демо-режим: любой email → код <strong>123456</strong>
        </div>
    </div>
</div>

<script>
    // ========== КОНФИГ ==========
    const SIZE = 100;
    const COOLDOWN_MS = 60000;
    const CLAN_COLORS = ["#e34d4d", "#4caf50", "#2196f3", "#ffc107", "#9c27b0"];
    const NEUTRAL_COLOR = "#FFFFFF";

    let canvasGrid = Array(SIZE).fill().map(() => Array(SIZE).fill(-1));
    let users = new Map();
    let currentUser = null;
    let pendingVerification = null;

    let currentZoom = 1;
    const MIN_ZOOM = 0.5;
    const MAX_ZOOM = 4;

    const canvas = document.getElementById('battleCanvas');
    const ctx = canvas.getContext('2d');
    const zoomWrapper = document.getElementById('zoomWrapper');
    const cooldownSpan = document.getElementById('cooldownSeconds');
    const userDisplaySpan = document.getElementById('userDisplay');
    const clanStatsDiv = document.getElementById('clanStatsDisplay');
    const statusMsgSpan = document.getElementById('statusMsg');
    const coordsHint = document.getElementById('coordsHint');
    const modal = document.getElementById('authModal');
    const emailInput = document.getElementById('emailInput');
    const passwordInput = document.getElementById('passwordInput');
    const codeSection = document.getElementById('codeSection');
    const codeInput = document.getElementById('codeInput');

    let cooldownInterval = null;
    let isDragging = false;
    let startX, startY, scrollLeft, scrollTop;

    // ========== ИНИЦИАЛИЗАЦИЯ ПОЛЯ ==========
    function ensureEmptyGrid() {
        for(let i = 0; i < SIZE; i++) {
            for(let j = 0; j < SIZE; j++) {
                if(canvasGrid[i][j] === undefined || canvasGrid[i][j] === null) {
                    canvasGrid[i][j] = -1;
                }
            }
        }
    }

    // Сохранение/загрузка
    function saveFullState() {
        const state = {
            grid: canvasGrid,
            users: Array.from(users.entries()),
            version: 5
        };
        localStorage.setItem('pixelbattle_ios', JSON.stringify(state));
        if(currentUser) {
            localStorage.setItem('pixelbattle_session_ios', JSON.stringify({email: currentUser.email}));
        } else {
            localStorage.removeItem('pixelbattle_session_ios');
        }
    }

    function loadFullState() {
        const saved = localStorage.getItem('pixelbattle_ios');
        if(saved) {
            try {
                const state = JSON.parse(saved);
                if(state.grid && state.grid.length === SIZE) canvasGrid = state.grid;
                if(state.users) users = new Map(state.users);
            } catch(e) {}
        }
        ensureEmptyGrid();
        const session = localStorage.getItem('pixelbattle_session_ios');
        if(session) {
            try {
                const {email} = JSON.parse(session);
                if(email && users.has(email)) {
                    const userData = users.get(email);
                    currentUser = {
                        email: email,
                        clan: userData.clan,
                        lastPlaceTime: userData.lastPlaceTimestamp || 0
                    };
                }
            } catch(e) {}
        }
        renderCanvas();
        updateUI();
        updateClanStats();
    }

    function renderCanvas() {
        canvas.width = SIZE * 10;
        canvas.height = SIZE * 10;
        const cellSize = 10;
        for(let i = 0; i < SIZE; i++) {
            for(let j = 0; j < SIZE; j++) {
                const clanId = canvasGrid[i][j];
                let fillColor = NEUTRAL_COLOR;
                if(clanId !== -1 && clanId >= 0 && clanId < CLAN_COLORS.length) {
                    fillColor = CLAN_COLORS[clanId];
                }
                ctx.fillStyle = fillColor;
                ctx.fillRect(j * cellSize, i * cellSize, cellSize - 0.5, cellSize - 0.5);
                ctx.strokeStyle = "rgba(200,200,220,0.3)";
                ctx.lineWidth = 0.5;
                ctx.strokeRect(j * cellSize, i * cellSize, cellSize, cellSize);
            }
        }
        applyZoom();
    }

    function applyZoom() {
        canvas.style.transform = `scale(${currentZoom})`;
        canvas.style.transformOrigin = "0 0";
    }

    function zoomIn() { if(currentZoom < MAX_ZOOM) { currentZoom += 0.25; applyZoom(); } }
    function zoomOut() { if(currentZoom > MIN_ZOOM) { currentZoom -= 0.25; applyZoom(); } }
    function resetZoom() { currentZoom = 1; applyZoom(); if(zoomWrapper) { zoomWrapper.scrollLeft = (zoomWrapper.scrollWidth - zoomWrapper.clientWidth) / 2; zoomWrapper.scrollTop = (zoomWrapper.scrollHeight - zoomWrapper.clientHeight) / 2; } }

    function getRemainingCooldown() {
        if(!currentUser || !currentUser.lastPlaceTime) return 0;
        return Math.max(0, COOLDOWN_MS - (Date.now() - currentUser.lastPlaceTime));
    }

    function canPlacePixel() { return currentUser && getRemainingCooldown() <= 0; }

    function updateUI() {
        if(currentUser) {
            const clanName = ["КРАСНЫЙ","ЗЕЛЕНЫЙ","СИНИЙ","ЖЕЛТЫЙ","ФИОЛЕТ"][currentUser.clan];
            userDisplaySpan.innerHTML = `👤 ${currentUser.email} | ${clanName} клан`;
            const left = getRemainingCooldown();
            if(left > 0) {
                cooldownSpan.innerText = Math.ceil(left/1000);
                statusMsgSpan.innerHTML = `⏳ Кулдаун: ${Math.ceil(left/1000)} сек до следующего пикселя`;
            } else {
                cooldownSpan.innerText = "0";
                statusMsgSpan.innerHTML = `✅ Готов! Нажми на любую клетку, чтобы поставить пиксель клана ${clanName}`;
            }
        } else {
            userDisplaySpan.innerHTML = `⚔️ Не авторизован`;
            cooldownSpan.innerText = "--";
            statusMsgSpan.innerHTML = `🔐 Нажми "Войти", чтобы начать битву пикселей`;
        }
        highlightActiveClan();
    }

    function placePixel(x, y) {
        if(!currentUser) { statusMsgSpan.innerHTML = "❌ Войдите в аккаунт"; return false; }
        if(!canPlacePixel()) {
            const left = getRemainingCooldown();
            statusMsgSpan.innerHTML = `❌ Кулдаун! Жди ${Math.ceil(left/1000)} сек`;
            return false;
        }
        if(x<0 || x>=SIZE || y<0 || y>=SIZE) return false;
        
        canvasGrid[x][y] = currentUser.clan;
        currentUser.lastPlaceTime = Date.now();
        if(users.has(currentUser.email)) {
            let u = users.get(currentUser.email);
            u.lastPlaceTimestamp = currentUser.lastPlaceTime;
            users.set(currentUser.email, u);
        }
        saveFullState();
        renderCanvas();
        updateClanStats();
        updateUI();
        statusMsgSpan.innerHTML = `🎨 [${x},${y}] захвачен кланом ${["🔴","🟢","🔵","🟡","🟣"][currentUser.clan]}! Следующий через 60 сек`;
        startCooldownTimer();
        return true;
    }

    function startCooldownTimer() {
        if(cooldownInterval) clearInterval(cooldownInterval);
        cooldownInterval = setInterval(() => {
            if(currentUser) {
                const left = getRemainingCooldown();
                if(left <= 0) {
                    cooldownSpan.innerText = "0";
                    if(canPlacePixel()) statusMsgSpan.innerHTML = "✨ Готов! Ставь новый пиксель!";
                    clearInterval(cooldownInterval);
                    cooldownInterval = null;
                } else {
                    cooldownSpan.innerText = Math.ceil(left/1000);
                }
            } else if(cooldownInterval) {
                clearInterval(cooldownInterval);
                cooldownInterval = null;
            }
        }, 500);
    }

    function updateClanStats() {
        const counts = [0,0,0,0,0];
        let neutral = 0;
        for(let i=0;i<SIZE;i++) {
            for(let j=0;j<SIZE;j++) {
                let val = canvasGrid[i][j];
                if(val>=0 && val<5) counts[val]++;
                else neutral++;
            }
        }
        let html = "";
        for(let c=0;c<5;c++) {
            let percent = (counts[c]/(SIZE*SIZE)*100).toFixed(1);
            html += `<span style="color:${CLAN_COLORS[c]};">● ${["Кр","Зл","Сн","Жл","Фт"][c]} ${counts[c]} (${percent}%)</span> `;
        }
        html += `⚪ пусто:${neutral}`;
        clanStatsDiv.innerHTML = "🏆 " + html;
    }

    function setUserClan(newClanId) {
        if(!currentUser) { statusMsgSpan.innerHTML = "Сначала войдите"; return false; }
        currentUser.clan = newClanId;
        if(users.has(currentUser.email)) {
            users.get(currentUser.email).clan = newClanId;
        }
        saveFullState();
        updateUI();
        statusMsgSpan.innerHTML = `🎖️ Теперь ты в клане ${["КРАСНЫЙ","ЗЕЛЕНЫЙ","СИНИЙ","ЖЕЛТЫЙ","ФИОЛЕТ"][newClanId]}!`;
        return true;
    }

    function highlightActiveClan() {
        document.querySelectorAll('.clan-btn').forEach(btn => {
            btn.classList.remove('active');
            if(currentUser && parseInt(btn.dataset.clan) === currentUser.clan) btn.classList.add('active');
        });
    }

    // Аутентификация (демо с кодом 123456)
    function sendVerificationCode(email, password) {
        pendingVerification = { email, password };
        codeSection.style.display = "block";
        statusMsgSpan.innerHTML = `📨 Код отправлен на ${email} (демо: 123456)`;
        codeInput.value = "";
    }

    function verifyCodeAndLogin() {
        if(!pendingVerification || codeInput.value.trim() !== "123456") {
            statusMsgSpan.innerHTML = "❌ Неверный код. Используйте 123456";
            return;
        }
        const { email, password } = pendingVerification;
        if(users.has(email)) {
            const existing = users.get(email);
            if(existing.passwordHash === password) {
                currentUser = { email, clan: existing.clan, lastPlaceTime: existing.lastPlaceTimestamp || 0 };
            } else { statusMsgSpan.innerHTML = "❌ Неверный пароль"; return; }
        } else {
            const newUser = { email, clan: 0, lastPlaceTimestamp: 0, passwordHash: password };
            users.set(email, newUser);
            currentUser = { email, clan: 0, lastPlaceTime: 0 };
            statusMsgSpan.innerHTML = `✨ Новый воин! ${email}, твой клан КРАСНЫЙ.`;
        }
        saveFullState();
        modal.classList.remove('active');
        codeSection.style.display = "none";
        pendingVerification = null;
        emailInput.value = passwordInput.value = "";
        updateUI();
        renderCanvas();
        updateClanStats();
        startCooldownTimer();
    }

    function logout() {
        currentUser = null;
        localStorage.removeItem('pixelbattle_session_ios');
        saveFullState();
        updateUI();
        if(cooldownInterval) clearInterval(cooldownInterval);
        cooldownInterval = null;
        cooldownSpan.innerText = "--";
        statusMsgSpan.innerHTML = "👋 Вы вышли. До встречи в битве!";
    }

    // Обработка клика по канвасу
    function handleCanvasClick(e) {
        if(!currentUser) { statusMsgSpan.innerHTML = "🔐 Войдите, чтобы ставить пиксели"; return; }
        const rect = canvas.getBoundingClientRect();
        const scaleX = canvas.width / rect.width;
        const scaleY = canvas.height / rect.height;
        let clickX = (e.clientX - rect.left) * scaleX;
        let clickY = (e.clientY - rect.top) * scaleY;
        if(clickX < 0 || clickY < 0) return;
        const cellSize = canvas.width / SIZE;
        const gridX = Math.floor(clickY / cellSize);
        const gridY = Math.floor(clickX / cellSize);
        if(gridX >= 0 && gridX < SIZE && gridY >= 0 && gridY < SIZE) placePixel(gridX, gridY);
    }

    function updateCoords(e) {
        const rect = canvas.getBoundingClientRect();
        const scaleX = canvas.width / rect.width;
        const scaleY = canvas.height / rect.height;
        let mouseX = (e.clientX - rect.left) * scaleX;
        let mouseY = (e.clientY - rect.top) * scaleY;
        if(mouseX >= 0 && mouseY >= 0 && mouseX <= canvas.width && mouseY <= canvas.height) {
            const cellSize = canvas.width / SIZE;
            const gridX = Math.floor(mouseY / cellSize);
            const gridY = Math.floor(mouseX / cellSize);
            if(gridX >= 0 && gridX < SIZE && gridY >= 0 && gridY < SIZE) coordsHint.innerHTML = `📍 [${gridX}, ${gridY}]`;
            else coordsHint.innerHTML = `📍 вне поля`;
        } else coordsHint.innerHTML = `📍 наведи на поле`;
    }

    function initDragScroll() {
        zoomWrapper.addEventListener('mousedown', (e) => {
            if(e.target === canvas || zoomWrapper.contains(e.target)) {
                isDragging = true;
                startX = e.pageX - zoomWrapper.offsetLeft;
                startY = e.pageY - zoomWrapper.offsetTop;
                scrollLeft = zoomWrapper.scrollLeft;
                scrollTop = zoomWrapper.scrollTop;
                zoomWrapper.style.cursor = 'grabbing';
            }
        });
        window.addEventListener('mousemove', (e) => {
            if(!isDragging) return;
            e.preventDefault();
            const x = e.pageX - zoomWrapper.offsetLeft;
            const y = e.pageY - zoomWrapper.offsetTop;
            zoomWrapper.scrollLeft = scrollLeft - (x - startX);
            zoomWrapper.scrollTop = scrollTop - (y - startY);
        });
        window.addEventListener('mouseup', () => { isDragging = false; zoomWrapper.style.cursor = 'grab'; });
    }

    function init() {
        loadFullState();
        startCooldownTimer();
        canvas.addEventListener('click', handleCanvasClick);
        canvas.addEventListener('mousemove', updateCoords);
        document.querySelectorAll('.clan-btn').forEach(btn => btn.addEventListener('click', () => setUserClan(parseInt(btn.dataset.clan))));
        document.getElementById('showLoginBtn').addEventListener('click', () => { modal.classList.add('active'); codeSection.style.display = "none"; pendingVerification = null; });
        document.getElementById('closeModalBtn').addEventListener('click', () => modal.classList.remove('active'));
        document.getElementById('sendCodeBtn').addEventListener('click', () => {
            const email = emailInput.value.trim();
            const pwd = passwordInput.value.trim();
            if(!email || !pwd || pwd.length < 4) { statusMsgSpan.innerHTML = "❌ Введите email и пароль (мин 4 символа)"; return; }
            if(!email.includes('@')) { statusMsgSpan.innerHTML = "❌ Введите корректный email"; return; }
            sendVerificationCode(email, pwd);
        });
        document.getElementById('verifyCodeBtn').addEventListener('click', verifyCodeAndLogin);
        document.getElementById('logoutBtn').addEventListener('click', logout);
        document.getElementById('zoomIn').addEventListener('click', zoomIn);
        document.getElementById('zoomOut').addEventListener('click', zoomOut);
        document.getElementById('resetZoom').addEventListener('click', resetZoom);
        initDragScroll();
        zoomWrapper.style.cursor = 'grab';
        setInterval(() => updateClanStats(), 3000);
        resetZoom();
    }
    init();
</script>
</body>
</html>
