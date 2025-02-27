---
layout: default
title: Green Hat Gravity.
description: Game
url: /projects/ghg/
---
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Green Hat Gravity</title>
    <link rel="icon" type="image/png" href="favicon.png">
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@700&display=swap" rel="stylesheet">
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: #000 !important;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        canvas {
            display: block;
            border: 2px solid #ff4444;
            background: #000;
        }
        #ui {
            position: absolute;
            color: white;
            font-family: 'Orbitron', Arial, sans-serif;
            font-size: 41.4px;
            text-align: center;
            width: 100%;
            top: 10px;
            pointer-events: none;
        }
        #howToPlay, #gameOver, #pauseScreen {
            display: none;
            position: absolute;
            color: white;
            font-family: 'Orbitron', 'Minecraftia', Arial, sans-serif;
            text-align: center;
            width: 100%;
            top: 40%;
            transform: translateY(-50%);
            background: rgba(0, 0, 0, 0.9);
            padding: 20px;
            pointer-events: auto;
        }
        #howToPlay h1, #gameOver h1, #pauseScreen h1 {
            font-size: 36px;
            margin: 10px 0;
        }
        #howToPlay p, #gameOver p, #pauseScreen p {
            font-size: 20px;
            margin: 8px 0;
        }
        #howToPlay p.mute-line {
            margin-bottom: 24px;
        }
        #gameOver p.high-score {
            margin-top: 24px;
        }
        button {
            padding: 10px 20px;
            font-size: 18px;
            cursor: pointer;
            margin: 5px;
            background: #555;
            color: white;
            border: 2px solid #fff;
            display: block;
            margin-left: auto;
            margin-right: auto;
            transition: background 0.2s;
        }
        button:hover {
            background: #777;
        }
        #countdown {
            position: absolute;
            color: white;
            font-family: Arial, sans-serif;
            font-size: 48px;
            width: 100%;
            text-align: center;
            top: 50%;
            transform: translateY(-50%);
            display: none;
            pointer-events: none;
        }
        .title {
            font-family: 'Orbitron', 'Minecraftia', Arial, sans-serif;
            font-size: 48px;
            color: #fff;
            text-shadow: 4px 4px #000, -4px -4px #000, 4px -4px #000, -4px 4px #000;
            position: relative;
            margin-bottom: 40px;
            background: linear-gradient(45deg, #1a0033, #0a0015);
            padding: 10px 20px;
            border-radius: 10px;
            animation: glow 2s infinite alternate;
        }
        .title-hat {
            position: absolute;
            width: 48px;
            height: 16px;
            top: -35px;
            left: 35%;
            transform: translateX(-50%) rotate(-5deg);
            animation: bounce 2s infinite;
        }
        .title-hat::before {
            content: '';
            position: absolute;
            width: 48px;
            height: 12px;
            background: #00ff00;
            top: 0;
            left: 0;
            border: 2px solid #009900;
        }
        .title-hat::after {
            content: '';
            position: absolute;
            width: 16px;
            height: 8px;
            background: #009900;
            top: 12px;
            left: 16px;
        }
        .title-planet {
            position: absolute;
            width: 24px;
            height: 24px;
            background: #deb887;
            border-radius: 50%;
            top: -40px;
            left: 70%;
            box-shadow: inset -5px -5px rgba(0, 0, 0, 0.3);
            animation: orbit 5s infinite linear;
        }
        @keyframes bounce {
            0%, 100% { transform: translateX(-50%) translateY(0) rotate(-5deg); }
            50% { transform: translateX(-50%) translateY(-5px) rotate(-5deg); }
        }
        @keyframes glow {
            0% { text-shadow: 4px 4px #000, -4px -4px #000, 4px -4px #000, -4px 4px #000; }
            100% { text-shadow: 4px 4px #00ff00, -4px -4px #00ff00, 4px -4px #00ff00, -4px 4px #00ff00; }
        }
        @keyframes orbit {
            0% { transform: translateX(0) translateY(0); }
            25% { transform: translateX(20px) translateY(-20px); }
            50% { transform: translateX(0) translateY(-40px); }
            75% { transform: translateX(-20px) translateY(-20px); }
            100% { transform: translateX(0) translateY(0); }
        }
    </style>
</head>
<body>
    <div id="ui"><span id="score">0</span></div>
    <div id="howToPlay">
        <div class="title">Green Hat Gravity<div class="title-hat"></div><div class="title-planet"></div></div>
        <h1>How to Play</h1>
        <p>A: Move Left</p>
        <p>D: Move Right</p>
        <p>W: Jump (Double-press for big jump)</p>
        <p>Space: Flip Gravity (3x limit, then cooldown)</p>
        <p>P or ESC: Pause/Resume</p>
        <p class="mute-line">M: Toggle Mute</p>
        <p>Jump and flip gravity to navigate platforms and collect orbs for 10 points each!</p>
        <button onclick="startGame()">Start</button>
        <button onclick="toggleFullScreen()">Full Screen</button>
    </div>
    <div id="pauseScreen">
        <div class="title">Green Hat Gravity<div class="title-hat"></div><div class="title-planet"></div></div>
        <h1>Paused</h1>
        <p>Jumps Made: <span id="pauseJumps">0</span></p>
        <p>Gravity Flips: <span id="pauseFlips">0</span></p>
        <p>Platforms Landed: <span id="pausePlatforms">0</span></p>
        <p>Orbs Collected: <span id="pauseOrbs">0%</span></p>
        <p>Total Points: <span id="pausePoints">0</span></p>
        <button onclick="resumeGame()">Resume</button>
    </div>
    <div id="gameOver">
        <div class="title">Green Hat Gravity<div class="title-hat"></div><div class="title-planet"></div></div>
        <h1>Game Over</h1>
        <p>Jumps Made: <span id="endJumps">0</span></p>
        <p>Gravity Flips: <span id="endFlips">0</span></p>
        <p>Platforms Landed: <span id="endPlatforms">0</span></p>
        <p>Orbs Collected: <span id="endOrbs">0%</span></p>
        <p>Total Points: <span id="finalScore">0</span></p>
        <p class="high-score">High Score: <span id="highScore">0</span></p>
        <button onclick="resetGame()">Retry</button>
        <button onclick="shareScore()">Share Score</button>
        <button onclick="backToMenu()">Back to Menu</button>
    </div>
    <div id="countdown"></div>
    <canvas id="gameCanvas"></canvas>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = 1000;
        canvas.height = 800;

        const ui = document.getElementById('ui');
        const scoreDisplay = document.getElementById('score');
        const howToPlayScreen = document.getElementById('howToPlay');
        const pauseScreen = document.getElementById('pauseScreen');
        const gameOverScreen = document.getElementById('gameOver');
        const finalScoreDisplay = document.getElementById('finalScore');
        const highScoreDisplay = document.getElementById('highScore');
        const countdownDisplay = document.getElementById('countdown');

        let game = {
            score: 0,
            gravity: 0.2,
            isGravityFlipped: false,
            speed: 1,
            gameOver: false,
            started: false,
            paused: false,
            pausing: false,
            flipCount: 0,
            flipCooldown: 0,
            maxFlips: 3,
            muted: false,
            jumpsMade: 0,
            gravityFlips: 0,
            platformsLanded: 0,
            lastLandedPlatform: null,
            orbsSpawned: 0,
            orbsCollected: 0,
            exitAnimation: false,
            exitFrames: 0,
            highScore: localStorage.getItem('highScore') ? parseInt(localStorage.getItem('highScore')) : 0,
            zone: 1,
            lastSpeedIncreaseScore: 0, // Track last speed increase
            invincible: false // Cheat code invincibility
        };

        const player = {
            x: 100,
            y: 0,
            width: 32,
            height: 32,
            vx: 0,
            vy: 0,
            moveSpeed: 4,
            jumpStrength: -6,
            doubleJumpStrength: -10,
            grounded: false,
            spinAngle: 0,
            spinning: false,
            spinFrames: 15,
            jumping: false,
            jumpFrames: 12,
            doubleJumpCharge: 0,
            lastWPress: 0,
            doubleJumpWindow: 200
        };

        let platforms = [];
        let orbs = [];
        const minPlatformWidth = 100;
        const maxPlatformWidth = 300;
        const spikeHeight = 20;

        const bgLayers = {
            starsSlow: [],
            starsFast: [],
            nebula: { x: 0, speed: 0.5 },
            planetBrown: { x: canvas.width, speed: 0.75, radius: 120, color: '#8b4513', craters: true },
            planetPurple: { x: canvas.width * 1.5, speed: 0.6, radius: 80, color: '#9932cc', craters: true },
            planetIce: { x: canvas.width * 2, speed: 0.9, radius: 50, color: '#87ceeb', spots: true },
            planetRinged: { x: canvas.width * 2.5, speed: 0.7, radius: 100, color: '#deb887', ringColor: '#cccccc' },
            startPlanetBrown: { x: 900, y: 700, speed: 0.3, radius: 40, color: '#deb887', ringColor: '#cccccc' },
            startPlanetBlue: { x: 100, y: 100, speed: 0.3, radius: 40, color: '#4682b4', ringColor: '#cccccc' },
            asteroids: [],
            rocketShip: { x: canvas.width, y: canvas.height / 2, speed: 0.5, width: 40, height: 20 }
        };

        function initStars() {
            bgLayers.starsSlow = [];
            bgLayers.starsFast = [];
            for (let i = 0; i < 50; i++) {
                bgLayers.starsSlow.push({
                    x: Math.random() * canvas.width * 2,
                    y: Math.random() * canvas.height,
                    size: 1 + Math.random(),
                    opacity: 0.3 + Math.random() * 0.3,
                    speed: 0.25
                });
            }
            for (let i = 0; i < 20; i++) {
                bgLayers.starsFast.push({
                    x: Math.random() * canvas.width * 2,
                    y: Math.random() * canvas.height,
                    size: 1 + Math.random() * 0.5,
                    opacity: 0.4 + Math.random() * 0.2,
                    speed: 0.4
                });
            }
            bgLayers.asteroids = [];
            for (let i = 0; i < 10; i++) {
                bgLayers.asteroids.push({
                    x: Math.random() * canvas.width * 2,
                    y: Math.random() * canvas.height,
                    radius: 10 + Math.random() * 20,
                    speed: 0.3 + Math.random() * 0.2
                });
            }
        }

        function initGame(generatePlatforms = true) {
            platforms = [];
            orbs = [];
            initStars();
            if (generatePlatforms) {
                let initialX = 100;
                for (let i = 0; i < 6; i++) {
                    generatePlatform(initialX, i % 2 === 0);
                    initialX += 200 + Math.random() * 100;
                }
                player.x = platforms[0].x + platforms[0].width / 2 - player.width / 2;
                player.y = platforms[0].y - player.height;
            } else {
                player.x = 100;
                player.y = canvas.height - player.height;
            }
            player.vx = 0;
            player.vy = 0;
            player.grounded = true;
            player.spinAngle = 0;
            player.spinning = false;
            player.jumping = false;
            player.doubleJumpCharge = 100;
            game.isGravityFlipped = false;
            game.score = 0;
            game.speed = 1;
            game.flipCount = 0;
            game.flipCooldown = 0;
            game.jumpsMade = 0;
            game.gravityFlips = 0;
            game.platformsLanded = 0;
            game.lastLandedPlatform = null;
            game.orbsSpawned = 0;
            game.orbsCollected = 0;
            game.exitAnimation = false;
            game.pausing = false;
            game.zone = 1;
            game.lastSpeedIncreaseScore = 0;
            game.invincible = false;
            scoreDisplay.textContent = '0';
            highScoreDisplay.textContent = game.highScore;
        }

        let muteButton = { x: canvas.width - 80, y: 10, width: 30, height: 30 };
        let pauseButton = { x: canvas.width - 40, y: 10, width: 30, height: 30 };

        function generatePlatform(x, isUpper = false) {
            let yRange = canvas.height / 3;
            let y = isUpper ? 50 + Math.random() * yRange : (canvas.height - 50 - yRange) + Math.random() * yRange;
            let width = minPlatformWidth + Math.random() * (maxPlatformWidth - minPlatformWidth);
            platforms.push({ x, y, width, height: 20, isUpper });
            if (Math.random() < 0.5) {
                let orbY = y < 100 ? y + 20 : y - (isUpper ? 40 : 20);
                orbs.push({ x: x + width / 2 - 6, y: orbY, width: 12, height: 12 });
                game.orbsSpawned++;
            }
        }

        const keys = {};
        document.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            const now = Date.now();
            if (e.code === 'Space' && game.started && !game.gameOver && !game.paused && !player.spinning && game.flipCooldown <= 0) {
                if (game.flipCount < game.maxFlips) {
                    game.isGravityFlipped = !game.isGravityFlipped;
                    player.vy = 0;
                    player.spinning = true;
                    player.spinAngle = 0;
                    game.flipCount++;
                    game.gravityFlips++;
                    if (game.flipCount === game.maxFlips) game.flipCooldown = 60;
                }
            }
            if (e.code === 'KeyW' && game.started && !game.gameOver && !game.paused) {
                if (player.grounded) {
                    player.vy = game.isGravityFlipped ? -player.jumpStrength : player.jumpStrength;
                    player.grounded = false;
                    player.jumping = true;
                    player.jumpFrames = 12;
                    player.lastWPress = now;
                    game.jumpsMade++;
                } else if (now - player.lastWPress < player.doubleJumpWindow && player.doubleJumpCharge >= 100) {
                    player.vy = game.isGravityFlipped ? -player.doubleJumpStrength : player.doubleJumpStrength;
                    player.jumping = true;
                    player.jumpFrames = 12;
                    player.doubleJumpCharge = 0;
                    game.jumpsMade++;
                }
            }
            if (e.code === 'KeyM') {
                toggleMute();
            }
            if ((e.code === 'KeyP' || e.code === 'Escape') && game.started && !game.gameOver && !game.pausing) {
                if (!game.paused) {
                    pauseGame();
                } else {
                    resumeGame();
                }
            }
            if (e.code === 'KeyR' && game.started && !game.gameOver && !game.paused) {
                if (!game.invincible) {
                    if (confirm('Activate invincibility cheat?')) {
                        game.invincible = true;
                        console.log('Invincibility activated');
                    }
                } else {
                    game.invincible = false;
                    console.log('Invincibility deactivated');
                }
            }
        });
        document.addEventListener('keyup', (e) => keys[e.code] = false);

        function handleClick(e) {
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width / rect.width;
            const scaleY = canvas.height / rect.height;
            const x = (e.clientX - rect.left) * scaleX;
            const y = (e.clientY - rect.top) * scaleY;

            if (x >= muteButton.x && x <= muteButton.x + muteButton.width &&
                y >= muteButton.y && y <= muteButton.y + muteButton.height) {
                toggleMute();
                return;
            }

            if (x >= pauseButton.x && x <= pauseButton.x + pauseButton.width &&
                y >= pauseButton.y && y <= pauseButton.y + pauseButton.height &&
                game.started && !game.gameOver && !game.pausing) {
                if (!game.paused) {
                    pauseGame();
                } else {
                    resumeGame();
                }
                return;
            }

            if (game.paused && !game.pausing && 
                x >= canvas.width / 2 - 50 && x <= canvas.width / 2 + 50 &&
                y >= canvas.height / 2 + 50 && y <= canvas.height / 2 + 90) {
                resumeGame();
                return;
            }

            if (!game.started && 
                x >= canvas.width / 2 - 100 && x <= canvas.width / 2 + 100 &&
                y >= canvas.height / 2 + 20 && y <= canvas.height / 2 + 60) {
                startGame();
                return;
            }

            if (!game.started && 
                x >= canvas.width / 2 - 100 && x <= canvas.width / 2 + 100 &&
                y >= canvas.height / 2 + 70 && y <= canvas.height / 2 + 110) {
                toggleFullScreen();
                return;
            }

            if (game.gameOver && 
                x >= canvas.width / 2 - 100 && x <= canvas.width / 2 + 100 &&
                y >= canvas.height / 2 + 50 && y <= canvas.height / 2 + 90) {
                resetGame();
                return;
            }

            if (game.gameOver && 
                x >= canvas.width / 2 - 100 && x <= canvas.width / 2 + 100 &&
                y >= canvas.height / 2 + 100 && y <= canvas.height / 2 + 140) {
                shareScore();
                return;
            }

            if (game.gameOver && 
                x >= canvas.width / 2 - 100 && x <= canvas.width / 2 + 100 &&
                y >= canvas.height / 2 + 150 && y <= canvas.height / 2 + 190) {
                backToMenu();
                return;
            }
        }
        canvas.addEventListener('click', handleClick);

        function toggleFullScreen() {
            if (!document.fullscreenElement && document.fullscreenEnabled) {
                document.documentElement.requestFullscreen().catch(err => {
                    console.error(`Error enabling full-screen: ${err.message}`);
                });
            } else if (document.fullscreenElement) {
                document.exitFullscreen().catch(err => {
                    console.error(`Error exiting full-screen: ${err.message}`);
                });
            }
            if (!game.started && !game.gameOver) {
                howToPlayScreen.style.display = 'block';
            }
        }

        function toggleMute() {
            game.muted = !game.muted;
            draw();
        }

        function pauseGame() {
            game.paused = true;
            game.pausing = true;
            pauseScreen.style.display = 'block';
            document.getElementById('pauseJumps').textContent = game.jumpsMade;
            document.getElementById('pauseFlips').textContent = game.gravityFlips;
            document.getElementById('pausePlatforms').textContent = game.platformsLanded;
            document.getElementById('pauseOrbs').textContent = game.orbsSpawned > 0 ? Math.round((game.orbsCollected / game.orbsSpawned) * 100) + '%' : '0%';
            document.getElementById('pausePoints').textContent = game.score;
            player.vx = 0;
            player.vy = 0;
        }

        function resumeGame() {
            pauseScreen.style.display = 'none';
            countdownDisplay.style.display = 'block';
            let countdown = 3;
            countdownDisplay.textContent = countdown;
            const interval = setInterval(() => {
                countdown--;
                if (countdown > 0) {
                    countdownDisplay.textContent = countdown;
                } else {
                    clearInterval(interval);
                    countdownDisplay.style.display = 'none';
                    game.paused = false;
                    game.pausing = false;
                    update();
                }
            }, 1000);
        }

        function shareScore() {
            const stats = `Green Hat Gravity Score: ${game.score}\nHigh Score: ${game.highScore}\nJumps: ${game.jumpsMade}\nFlips: ${game.gravityFlips}\nPlatforms: ${game.platformsLanded}\nOrbs: ${game.orbsSpawned > 0 ? Math.round((game.orbsCollected / game.orbsSpawned) * 100) : 0}%`;
            if (navigator.clipboard) {
                navigator.clipboard.writeText(stats).then(() => {
                    alert('Score copied to clipboard! Paste it to share.');
                }).catch(err => {
                    console.error('Clipboard error:', err);
                    alert('Copy failed. Here’s your score:\n' + stats);
                });
            } else {
                prompt('Copy this to share your score:', stats);
            }
        }

        function updateHighScore() {
            if (game.score > game.highScore) {
                game.highScore = game.score;
                localStorage.setItem('highScore', game.highScore);
                highScoreDisplay.textContent = game.highScore;
            }
        }

        function backToMenu() {
            game.gameOver = false;
            game.started = false;
            game.paused = false;
            game.pausing = false;
            gameOverScreen.style.display = 'none';
            document.body.style.background = '#000';
            canvas.style.background = '#000';
            howToPlayScreen.style.display = 'block';
            initGame(false);
            draw();
        }

        function collides(a, b) {
            return a.x < b.x + b.width && a.x + a.width > b.x && a.y < b.y + b.height && a.y + a.height > b.y;
        }

        function update() {
            if (!game.started || game.gameOver || game.paused) return;

            if (!game.exitAnimation) {
                player.vx = 0;
                if (keys['KeyA']) player.vx = -player.moveSpeed;
                if (keys['KeyD']) player.vx = player.moveSpeed;
                player.x += player.vx;

                if (player.x < 60) player.x = 60;
                if (player.x + player.width > canvas.width - 60) player.x = canvas.width - 60 - player.width;

                player.vy += game.isGravityFlipped ? -game.gravity : game.gravity;
                player.y += player.vy;
                player.grounded = false;
            }

            if (player.spinning) {
                player.spinAngle += Math.PI / 7.5;
                player.spinFrames--;
                if (player.spinFrames <= 0) {
                    player.spinning = false;
                    player.spinAngle = 0;
                    player.spinFrames = 15;
                }
            }

            if (player.jumping) {
                player.jumpFrames--;
                if (player.jumpFrames <= 0) {
                    player.jumping = false;
                    player.jumpFrames = 12;
                }
            }

            if (player.doubleJumpCharge < 100) {
                player.doubleJumpCharge += 0.5;
            }

            if (game.flipCooldown > 0) {
                game.flipCooldown--;
                if (game.flipCooldown === 0) game.flipCount = 0;
            }

            platforms.forEach(p => {
                if (collides(player, p)) {
                    if (player.vy > 0 && !game.isGravityFlipped && player.y + player.height - player.vy <= p.y) {
                        player.y = p.y - player.height;
                        player.vy = 0;
                        player.grounded = true;
                        player.jumping = false;
                        if (game.lastLandedPlatform !== p) {
                            game.platformsLanded++;
                            game.lastLandedPlatform = p;
                        }
                        if (game.flipCount > 0 && game.flipCooldown === 0) game.flipCount = 0;
                    } else if (player.vy < 0 && game.isGravityFlipped && player.y - player.vy >= p.y + p.height) {
                        player.y = p.y + p.height;
                        player.vy = 0;
                        player.grounded = true;
                        player.jumping = false;
                        if (game.lastLandedPlatform !== p) {
                            game.platformsLanded++;
                            game.lastLandedPlatform = p;
                        }
                        if (game.flipCount > 0 && game.flipCooldown === 0) game.flipCount = 0;
                    }
                }
            });

            orbs = orbs.filter(o => {
                if (collides(player, o)) {
                    game.score += 10;
                    game.orbsCollected++;
                    scoreDisplay.textContent = game.score;
                    return false;
                }
                return true;
            });

            if (!game.isGravityFlipped && player.y + player.height > canvas.height - spikeHeight && !game.invincible) {
                game.gameOver = true;
                updateHighScore();
                gameOverScreen.style.display = 'block';
                finalScoreDisplay.textContent = game.score;
                document.getElementById('endJumps').textContent = game.jumpsMade;
                document.getElementById('endFlips').textContent = game.gravityFlips;
                document.getElementById('endPlatforms').textContent = game.platformsLanded;
                document.getElementById('endOrbs').textContent = game.orbsSpawned > 0 ? `${Math.round((game.orbsCollected / game.orbsSpawned) * 100)}%` : '0%';
            }

            if (game.isGravityFlipped && player.y + player.height < 0 && !game.exitAnimation && !game.invincible) {
                game.exitAnimation = true;
                game.exitFrames = 30;
            }
            if (game.exitAnimation) {
                game.exitFrames--;
                if (game.exitFrames <= 0) {
                    game.gameOver = true;
                    updateHighScore();
                    gameOverScreen.style.display = 'block';
                    finalScoreDisplay.textContent = game.score;
                    document.getElementById('endJumps').textContent = game.jumpsMade;
                    document.getElementById('endFlips').textContent = game.gravityFlips;
                    document.getElementById('endPlatforms').textContent = game.platformsLanded;
                    document.getElementById('endOrbs').textContent = game.orbsSpawned > 0 ? `${Math.round((game.orbsCollected / game.orbsSpawned) * 100)}%` : '0%';
                }
            }

            // Increase speed every 50 points
            if (game.score >= game.lastSpeedIncreaseScore + 50 && game.speed < 2.0) {
                game.speed += 0.05;
                game.lastSpeedIncreaseScore = Math.floor(game.score / 50) * 50;
                console.log(`Speed increased to ${game.speed} at score ${game.score}`);
            }

            // Update zone every 200 points
            if (game.score >= 200 && game.score < 400 && game.zone === 1) {
                game.zone = 2;
                console.log(`Zone changed to 2 at score ${game.score}`);
            } else if (game.score >= 400 && game.score < 600 && game.zone === 2) {
                game.zone = 3;
                console.log(`Zone changed to 3 at score ${game.score}`);
            } else if (game.score >= 600 && game.zone === 3) {
                game.zone = 4;
                console.log(`Zone changed to 4 at score ${game.score}`);
            }

            platforms.forEach(p => p.x -= game.speed);
            orbs.forEach(o => o.x -= game.speed);
            platforms = platforms.filter(p => p.x + p.width > -60);
            orbs = orbs.filter(o => o.x + o.width > 0);

            if (platforms[platforms.length - 1].x < canvas.width - 300) {
                let nextX = platforms[platforms.length - 1].x + 200 + Math.random() * 100;
                generatePlatform(nextX, Math.random() < 0.5);
                game.score += 5;
                scoreDisplay.textContent = game.score;
            }

            bgLayers.starsSlow.forEach(star => star.x -= game.speed * star.speed);
            bgLayers.starsFast.forEach(star => star.x -= game.speed * star.speed);
            bgLayers.nebula.x -= game.speed * bgLayers.nebula.speed;
            bgLayers.planetBrown.x -= game.speed * bgLayers.planetBrown.speed;
            bgLayers.planetPurple.x -= game.speed * bgLayers.planetPurple.speed;
            bgLayers.planetIce.x -= game.speed * bgLayers.planetIce.speed;
            bgLayers.planetRinged.x -= game.speed * bgLayers.planetRinged.speed;
            bgLayers.startPlanetBrown.x -= game.speed * bgLayers.startPlanetBrown.speed;
            bgLayers.startPlanetBlue.x -= game.speed * bgLayers.startPlanetBlue.speed;
            bgLayers.asteroids.forEach(a => a.x -= game.speed * a.speed);
            bgLayers.rocketShip.x -= game.speed * bgLayers.rocketShip.speed;

            bgLayers.starsSlow.forEach(star => { if (star.x <= -star.size) star.x = canvas.width + star.size; });
            bgLayers.starsFast.forEach(star => { if (star.x <= -star.size) star.x = canvas.width + star.size; });
            if (bgLayers.nebula.x <= -canvas.width) bgLayers.nebula.x = 0;
            if (bgLayers.planetBrown.x <= -bgLayers.planetBrown.radius * 2) bgLayers.planetBrown.x = canvas.width + bgLayers.planetBrown.radius * 2 + 500;
            if (bgLayers.planetPurple.x <= -bgLayers.planetPurple.radius * 2) bgLayers.planetPurple.x = canvas.width + bgLayers.planetPurple.radius * 2 + 400;
            if (bgLayers.planetIce.x <= -bgLayers.planetIce.radius * 2) bgLayers.planetIce.x = canvas.width + bgLayers.planetIce.radius * 2 + 300;
            if (bgLayers.planetRinged.x <= -bgLayers.planetRinged.radius * 2) bgLayers.planetRinged.x = canvas.width + bgLayers.planetRinged.radius * 2 + 600;
            if (bgLayers.startPlanetBrown.x <= -bgLayers.startPlanetBrown.radius * 2) bgLayers.startPlanetBrown.x = canvas.width + bgLayers.startPlanetBrown.radius * 2;
            if (bgLayers.startPlanetBlue.x <= -bgLayers.startPlanetBlue.radius * 2) bgLayers.startPlanetBlue.x = canvas.width + bgLayers.startPlanetBlue.radius * 2;
            bgLayers.asteroids.forEach(a => { if (a.x <= -a.radius) a.x = canvas.width + a.radius; });
            if (bgLayers.rocketShip.x <= -bgLayers.rocketShip.width) bgLayers.rocketShip.x = canvas.width;

            draw();
            requestAnimationFrame(update);
        }

        function draw() {
            if (game.zone === 1) {
                ctx.fillStyle = '#0a0015';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                ctx.fillStyle = '#1a0033';
                ctx.fillRect(bgLayers.nebula.x, 0, canvas.width, canvas.height);
                ctx.fillRect(bgLayers.nebula.x + canvas.width, 0, canvas.width, canvas.height);
            } else if (game.zone === 2) {
                ctx.fillStyle = '#4a4a4a';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                bgLayers.asteroids.forEach(a => {
                    ctx.fillStyle = '#666666';
                    ctx.beginPath();
                    ctx.arc(a.x, a.y, a.radius, 0, Math.PI * 2);
                    ctx.fill();
                });
            } else if (game.zone === 3) {
                const gradient = ctx.createLinearGradient(0, 0, canvas.width, canvas.height);
                gradient.addColorStop(0, '#800080');
                gradient.addColorStop(1, '#ff69b4');
                ctx.fillStyle = gradient;
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                ctx.fillStyle = 'rgba(255, 105, 180, 0.2)';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
            } else if (game.zone === 4) {
                ctx.fillStyle = '#00004d';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                ctx.fillStyle = '#ffffff';
                ctx.fillRect(bgLayers.rocketShip.x, bgLayers.rocketShip.y - 5, bgLayers.rocketShip.width, bgLayers.rocketShip.height);
                ctx.fillStyle = '#ff0000';
                ctx.beginPath();
                ctx.moveTo(bgLayers.rocketShip.x, bgLayers.rocketShip.y);
                ctx.lineTo(bgLayers.rocketShip.x + bgLayers.rocketShip.width / 2, bgLayers.rocketShip.y - 10);
                ctx.lineTo(bgLayers.rocketShip.x + bgLayers.rocketShip.width, bgLayers.rocketShip.y);
                ctx.fill();
                ctx.fillStyle = '#ffff00';
                ctx.beginPath();
                ctx.arc(bgLayers.rocketShip.x + bgLayers.rocketShip.width / 2, bgLayers.rocketShip.y + 5, 3, 0, Math.PI * 2);
                ctx.fill();
            }

            bgLayers.starsSlow.forEach(star => {
                ctx.fillStyle = `rgba(255, 255, 255, ${star.opacity + Math.sin(Date.now() * 0.001) * 0.1})`;
                ctx.fillRect(star.x, star.y, star.size, star.size);
            });
            bgLayers.starsFast.forEach(star => {
                ctx.fillStyle = `rgba(255, 255, 255, ${star.opacity + Math.sin(Date.now() * 0.002) * 0.1})`;
                ctx.fillRect(star.x, star.y, star.size, star.size);
            });

            if (!game.started && !game.gameOver && !game.paused) {
                ctx.fillStyle = bgLayers.startPlanetBrown.color;
                ctx.beginPath();
                ctx.arc(bgLayers.startPlanetBrown.x, bgLayers.startPlanetBrown.y, bgLayers.startPlanetBrown.radius, 0, Math.PI * 2);
                ctx.fill();
                ctx.save();
                ctx.translate(bgLayers.startPlanetBrown.x, bgLayers.startPlanetBrown.y);
                ctx.rotate(Math.PI / 4);
                ctx.strokeStyle = 'rgba(204, 204, 204, 0.8)';
                ctx.lineWidth = 8;
                ctx.beginPath();
                ctx.ellipse(0, 0, 60, 20, 0, 0, Math.PI * 2);
                ctx.stroke();
                ctx.strokeStyle = 'rgba(204, 204, 204, 0.6)';
                ctx.lineWidth = 6;
                ctx.beginPath();
                ctx.ellipse(0, 0, 50, 15, 0, 0, Math.PI * 2);
                ctx.stroke();
                ctx.restore();

                ctx.fillStyle = bgLayers.startPlanetBlue.color;
                ctx.beginPath();
                ctx.arc(bgLayers.startPlanetBlue.x, bgLayers.startPlanetBlue.y, bgLayers.startPlanetBlue.radius, 0, Math.PI * 2);
                ctx.fill();
                ctx.save();
                ctx.translate(bgLayers.startPlanetBlue.x, bgLayers.startPlanetBlue.y);
                ctx.rotate(Math.PI / 4);
                ctx.strokeStyle = 'rgba(204, 204, 204, 0.8)';
                ctx.lineWidth = 8;
                ctx.beginPath();
                ctx.ellipse(0, 0, 60, 20, 0, 0, Math.PI * 2);
                ctx.stroke();
                ctx.strokeStyle = 'rgba(204, 204, 204, 0.6)';
                ctx.lineWidth = 6;
                ctx.beginPath();
                ctx.ellipse(0, 0, 50, 15, 0, 0, Math.PI * 2);
                ctx.stroke();
                ctx.restore();

                const platformX = canvas.width * 0.65 - 60;
                const platformY = canvas.height - 40;
                ctx.fillStyle = '#8b0000';
                ctx.fillRect(platformX, platformY, 120, 20);

                ctx.save();
                ctx.translate(canvas.width * 0.65, platformY - 96);
                ctx.scale(3, 3);
                ctx.fillStyle = '#ffcc99';
                ctx.fillRect(-8, -2, 16, 14);
                ctx.fillStyle = '#000000';
                ctx.fillRect(-4, 2, 2, 2);
                ctx.fillRect(2, 2, 2, 2);
                ctx.fillStyle = '#ff5555';
                ctx.fillRect(-2, 8, 4, 1);
                ctx.fillStyle = '#5555ff';
                ctx.fillRect(-8, 12, 16, 8);
                ctx.fillStyle = '#ffcc99';
                ctx.fillRect(-12, 12, 4, 6);
                ctx.fillRect(8, 12, 4, 6);
                ctx.fillStyle = '#333333';
                ctx.fillRect(-6, 20, 6, 2);
                ctx.fillRect(0, 20, 6, 2);
                ctx.fillStyle = '#00ff00';
                ctx.fillRect(-6, -10, 12, 8);
                ctx.fillStyle = '#009900';
                ctx.fillRect(-2, -2, 8, 2);
                ctx.restore();
            }

            if (game.started) {
                ctx.fillStyle = bgLayers.planetBrown.color;
                ctx.beginPath();
                ctx.arc(bgLayers.planetBrown.x, canvas.height / 2, bgLayers.planetBrown.radius, 0, Math.PI * 2);
                ctx.fill();
                if (bgLayers.planetBrown.craters) {
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.3)';
                    ctx.beginPath();
                    ctx.arc(bgLayers.planetBrown.x - 40, canvas.height / 2 - 30, 20, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.beginPath();
                    ctx.arc(bgLayers.planetBrown.x + 30, canvas.height / 2 + 20, 15, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.fillStyle = bgLayers.planetPurple.color;
                ctx.beginPath();
                ctx.arc(bgLayers.planetPurple.x, canvas.height / 2 / 3, bgLayers.planetPurple.radius, 0, Math.PI * 2);
                ctx.fill();
                if (bgLayers.planetPurple.craters) {
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.3)';
                    ctx.beginPath();
                    ctx.arc(bgLayers.planetPurple.x - 20, canvas.height / 2 / 3 - 15, 15, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.beginPath();
                    ctx.arc(bgLayers.planetPurple.x + 10, canvas.height / 2 / 3 + 10, 10, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.fillStyle = bgLayers.planetIce.color;
                ctx.beginPath();
                ctx.arc(bgLayers.planetIce.x, canvas.height * 2 / 3, bgLayers.planetIce.radius, 0, Math.PI * 2);
                ctx.fill();
                if (bgLayers.planetIce.spots) {
                    ctx.fillStyle = 'rgba(255, 255, 255, 0.5)';
                    ctx.beginPath();
                    ctx.arc(bgLayers.planetIce.x - 10, canvas.height * 2 / 3 - 10, 8, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.beginPath();
                    ctx.arc(bgLayers.planetIce.x + 5, canvas.height * 2 / 3 + 5, 5, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.fillStyle = bgLayers.planetRinged.color;
                ctx.beginPath();
                ctx.arc(bgLayers.planetRinged.x, canvas.height / 2, bgLayers.planetRinged.radius, 0, Math.PI * 2);
                ctx.fill();
                ctx.save();
                ctx.translate(bgLayers.planetRinged.x, canvas.height / 2);
                ctx.rotate(Math.PI / 6);
                ctx.strokeStyle = bgLayers.planetRinged.ringColor;
                ctx.lineWidth = 10;
                ctx.globalAlpha = 0.6 + Math.sin(Date.now() * 0.001) * 0.2;
                ctx.beginPath();
                ctx.ellipse(0, 0, bgLayers.planetRinged.radius * 1.5, bgLayers.planetRinged.radius * 0.5, 0, 0, Math.PI * 2);
                ctx.stroke();
                ctx.beginPath();
                ctx.ellipse(0, 0, bgLayers.planetRinged.radius * 1.8, bgLayers.planetRinged.radius * 0.6, 0, 0, Math.PI * 2);
                ctx.stroke();
                ctx.restore();

                ctx.fillStyle = '#8b0000';
                platforms.forEach(p => ctx.fillRect(p.x, p.y, p.width, p.height));

                ctx.fillStyle = '#ffaaaa';
                orbs.forEach(o => {
                    ctx.beginPath();
                    ctx.arc(o.x + o.width / 2, o.y + o.height / 2, o.width / 2, 0, Math.PI * 2);
                    ctx.fill();
                });

                ctx.fillStyle = '#ff4444';
                for (let x = 0; x < canvas.width; x += 20) {
                    ctx.beginPath();
                    ctx.moveTo(x, canvas.height);
                    ctx.lineTo(x + 10, canvas.height - spikeHeight);
                    ctx.lineTo(x + 20, canvas.height);
                    ctx.fill();
                }

                ctx.save();
                ctx.translate(player.x + player.width / 2, player.y + player.height / 2);
                if (player.spinning) {
                    ctx.rotate(player.spinAngle);
                } else if (game.isGravityFlipped && !game.exitAnimation) {
                    ctx.scale(1, -1);
                }

                if (game.exitAnimation) {
                    const scale = 1 - (30 - game.exitFrames) / 30;
                    ctx.scale(scale, scale);
                    ctx.globalAlpha = game.exitFrames / 30;
                }

                if (player.jumping && !game.exitAnimation) {
                    let jumpProgress = 1 - player.jumpFrames / 12;
                    let scale = 1 - 0.3 * Math.sin(jumpProgress * Math.PI);
                    ctx.scale(scale, scale);
                    ctx.rotate(jumpProgress * Math.PI * 2);
                    ctx.fillStyle = '#5555ff';
                    ctx.beginPath();
                    ctx.arc(0, 0, 12, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.fillStyle = '#00ff00';
                    ctx.fillRect(-6, -14, 12, 8);
                    ctx.fillStyle = '#009900';
                    ctx.fillRect(-2, -6, 8, 2);
                } else {
                    ctx.fillStyle = '#ffcc99';
                    ctx.fillRect(-8, -2, 16, 14);
                    ctx.fillStyle = '#000000';
                    ctx.fillRect(-4, 2, 2, 2);
                    ctx.fillRect(2, 2, 2, 2);
                    ctx.fillStyle = '#ff5555';
                    ctx.fillRect(-2, 8, 4, 1);
                    ctx.fillStyle = '#5555ff';
                    ctx.fillRect(-8, 12, 16, 8);
                    ctx.fillStyle = '#ffcc99';
                    ctx.fillRect(-12, 12, 4, 6);
                    ctx.fillRect(8, 12, 4, 6);
                    ctx.fillStyle = '#333333';
                    ctx.fillRect(-6, 20, 6, 2);
                    ctx.fillRect(0, 20, 6, 2);
                    ctx.fillStyle = '#00ff00';
                    ctx.fillRect(-6, -10, 12, 8);
                    ctx.fillStyle = '#009900';
                    ctx.fillRect(-2, -2, 8, 2);
                }
                ctx.restore();

                ctx.fillStyle = '#ffcc99';
                ctx.fillRect(10, canvas.height - 72, 16, 14);
                ctx.fillStyle = '#000000';
                ctx.fillRect(14, canvas.height - 68, 2, 2);
                ctx.fillRect(20, canvas.height - 68, 2, 2);
                ctx.fillStyle = '#ff5555';
                ctx.fillRect(16, canvas.height - 62, 4, 1);
                ctx.fillStyle = '#00ff00';
                ctx.fillRect(12, canvas.height - 80, 12, 8);
                ctx.fillStyle = '#009900';
                ctx.fillRect(16, canvas.height - 72, 8, 2);

                ctx.fillStyle = '#333';
                ctx.fillRect(10, canvas.height - 50, 100, 10);
                ctx.fillStyle = '#00ff00';
                ctx.fillRect(10, canvas.height - 50, player.doubleJumpCharge, 10);
                ctx.strokeStyle = '#fff';
                ctx.lineWidth = 1;
                ctx.strokeRect(10, canvas.height - 50, 100, 10);

                ctx.fillStyle = '#333';
                ctx.fillRect(10, canvas.height - 35, 100, 10);
                ctx.fillStyle = '#ff8800';
                ctx.fillRect(10, canvas.height - 35, (game.maxFlips - game.flipCount) * (100 / game.maxFlips), 10);
                ctx.strokeStyle = '#fff';
                ctx.lineWidth = 1;
                ctx.strokeRect(10, canvas.height - 35, 100, 10);
            }

            ctx.fillStyle = '#fff';
            ctx.beginPath();
            ctx.moveTo(muteButton.x + 5, muteButton.y + 8);
            ctx.lineTo(muteButton.x + 15, muteButton.y + 2);
            ctx.lineTo(muteButton.x + 15, muteButton.y + 28);
            ctx.lineTo(muteButton.x + 5, muteButton.y + 22);
            ctx.fill();
            ctx.fillRect(muteButton.x + 2, muteButton.y + 10, 5, 10);
            if (!game.muted) {
                ctx.beginPath();
                ctx.arc(muteButton.x + 20, muteButton.y + 15, 5, -Math.PI / 3, Math.PI / 3);
                ctx.strokeStyle = '#fff';
                ctx.lineWidth = 2;
                ctx.stroke();
                ctx.beginPath();
                ctx.arc(muteButton.x + 20, muteButton.y + 15, 8, -Math.PI / 3, Math.PI / 3);
                ctx.stroke();
            } else {
                ctx.strokeStyle = '#ff5555';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.moveTo(muteButton.x + 5, muteButton.y + 5);
                ctx.lineTo(muteButton.x + 25, muteButton.y + 25);
                ctx.moveTo(muteButton.x + 25, muteButton.y + 5);
                ctx.lineTo(muteButton.x + 5, muteButton.y + 25);
                ctx.stroke();
            }

            if (game.started && !game.gameOver) {
                ctx.fillStyle = '#fff';
                if (!game.paused) {
                    ctx.fillRect(pauseButton.x + 8, pauseButton.y + 5, 4, 20);
                    ctx.fillRect(pauseButton.x + 18, pauseButton.y + 5, 4, 20);
                } else {
                    ctx.beginPath();
                    ctx.moveTo(pauseButton.x + 10, pauseButton.y + 5);
                    ctx.lineTo(pauseButton.x + 22, pauseButton.y + 15);
                    ctx.lineTo(pauseButton.x + 10, pauseButton.y + 25);
                    ctx.fill();
                }
            }
        }

        function startGame() {
            game.started = true;
            howToPlayScreen.style.display = 'none';
            initGame(true);
            update();
        }

        function resetGame() {
            game.score = 0;
            game.gravity = 0.2;
            game.isGravityFlipped = false;
            game.speed = 1;
            game.gameOver = false;
            game.started = true;
            game.paused = false;
            game.pausing = false;
            game.zone = 1;
            game.orbsSpawned = 0;
            game.orbsCollected = 0;
            initGame(true);
            scoreDisplay.textContent = '0';
            gameOverScreen.style.display = 'none';
            update();
        }

        howToPlayScreen.style.display = 'block';
        highScoreDisplay.textContent = game.highScore;
        initGame(false);
        draw();
    </script>
</body>
</html>