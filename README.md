# ATL-Lab

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Racing Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #000;
            color: #fff;
            margin: 0;
            padding: 0;
        }
        #gameContainer {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            perspective: 1000px;
        }
        #gameCanvas {
            width: 400px;
            height: 400px;
            transform: rotateX(20deg);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.8);
            position: relative;
            overflow: hidden;
        }
        #road {
            width: 100%;
            height: 200%;
            position: absolute;
            top: 0;
            left: 0;
            background-image: linear-gradient(to bottom, #333 10%, #666 50%, #333 90%);
            animation: animateRoad linear infinite;
        }
        @keyframes animateRoad {
            from { top: 0; }
            to { top: -100%; }
        }
        #car {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            width: 40px;
            height: 80px;
            background-color: red;
            border-radius: 5px;
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
        }
        .stone {
            position: absolute;
            width: 30px;
            height: 30px;
            background-color: #aaa;
            border-radius: 50%;
        }
        #score {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 24px;
        }
    </style>
</head>
<body>
    <h1>Racing Game</h1>
    <div id="gameContainer">
        <div id="gameCanvas">
            <div id="road"></div>
            <div id="car"></div>
            <div id="score">Score: 0</div>
        </div>
    </div>
    
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const gameCanvas = document.getElementById('gameCanvas');
            const road = document.getElementById('road');
            const car = document.getElementById('car');
            const scoreDisplay = document.getElementById('score');

            let carX = 50; // Initial car position (percentage)
            const carSpeed = 5; // Car movement speed
            let score = 0;

            // Handle keyboard input (left and right arrow keys)
            document.addEventListener('keydown', (event) => {
                if (event.key === 'ArrowLeft' && carX > 10) {
                    carX -= carSpeed;
                } else if (event.key === 'ArrowRight' && carX < 90) {
                    carX += carSpeed;
                }
                updateCarPosition();
            });

            // Update car position based on carX percentage
            function updateCarPosition() {
                car.style.left = carX + '%';
            }

            // Game loop to check collisions, update score, and create obstacles
            function gameLoop() {
                const obstacles = document.getElementsByClassName('stone');

                // Move and check collisions with obstacles
                for (let obstacle of obstacles) {
                    obstacle.style.top = parseInt(obstacle.style.top) + carSpeed + 'px';

                    // Check collision between car and obstacle
                    if (isColliding(car, obstacle)) {
                        gameOver();
                        return;
                    }

                    // Remove obstacle if it goes beyond the game canvas
                    if (parseInt(obstacle.style.top) > gameCanvas.clientHeight) {
                        obstacle.remove();
                        score++;
                        scoreDisplay.textContent = 'Score: ' + score;
                    }
                }

                // Create new obstacles randomly
                if (Math.random() < 0.02) { // Adjust obstacle spawn rate as needed
                    createObstacle();
                }

                requestAnimationFrame(gameLoop);
            }

            // Check collision between two elements
            function isColliding(element1, element2) {
                const rect1 = element1.getBoundingClientRect();
                const rect2 = element2.getBoundingClientRect();
                return !(rect1.right < rect2.left ||
                         rect1.left > rect2.right ||
                         rect1.bottom < rect2.top ||
                         rect1.top > rect2.bottom);
            }

            // Create a new obstacle (stone)
            function createObstacle() {
                const obstacle = document.createElement('div');
                obstacle.className = 'stone';
                obstacle.style.left = Math.random() * (gameCanvas.clientWidth - 30) + 'px';
                obstacle.style.top = '-30px'; // Start above the game canvas
                road.appendChild(obstacle);
            }

            // Game over function
            function gameOver() {
                alert('Game Over! Your score: ' + score);
                carX = 50; // Reset car position
                score = 0; // Reset score
                scoreDisplay.textContent = 'Score: ' + score;

                // Remove all obstacles from the road
                const obstacles = document.getElementsByClassName('stone');
                while (obstacles.length > 0) {
                    obstacles[0].remove();
                }

                updateCarPosition(); // Update car position
            }

            // Start the game loop
            gameLoop();
        });
    </script>
</body>
</html>
