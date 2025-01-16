<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Code Camp Game</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            font-family: Arial, sans-serif;
            background: url('CodeCampGame Background .png') no-repeat center center fixed;
            background-size: cover;
        }
        #overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(164, 28, 6, 0.25);
        }
        #game-area {
            position: relative;
            width: 100%;
            height: 100vh;
        }
        #start-button {
            position: absolute;
            top: 60%;
            left: 80%;
            transform: translate(-50%, -50%);
            padding: 20px 30px;
            font-size: 26px;
            color: white;
            background-color: #A41C06;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            display: none;
            user-select: none;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            
        }
        #instructions {
            position: absolute;
            top: 44%;
            left: 80%;
            transform: translate(-50%, -50%);
            font-size: 23px;
            color: white;
            text-align: center;
            display: none;
        }
        #instructions strong {
            font-weight: bold;
            text-decoration: underline;
        }
        #timer {
            position: absolute;
            top: 20px;
            right: 20px;
            width: 80px;
            height: 80px;
            border-radius: 50%;
            border: 5px solid white;
            display: none;
            align-items: center;
            justify-content: center;
            font-size: 50px;
            font-weight: bold;
            color: white;
             user-select: none;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
        }
        .balloon {
            position: absolute;
            width: 90px;
            height: 90px;
            background-color: #A41C06;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 55px;
            color: #ea513e;
            font-weight: bold;
            user-select: none;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
           
        }
        #final-score {
            position: absolute;
            top: 50%;
            left: 80%;
            transform: translate(-50%, -50%);
            color: white;
            font-size: 36px;
            text-align: center;
            display: none;
        }
        #final-score button {
            margin-top: 20px;
            padding: 15px 15px;
            font-size: 25px;
            font-weight: bold;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            user-select: none;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            
        }
        #try-again {
            background-color: #ea513e;
            color: white;
        }
        #join-codecamp {
            background-color: #4CAF50;
            color: white;
        }
        @media (max-width: 768px) {

             body {
                background-size: contain;
                background-repeat: no-repeat;
                background-position: center;
            }
            #start-button {
                top: 50%;
                left: 78%;
                font-size: 15px;
                padding: 20px 25px;
                border-radius: 15px;
            }
            #instructions {
                top: 40%;
                left: 50%;
                font-size: 10px;
                transform: translate(-50%, -50%);
            }
            .balloon {
                width: 70px;
                height: 70px;
                font-size: 40px;
            }
            #timer {
                width: 60px;
                height: 60px;
                font-size: 40px;
            }
            #final-score {
                top: 30%;
                left: 50%;
                font-size: 28px;
            }
            #final-score button {
                font-size: 20px;
                padding: 10px 10px;
            }
        }

        @media (max-width: 480px) {

             body {
                background-size: contain;
                background-repeat: no-repeat;
                background-position: center;
            }
            #start-button {
                 font-size: 18px;
                 padding: 12px 18px;
             }
             #instructions {
                 font-size: 12px;
            }
             .balloon {
               width: 60px;
               height: 60px;
               font-size: 30px;
             }
            #timer {
                width: 50px;
                height: 50px;
                font-size: 35px;
            }
            #final-score {
                font-size: 24px;
            }
            #final-score button {
                font-size: 18px;
                padding: 8px 8px;
            }
}

    </style>
</head>
<body>
    <div id="overlay"></div>
    <div id="game-area">
        <button id="start-button"><strong>Start Game</strong></button>
        <div id="instructions"><strong>Instruction:</strong> Pop all balloons that appear in record time to join the Code Camp. Let's Play!</div>
        <div id="timer"><strong>30</strong></div>
        <div id="final-score"></div>
    </div>

    <!-- Sound Effects -->
    <audio id="background-music" src="C:\Users\HP\Desktop\Web Design\CodeCamp\Background Theme .MP3" loop></audio>
    <audio id="pop-sound" src="C:\Users\HP\Desktop\Web Design\CodeCamp\108616__juskiddink__hard-pop.wav"></audio>

    <script>
        let score = 0;
        let balloonCount = 0;
        const maxBalloons = 8;
        let timer = 30;
        let gameInterval;
        let startTime;

        const gameArea = document.getElementById('game-area');
        const startButton = document.getElementById('start-button');
        const timerDisplay = document.getElementById('timer');
        const finalScoreDisplay = document.getElementById('final-score');
        const backgroundMusic = document.getElementById('background-music');
        const popSound = document.getElementById('pop-sound');
        const instructions = document.getElementById('instructions');

        backgroundMusic.volume = 0.2;

        // Customizable array of letters to display on balloons
        const alphabetArray = ['T', 'I', 'M', 'E', 'T', 'E', 'C', 'H'];
        let letterIndex = 0;

