# in-class-week-7
<!DOCTYPE html>
<html>
<head>
    <title>Temple Run Game</title>
    <style>
        #gameCanvas {
            width: 800px;
            height: 400px;
            border: 2px solid black;
            position: relative;
            background: #87CEEB;
            overflow: hidden;
            margin: 0 auto;
        }

        #player {
            width: 40px;
            height: 40px;
            background: #FFD700;
            position: absolute;
            bottom: 0;
            left: 100px;
            border-radius: 50%;
        }

        .obstacle {
            width: 40px;
            height: 40px;
            background: #FF0000;
            position: absolute;
            bottom: 0;
        }

        #score {
            position: absolute;
            top: 10px;
            left: 10px;
            font-family: Arial, sans-serif;
            font-size: 20px;
        }
    </style>
</head>
<body>
    <div id="gameCanvas">
        <div id="player"></div>
        <div id="score">Score: 0</div>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const player = document.getElementById('player');
        const scoreDisplay = document.getElementById('score');

        let playerY = 0;
        let isJumping = false;
        let score = 0;
        let obstacles = [];
        let gameSpeed = 5;

        // Player movement
        document.addEventListener('keydown', (e) => {
            if (e.code === 'Space' && !isJumping) {
                jump();
            }
        });

        function jump() {
            isJumping = true;
            let jumpHeight = 0;
            const jumpInterval = setInterval(() => {
                if (jumpHeight < 150) {
                    jumpHeight += 10;
                    player.style.bottom = jumpHeight + 'px';
                } else {
                    clearInterval(jumpInterval);
                    fall();
                }
            }, 20);
        }

        function fall() {
            const fallInterval = setInterval(() => {
                if (jumpHeight > 0) {
                    jumpHeight -= 10;
                    player.style.bottom = jumpHeight + 'px';
                } else {
                    clearInterval(fallInterval);
                    isJumping = false;
                }
            }, 20);
        }

        // Create obstacles
        function createObstacle() {
            const obstacle = document.createElement('div');
            obstacle.className = 'obstacle';
            obstacle.style.left = '800px';
            canvas.appendChild(obstacle);
            obstacles.push(obstacle);
        }

        // Game loop
        function gameLoop() {
            // Move obstacles
            obstacles.forEach((obstacle, index) => {
                let left = parseInt(obstacle.style.left);
                left -= gameSpeed;

                if (left < -40) {
                    obstacle.remove();
                    obstacles.splice(index, 1);
                    score += 10;
                    scoreDisplay.textContent = `Score: ${score}`;
                } else {
                    obstacle.style.left = left + 'px';
                }

                // Collision detection
                const playerRect = player.getBoundingClientRect();
                const obstacleRect = obstacle.getBoundingClientRect();

                if (playerRect.left < obstacleRect.right &&
                    playerRect.right > obstacleRect.left &&
                    playerRect.bottom > obstacleRect.top) {
                    alert(`Game Over! Score: ${score}`);
                    resetGame();
                }
            });

            requestAnimationFrame(gameLoop);
        }

        // Reset game
        function resetGame() {
            obstacles.forEach(obstacle => obstacle.remove());
            obstacles = [];
            score = 0;
            scoreDisplay.textContent = `Score: ${score}`;
            gameSpeed = 5;
        }

        // Start game
        setInterval(() => {
            if (Math.random() < 0.02) {
                createObstacle();
            }
            gameSpeed += 0.005; // Gradually increase difficulty
        }, 1000/60);

        gameLoop();
    </script>
</body>
</html>
