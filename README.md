<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Notcoin - Tap to Earn</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #1a1a2a 0%, #2d1b69 100%);
            color: white;
            overflow: hidden;
            height: 100vh;
        }

        .container {
            max-width: 400px;
            margin: 0 auto;
            height: 100vh;
            display: flex;
            flex-direction: column;
            position: relative;
        }

        /* Header */
        .header {
            padding: 20px 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(10px);
        }

        .balance-container {
            text-align: center;
        }

        .balance-label {
            font-size: 14px;
            opacity: 0.8;
            margin-bottom: 4px;
        }

        .balance {
            font-size: 24px;
            font-weight: bold;
            background: linear-gradient(45deg, #ffd700, #ffed4e);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .boost-container {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .boost {
            background: rgba(255, 255, 255, 0.1);
            padding: 8px 12px;
            border-radius: 20px;
            font-size: 14px;
        }

        /* Main Game Area */
        .game-area {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        .coin {
            width: 200px;
            height: 200px;
            background: radial-gradient(circle at 30% 30%, #ffd700, #ff6b00);
            border-radius: 50%;
            border: 8px solid #ffed4e;
            box-shadow: 
                0 0 50px rgba(255, 215, 0, 0.5),
                inset 0 0 20px rgba(255, 255, 255, 0.3);
            cursor: pointer;
            position: relative;
            transition: all 0.1s ease;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .coin:active {
            transform: scale(0.95);
            box-shadow: 
                0 0 30px rgba(255, 215, 0, 0.3),
                inset 0 0 15px rgba(255, 255, 255, 0.2);
        }

        .coin-inner {
            width: 80%;
            height: 80%;
            background: radial-gradient(circle at 40% 40%, #ffed4e, #ffa500);
            border-radius: 50%;
            border: 4px solid #ffd700;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 48px;
            font-weight: bold;
            color: #8b6914;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        /* Tap Effects */
        .tap-effect {
            position: absolute;
            width: 60px;
            height: 60px;
            pointer-events: none;
            background: radial-gradient(circle, rgba(255, 215, 0, 0.8) 0%, transparent 70%);
            border-radius: 50%;
            animation: tapAnimation 0.6s ease-out forwards;
            z-index: 10;
        }

        @keyframes tapAnimation {
            0% {
                transform: scale(0.5);
                opacity: 1;
            }
            100% {
                transform: scale(2);
                opacity: 0;
            }
        }

        .coin-particle {
            position: absolute;
            width: 20px;
            height: 20px;
            background: radial-gradient(circle, #ffd700, #ffa500);
            border-radius: 50%;
            pointer-events: none;
            animation: particleFloat 1s ease-out forwards;
            z-index: 5;
        }

        @keyframes particleFloat {
            0% {
                transform: translate(0, 0) scale(1);
                opacity: 1;
            }
            100% {
                transform: translate(var(--tx), var(--ty)) scale(0);
                opacity: 0;
            }
        }

        /* Stats */
        .stats {
            display: flex;
            justify-content: space-around;
            padding: 20px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 20px 20px 0 0;
            margin-top: auto;
            width: 100%;
        }

        .stat-item {
            text-align: center;
        }

        .stat-value {
            font-size: 18px;
            font-weight: bold;
            color: #ffd700;
        }

        .stat-label {
            font-size: 12px;
            opacity: 0.7;
            margin-top: 4px;
        }

        /* Bottom Navigation */
        .bottom-nav {
            display: flex;
            justify-content: space-around;
            padding: 16px;
            background: rgba(0, 0, 0, 0.4);
            backdrop-filter: blur(10px);
        }

        .nav-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 6px;
            padding: 8px 16px;
            border-radius: 12px;
            transition: background 0.3s ease;
            cursor: pointer;
        }

        .nav-item.active {
            background: rgba(255, 215, 0, 0.2);
        }

        .nav-icon {
            font-size: 20px;
        }

        .nav-label {
            font-size: 12px;
        }

        /* Energy Bar */
        .energy-bar {
            position: absolute;
            top: 20px;
            right: 20px;
            display: flex;
            align-items: center;
            gap: 8px;
            background: rgba(0, 0, 0, 0.5);
            padding: 6px 12px;
            border-radius: 20px;
        }

        .energy-fill {
            width: 60px;
            height: 6px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 3px;
            overflow: hidden;
        }

        .energy-progress {
            height: 100%;
            background: linear-gradient(90deg, #00ff88, #00ccff);
            border-radius: 3px;
            transition: width 0.3s ease;
        }

        /* Boost Popup */
        .boost-popup {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0);
            background: rgba(0, 0, 0, 0.9);
            padding: 30px;
            border-radius: 20px;
            text-align: center;
            border: 2px solid #ffd700;
            transition: transform 0.3s ease;
            z-index: 100;
        }

        .boost-popup.show {
            transform: translate(-50%, -50%) scale(1);
        }

        .boost-title {
            font-size: 24px;
            margin-bottom: 20px;
            color: #ffd700;
        }

        .boost-close {
            margin-top: 20px;
            padding: 10px 20px;
            background: #ffd700;
            border: none;
            border-radius: 10px;
            color: #000;
            font-weight: bold;
            cursor: pointer;
        }

        /* Achievement Notification */
        .achievement {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%) translateY(-100px);
            background: linear-gradient(45deg, #ffd700, #ffed4e);
            color: #000;
            padding: 12px 24px;
            border-radius: 25px;
            font-weight: bold;
            box-shadow: 0 4px 20px rgba(255, 215, 0, 0.5);
            transition: transform 0.5s ease;
            z-index: 1000;
        }

        .achievement.show {
            transform: translateX(-50%) translateY(0);
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Header -->
        <div class="header">
            <div class="balance-container">
                <div class="balance-label">BALANCE</div>
                <div class="balance" id="balance">0</div>
            </div>
            <div class="boost-container">
                <div class="boost">⚡ 1.0x</div>
            </div>
        </div>

        <!-- Game Area -->
        <div class="game-area">
            <div class="energy-bar">
                <div class="energy-fill">
                    <div class="energy-progress" id="energy-progress" style="width: 100%;"></div>
                </div>
                <div>⚡</div>
            </div>

            <div class="coin" id="coin">
                <div class="coin-inner">N</div>
            </div>

            <div class="stats">
                <div class="stat-item">
                    <div class="stat-value" id="total-taps">0</div>
                    <div class="stat-label">TOTAL TAPS</div>
                </div>
                <div class="stat-item">
                    <div class="stat-value" id="taps-per-second">1.0</div>
                    <div class="stat-label">TAPS/SEC</div>
                </div>
                <div class="stat-item">
                    <div class="stat-value" id="energy">100/100</div>
                    <div class="stat-label">ENERGY</div>
                </div>
            </div>
        </div>

        <!-- Bottom Navigation -->
        <div class="bottom-nav">
            <div class="nav-item active">
                <div class="nav-icon">🪙</div>
                <div class="nav-label">Mine</div>
            </div>
            <div class="nav-item">
                <div class="nav-icon">👥</div>
                <div class="nav-label">Squad</div>
            </div>
            <div class="nav-item">
                <div class="nav-icon">🏆</div>
                <div class="nav-label">Tasks</div>
            </div>
            <div class="nav-item">
                <div class="nav-icon">⚡</div>
                <div class="nav-label">Boost</div>
            </div>
        </div>

        <!-- Boost Popup -->
        <div class="boost-popup" id="boost-popup">
            <div class="boost-title">⚡ BOOST ACTIVATED!</div>
            <p>Your tap power increased to 2.0x for 30 seconds!</p>
            <button class="boost-close" onclick="closeBoost()">OK</button>
        </div>

        <!-- Achievement -->
        <div class="achievement" id="achievement">
            🎉 Achievement Unlocked: First 100 Taps!
        </div>
    </div>

    <script>
        // Game State
        let balance = 0;
        let totalTaps = 0;
        let tapPower = 1.0;
        let energy = 100;
        let maxEnergy = 100;
        let energyRechargeRate = 1;
        let lastEnergyUpdate = Date.now();
        let isBoostActive = false;
        let boostEndTime = 0;

        // DOM Elements
        const coin = document.getElementById('coin');
        const balanceElement = document.getElementById('balance');
        const totalTapsElement = document.getElementById('total-taps');
        const tapsPerSecondElement = document.getElementById('taps-per-second');
        const energyElement = document.getElementById('energy');
        const energyProgress = document.getElementById('energy-progress');
        const boostPopup = document.getElementById('boost-popup');
        const achievement = document.getElementById('achievement');

        // Format number with commas
        function formatNumber(num) {
            return num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        }

        // Update UI
        function updateUI() {
            balanceElement.textContent = formatNumber(Math.floor(balance));
            totalTapsElement.textContent = formatNumber(totalTaps);
            tapsPerSecondElement.textContent = tapPower.toFixed(1);
            energyElement.textContent = `${Math.floor(energy)}/${maxEnergy}`;
            energyProgress.style.width = `${(energy / maxEnergy) * 100}%`;
        }

        // Create tap effect
        function createTapEffect(x, y) {
            const effect = document.createElement('div');
            effect.className = 'tap-effect';
            effect.style.left = `${x - 30}px`;
            effect.style.top = `${y - 30}px`;
            document.querySelector('.game-area').appendChild(effect);
            
            setTimeout(() => {
                effect.remove();
            }, 600);
        }

        // Create coin particles
        function createCoinParticles(x, y, count = 5) {
            for (let i = 0; i < count; i++) {
                const particle = document.createElement('div');
                particle.className = 'coin-particle';
                particle.style.left = `${x}px`;
                particle.style.top = `${y}px`;
                
                // Random direction and distance
                const angle = Math.random() * Math.PI * 2;
                const distance = 50 + Math.random() * 100;
                const tx = Math.cos(angle) * distance;
                const ty = Math.sin(angle) * distance;
                
                particle.style.setProperty('--tx', `${tx}px`);
                particle.style.setProperty('--ty', `${ty}px`);
                
                document.querySelector('.game-area').appendChild(particle);
                
                setTimeout(() => {
                    particle.remove();
                }, 1000);
            }
        }

        // Handle coin tap
        function handleTap(event) {
            const rect = coin.getBoundingClientRect();
            const x = event.clientX - rect.left;
            const y = event.clientY - rect.top;

            if (energy >= 1) {
                // Deduct energy
                energy = Math.max(0, energy - 1);
                
                // Add to balance
                balance += tapPower;
                totalTaps++;
                
                // Create effects
                createTapEffect(x, y);
                createCoinParticles(x, y, 3);
                
                // Check achievements
                checkAchievements();
                
                // Update UI
                updateUI();
                
                // Coin animation
                coin.style.transform = 'scale(0.95)';
                setTimeout(() => {
                    coin.style.transform = 'scale(1)';
                }, 100);
            }
        }

        // Check for achievements
        function checkAchievements() {
            if (totalTaps === 100) {
                showAchievement("First 100 Taps! 🎉");
            } else if (totalTaps === 500) {
                showAchievement("500 Taps! ⚡");
            } else if (totalTaps === 1000) {
                showAchievement("Tap Master! 🏆");
            }
        }

        // Show achievement
        function showAchievement(text) {
            achievement.textContent = `🎉 ${text}`;
            achievement.classList.add('show');
            setTimeout(() => {
                achievement.classList.remove('show');
            }, 3000);
        }

        // Energy recharge system
        function updateEnergy() {
            const now = Date.now();
            const deltaTime = (now - lastEnergyUpdate) / 1000;
            
            if (energy < maxEnergy) {
                energy = Math.min(maxEnergy, energy + energyRechargeRate * deltaTime);
                updateUI();
            }
            
            lastEnergyUpdate = now;
        }

        // Boost system
        function activateBoost() {
            if (!isBoostActive) {
                isBoostActive = true;
                tapPower = 2.0;
                boostEndTime = Date.now() + 30000; // 30 seconds
                boostPopup.classList.add('show');
                
                setTimeout(() => {
                    isBoostActive = false;
                    tapPower = 1.0;
                    updateUI();
                }, 30000);
            }
        }

        function closeBoost() {
            boostPopup.classList.remove('show');
        }

        // Auto-clicker simulation (for demo)
        function simulateAutoClicker() {
            if (Math.random() < 0.1 && energy > 10) { // 10% chance every frame
                const rect = coin.getBoundingClientRect();
                const x = rect.left + Math.random() * rect.width;
                const y = rect.top + Math.random() * rect.height;
                
                handleTap({ clientX: x, clientY: y });
            }
        }

        // Event Listeners
        coin.addEventListener('click', handleTap);
        
        // Touch support for mobile
        coin.addEventListener('touchstart', (e) => {
            e.preventDefault();
            handleTap(e.touches[0]);
        });

        // Boost button in nav
        document.querySelectorAll('.nav-item')[3].addEventListener('click', activateBoost);

        // Game loop
        function gameLoop() {
            updateEnergy();
            simulateAutoClicker();
            
            // Update boost timer
            if (isBoostActive && Date.now() >= boostEndTime) {
                isBoostActive = false;
                tapPower = 1.0;
            }
            
            requestAnimationFrame(gameLoop);
        }

        // Initialize game
        updateUI();
        gameLoop();

        // Demo: Auto-add some coins after delay
        setTimeout(() => {
            balance += 1000;
            updateUI();
        }, 2000);
    </script>
</body>
</html>