function createBalloon() {
    if (balloonCount >= maxBalloons) return;

    balloonCount++;
    const balloon = document.createElement('div');
    balloon.classList.add('balloon');

    let leftPos, topPos;
    do {
        leftPos = Math.random() * (gameArea.clientWidth - 90);
        topPos = Math.random() * (gameArea.clientHeight - 90);
    } while (leftPos < 0 || topPos < 0);

    balloon.style.left = `${leftPos}px`;
    balloon.style.top = `${topPos}px`;
    balloon.textContent = alphabetArray[letterIndex % alphabetArray.length];
    letterIndex++;

    balloon.addEventListener('click', () => popBalloon(balloon));
    gameArea.appendChild(balloon);

    setTimeout(() => {
        if (gameArea.contains(balloon)) {
            gameArea.removeChild(balloon);
            if (balloonCount === maxBalloons) {
                endGame();
            } else {
                createBalloon();
            }
        }
    }, 3000);
}

function popBalloon(balloon) {
    if (!gameArea.contains(balloon)) return;
    gameArea.removeChild(balloon);
    score++;
    popSound.play();

    if (balloonCount < maxBalloons) {
        setTimeout(createBalloon, 450);  // Slight delay before creating the next balloon
    } else {
        endGame();
    }
}

        function startTimer() {
            startTime = Date.now();
            timerDisplay.style.display = 'flex';
            gameInterval = setInterval(() => {
                const elapsedTime = Math.floor((Date.now() - startTime) / 1000);
                timerDisplay.textContent = 30 - elapsedTime;
                if (elapsedTime >= 30) {
                    clearInterval(gameInterval);
                    endGame();
                }
            }, 1000);
        }

        function startGame() {
            score = 0;
            balloonCount = 0;
            timerDisplay.textContent = 30;

            startButton.style.display = 'none';
            instructions.style.display = 'none';
            document.body.style.backgroundImage = 'none';

            createBalloon();
            startTimer();
            backgroundMusic.play();
        }

        function endGame() {
            clearInterval(gameInterval);
            const timeTaken = Math.floor((Date.now() - startTime) / 1000);
            const percentageScore = Math.max(0, 100 - (timeTaken * (100 / 30)));

            timerDisplay.style.display = 'none';
            backgroundMusic.pause();
            document.body.style.backgroundImage = "url('CodeCampGame Background .png')";

            if (percentageScore < 65) {
                finalScoreDisplay.innerHTML = `Game Over!<br>Your final score is ${percentageScore.toFixed(0)}%<br><button id="try-again">Try Again</button>`;
                document.getElementById('try-again').addEventListener('click', () => location.reload());
            } else {
                finalScoreDisplay.innerHTML = `Congratulations!<br>Your final score is ${percentageScore.toFixed(0)}%<br><a href="https://chat.whatsapp.com/KHkkb6ipRVf6qAM2K4j3ib" target="_blank">
                <button id="join-codecamp">Join Code Camp</button></a>`;
                document.getElementById('join-codecamp').addEventListener('click', () => window.location.href = 'https://chat.whatsapp.com/KHkkb6ipRVf6qAM2K4j3ib'); // Replace with your URL
                };
            }

            finalScoreDisplay.style.display = 'block';

        startButton.addEventListener('click', startGame);

        window.onload = () => {
         setTimeout(() => {
        startButton.style.display = 'block';
        instructions.style.display = 'block';
         }, 2000);
        };
    </script>
</body>
</html>
