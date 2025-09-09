<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Glass Bridge Challenge - Cross to Survive!</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(180deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            min-height: 100vh;
            overflow: hidden;
            perspective: 2000px;
        }

        /* City skyline background */
        .cityscape {
            position: fixed;
            bottom: -200px;
            left: 0;
            width: 100%;
            height: 400px;
            background: linear-gradient(to top, #000 0%, #1a1a2e 100%);
            z-index: -1;
        }

        .building {
            position: absolute;
            bottom: 0;
            background: linear-gradient(to top, #111, #222);
            border-top: 2px solid #444;
        }

        /* Bridge Container - positioned lower so question box doesn't cover it */
        .bridge-container {
            position: absolute;
            top: 60%;
            left: 50%;
            transform: translate(-50%, -50%) rotateX(15deg);
            width: 1200px;
            height: 150px;
            z-index: 10;
        }

        /* Entry and exit platforms */
        .platform {
            position: absolute;
            width: 120px;
            height: 150px;
            background: linear-gradient(135deg, #2c3e50, #34495e);
            border: 3px solid #444;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 3rem;
            z-index: 15;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
        }

        .platform.start {
            left: -150px;
            background: linear-gradient(135deg, #27ae60, #2ecc71);
            color: white;
        }

        .platform.end {
            right: -150px;
            background: linear-gradient(135deg, #e74c3c, #c0392b);
            color: white;
        }

        .platform.reached {
            background: linear-gradient(135deg, #f39c12, #e67e22);
            animation: victory 1s ease-in-out infinite alternate;
        }

        @keyframes victory {
            0% { box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5); }
            100% { box-shadow: 0 30px 80px rgba(243, 156, 18, 0.5); }
        }

        /* Glass panels on the bridge */
        .glass-panel {
            position: absolute;
            width: 100px;
            height: 150px;
            background: linear-gradient(135deg, 
                rgba(255, 255, 255, 0.1) 0%, 
                rgba(255, 255, 255, 0.05) 50%, 
                rgba(255, 255, 255, 0.1) 100%);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 8px;
            box-shadow: 
                0 10px 30px rgba(0, 0, 0, 0.3),
                0 0 0 1px rgba(255, 255, 255, 0.1) inset;
            transition: all 0.5s ease;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            font-weight: bold;
            color: rgba(255, 255, 255, 0.8);
        }

        .glass-panel:hover {
            background: rgba(255, 255, 255, 0.15);
            transform: translateY(-5px);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
        }

        .glass-panel.safe {
            border-color: #2ecc71;
            background: linear-gradient(135deg, 
                rgba(46, 204, 113, 0.2) 0%, 
                rgba(46, 204, 113, 0.1) 50%, 
                rgba(46, 204, 113, 0.2) 100%);
            box-shadow: 0 0 20px rgba(46, 204, 113, 0.3);
        }

        .glass-panel.broken {
            animation: shatterPanel 1s ease-out forwards;
            pointer-events: none;
        }

        @keyframes shatterPanel {
            0% { 
                transform: rotateX(0deg) translateY(0px);
                opacity: 1;
            }
            30% { 
                transform: rotateX(15deg) translateY(10px);
                opacity: 0.7;
            }
            100% { 
                transform: rotateX(90deg) translateY(500px);
                opacity: 0;
            }
        }

        /* Player character */
        .player {
            position: absolute;
            width: 60px;
            height: 60px;
            background: linear-gradient(135deg, #e74c3c, #c0392b);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8rem;
            color: white;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.4);
            z-index: 20;
            transition: all 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94);
            top: -80px;
            left: -90px;
        }

        .player.moving {
            transition-duration: 1.2s;
        }

        .player.falling {
            animation: playerFall 2s ease-in forwards;
            z-index: 1;
        }

        @keyframes playerFall {
            0% { 
                transform: translateY(0px) rotate(0deg);
                opacity: 1;
            }
            100% { 
                transform: translateY(800px) rotate(720deg);
                opacity: 0;
            }
        }

        /* Question interface */
        .question-interface {
            position: fixed;
            top: 50px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.9);
            backdrop-filter: blur(20px);
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 15px;
            padding: 30px;
            max-width: 600px;
            width: 90%;
            color: white;
            text-align: center;
            z-index: 100;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
        }

        .question-header {
            margin-bottom: 20px;
        }

        .question-title {
            font-size: 1.5rem;
            font-weight: bold;
            margin-bottom: 10px;
            color: #e74c3c;
        }

        .progress-info {
            font-size: 1rem;
            color: rgba(255, 255, 255, 0.7);
            margin-bottom: 20px;
        }

        .question-text {
            font-size: 1.3rem;
            line-height: 1.6;
            margin-bottom: 25px;
            color: #fff;
        }

        .answer-buttons {
            display: flex;
            gap: 20px;
            justify-content: center;
            margin-bottom: 20px;
        }

        .answer-btn {
            flex: 1;
            max-width: 200px;
            padding: 15px 30px;
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 10px;
            background: rgba(255, 255, 255, 0.1);
            color: #fff;
            font-size: 1.2rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .answer-btn:hover {
            background: rgba(255, 255, 255, 0.2);
            border-color: rgba(255, 255, 255, 0.5);
            transform: translateY(-2px);
        }

        .answer-btn.correct {
            border-color: #2ecc71;
            background: rgba(46, 204, 113, 0.3);
            box-shadow: 0 0 20px rgba(46, 204, 113, 0.5);
        }

        .answer-btn.incorrect {
            border-color: #e74c3c;
            background: rgba(231, 76, 60, 0.3);
            box-shadow: 0 0 20px rgba(231, 76, 60, 0.5);
        }

        .answer-btn:disabled {
            cursor: not-allowed;
            opacity: 0.7;
        }

        .step-forward-btn {
            display: none;
            width: 200px;
            margin: 20px auto;
            padding: 15px 30px;
            background: linear-gradient(45deg, #2ecc71, #27ae60);
            border: none;
            border-radius: 10px;
            color: white;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
        }

        .step-forward-btn.show {
            display: block;
        }

        .step-forward-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 25px rgba(46, 204, 113, 0.4);
        }

        /* Game screens */
        .game-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.95);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1000;
        }

        .screen-content {
            text-align: center;
            color: #fff;
            background: rgba(255, 255, 255, 0.1);
            padding: 50px;
            border-radius: 20px;
            backdrop-filter: blur(20px);
            border: 2px solid rgba(255, 255, 255, 0.2);
            max-width: 600px;
        }

        .screen-title {
            font-size: 3.5rem;
            font-weight: bold;
            margin-bottom: 20px;
        }

        .intro-title {
            background: linear-gradient(45deg, #e74c3c, #c0392b);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .success-title {
            background: linear-gradient(45deg, #2ecc71, #27ae60);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .fail-title {
            color: #e74c3c;
            text-shadow: 0 0 30px rgba(231, 76, 60, 0.5);
        }

        .screen-message {
            font-size: 1.4rem;
            line-height: 1.6;
            margin-bottom: 30px;
            color: rgba(255, 255, 255, 0.9);
        }

        .screen-btn {
            padding: 20px 50px;
            background: linear-gradient(45deg, #667eea, #764ba2);
            border: none;
            border-radius: 15px;
            color: white;
            font-size: 1.3rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .screen-btn:hover {
            transform: translateY(-3px) scale(1.05);
            box-shadow: 0 15px 35px rgba(102, 126, 234, 0.4);
        }

        /* Bridge step indicators */
        .step-indicators {
            position: fixed;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 10px;
            z-index: 50;
        }

        .step-dot {
            width: 15px;
            height: 15px;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.3);
            border: 2px solid rgba(255, 255, 255, 0.5);
            transition: all 0.3s ease;
        }

        .step-dot.completed {
            background: #2ecc71;
            border-color: #2ecc71;
            box-shadow: 0 0 15px rgba(46, 204, 113, 0.5);
        }

        .step-dot.current {
            background: #e74c3c;
            border-color: #e74c3c;
            box-shadow: 0 0 15px rgba(231, 76, 60, 0.5);
            animation: pulse 1s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.2); }
        }

        /* Responsive design */
        @media (max-width: 768px) {
            .bridge-container {
                width: 90%;
                transform: translate(-50%, -50%) rotateX(15deg) scale(0.8);
            }
            
            .question-interface {
                top: 30px;
                padding: 20px;
            }
            
            .answer-buttons {
                flex-direction: column;
                gap: 15px;
            }
            
            .screen-title {
                font-size: 2.5rem;
            }
        }
    </style>
</head>
<body>
    <!-- Cityscape background -->
    <div class="cityscape" id="cityscape"></div>

    <!-- Intro Screen -->
    <div class="game-screen" id="introScreen">
        <div class="screen-content">
            <div class="screen-title intro-title">🌉 Glass Bridge Challenge 🌉</div>
            <div class="screen-message">
                You must cross the deadly glass bridge to survive!<br><br>
                Each step requires a correct answer.<br>
                Get it wrong and you'll fall through the glass!<br><br>
                <strong>10 questions. 10 steps. One chance to cross.</strong>
            </div>
            <button class="screen-btn" onclick="startChallenge()">Begin the Challenge</button>
        </div>
    </div>

    <!-- Success Screen -->
    <div class="game-screen" id="successScreen" style="display: none;">
        <div class="screen-content">
            <div class="screen-title success-title">🎉 YOU SURVIVED! 🎉</div>
            <div class="screen-message">
                Congratulations! You've successfully crossed the glass bridge!<br><br>
                You answered <span id="finalScore">0</span> questions correctly and made it to safety!<br><br>
                <strong>You are one of the few survivors!</strong>
            </div>
            <button class="screen-btn" onclick="restartChallenge()">Cross Again?</button>
        </div>
    </div>

    <!-- Game Over Screen -->
    <div class="game-screen" id="gameOverScreen" style="display: none;">
        <div class="screen-content">
            <div class="screen-title fail-title">💀 YOU FELL! 💀</div>
            <div class="screen-message">
                The glass shattered beneath you!<br><br>
                You made it <span id="stepsCompleted">0</span> steps across the bridge before falling.<br><br>
                <strong>Better luck next time...</strong>
            </div>
            <button class="screen-btn" onclick="restartChallenge()">Try Again</button>
        </div>
    </div>

    <!-- Bridge Container -->
    <div class="bridge-container" id="bridgeContainer" style="display: none;">
        <!-- Start Platform -->
        <div class="platform start">🚪</div>
        
        <!-- Glass Panels (will be generated by JavaScript) -->
        <div id="glassPanels"></div>
        
        <!-- End Platform -->
        <div class="platform end" id="endPlatform">🏆</div>
        
        <!-- Player -->
        <div class="player" id="player">👤</div>
    </div>

    <!-- Question Interface -->
    <div class="question-interface" id="questionInterface" style="display: none;">
        <div class="question-header">
            <div class="question-title">Glass Bridge Challenge</div>
            <div class="progress-info">
                Step <span id="currentStep">1</span> of <span id="totalSteps">10</span> - 
                Answer correctly to step forward safely
            </div>
        </div>
        
        <div class="question-text" id="questionText">Loading question...</div>
        
        <div class="answer-buttons">
            <button class="answer-btn" id="trueBtn" onclick="selectAnswer(true)">True</button>
            <button class="answer-btn" id="falseBtn" onclick="selectAnswer(false)">False</button>
        </div>
        
        <button class="step-forward-btn" id="stepForwardBtn" onclick="stepForward()">Step Forward</button>
    </div>

    <!-- Step Indicators -->
    <div class="step-indicators" id="stepIndicators" style="display: none;"></div>

    <script>
        // Game state
        let questions = [];
        let currentQuestionIndex = 0;
        let score = 0;
        let selectedAnswer = null;
        let isAnswered = false;
        let gameOver = false;
        const totalSteps = 10;

        // Sample questions
        const sampleQuestions = [
            {
                "question": "Regular exercise can help reduce the risk of heart disease.",
                "answer": true,
                "explanation": "Exercise strengthens the heart muscle and improves cardiovascular health."
            },
            {
                "question": "Drinking 8 glasses of water daily is necessary for everyone.",
                "answer": false,
                "explanation": "Water needs vary by individual, activity level, and climate."
            },
            {
                "question": "Vitamin D is primarily obtained from sunlight exposure.",
                "answer": true,
                "explanation": "The skin produces vitamin D when exposed to UVB rays from sunlight."
            },
            {
                "question": "All fats are bad for your health.",
                "answer": false,
                "explanation": "Some fats like omega-3s are essential for good health."
            },
            {
                "question": "Sleep deprivation can weaken your immune system.",
                "answer": true,
                "explanation": "Lack of sleep reduces the body's ability to fight infections."
            },
            {
                "question": "Organic foods are always more nutritious than conventional foods.",
                "answer": false,
                "explanation": "Nutritional content is similar; organic refers to farming methods."
            },
            {
                "question": "Stress can have physical effects on your body.",
                "answer": true,
                "explanation": "Chronic stress can lead to various health problems including high blood pressure."
            },
            {
                "question": "You need to detox your body regularly with special diets.",
                "answer": false,
                "explanation": "The liver and kidneys naturally detoxify the body effectively."
            },
            {
                "question": "Meditation can help lower blood pressure.",
                "answer": true,
                "explanation": "Regular meditation practice has been shown to reduce hypertension."
            },
            {
                "question": "Carbohydrates should be completely avoided for weight loss.",
                "answer": false,
                "explanation": "Complex carbohydrates are important for energy and can be part of a healthy diet."
            }
        ];

        // Initialize the app
        document.addEventListener('DOMContentLoaded', function() {
            createCityscape();
            loadQuestions();
        });

        // Create cityscape background
        function createCityscape() {
            const cityscape = document.getElementById('cityscape');
            for (let i = 0; i < 20; i++) {
                const building = document.createElement('div');
                building.className = 'building';
                building.style.left = (i * 60) + 'px';
                building.style.width = Math.random() * 40 + 30 + 'px';
                building.style.height = Math.random() * 200 + 100 + 'px';
                cityscape.appendChild(building);
            }
        }

        // Load questions
        function loadQuestions() {
            questions = [...sampleQuestions];
        }

        // Start the challenge
        function startChallenge() {
            document.getElementById('introScreen').style.display = 'none';
            document.getElementById('bridgeContainer').style.display = 'block';
            document.getElementById('questionInterface').style.display = 'block';
            document.getElementById('stepIndicators').style.display = 'flex';
            
            createGlassPanels();
            createStepIndicators();
            displayQuestion();
        }

        // Create glass panels for the bridge
        function createGlassPanels() {
            const container = document.getElementById('glassPanels');
            container.innerHTML = '';
            
            for (let i = 0; i < totalSteps; i++) {
                const panel = document.createElement('div');
                panel.className = 'glass-panel';
                panel.style.left = (i * 110 + 30) + 'px';
                panel.textContent = `${i + 1}`;
                panel.id = `panel-${i}`;
                container.appendChild(panel);
            }
        }

        // Create step indicators
        function createStepIndicators() {
            const container = document.getElementById('stepIndicators');
            container.innerHTML = '';
            
            for (let i = 0; i < totalSteps; i++) {
                const dot = document.createElement('div');
                dot.className = 'step-dot';
                if (i === 0) dot.classList.add('current');
                dot.id = `dot-${i}`;
                container.appendChild(dot);
            }
        }

        // Display current question
        function displayQuestion() {
            if (gameOver) return;
            
            if (currentQuestionIndex >= questions.length || currentQuestionIndex >= totalSteps) {
                showSuccess();
                return;
            }

            const question = questions[currentQuestionIndex];
            
            document.getElementById('currentStep').textContent = currentQuestionIndex + 1;
            document.getElementById('totalSteps').textContent = totalSteps;
            document.getElementById('questionText').textContent = question.question;
            
            // Reset button states
            const trueBtn = document.getElementById('trueBtn');
            const falseBtn = document.getElementById('falseBtn');
            
            trueBtn.disabled = false;
            falseBtn.disabled = false;
            trueBtn.classList.remove('correct', 'incorrect');
            falseBtn.classList.remove('correct', 'incorrect');
            
            // Reset state
            selectedAnswer = null;
            isAnswered = false;
            document.getElementById('stepForwardBtn').classList.remove('show');
        }

        // Handle answer selection
        function selectAnswer(answer) {
            if (isAnswered || gameOver) return;
            
            selectedAnswer = answer;
            isAnswered = true;
            
            const currentQuestion = questions[currentQuestionIndex];
            const trueBtn = document.getElementById('trueBtn');
            const falseBtn = document.getElementById('falseBtn');
            
            // Disable buttons
            trueBtn.disabled = true;
            falseBtn.disabled = true;
            
            // Show correct answer
            if (currentQuestion.answer === true) {
                trueBtn.classList.add('correct');
                if (selectedAnswer === false) {
                    falseBtn.classList.add('incorrect');
                }
            } else {
                falseBtn.classList.add('correct');
                if (selectedAnswer === true) {
                    trueBtn.classList.add('incorrect');
                }
            }
            
            // Check if answer is correct
            if (selectedAnswer === currentQuestion.answer) {
                score++;
                // Show step forward button for correct answers
                setTimeout(() => {
                    document.getElementById('stepForwardBtn').classList.add('show');
                }, 500);
            } else {
                // Wrong answer - fall through the glass!
                setTimeout(() => {
                    fallThroughGlass();
                }, 1500);
            }
        }

        // Step forward on correct answer
        function stepForward() {
            const player = document.getElementById('player');
            const currentPanel = document.getElementById(`panel-${currentQuestionIndex}`);
            const currentDot = document.getElementById(`dot-${currentQuestionIndex}`);
            const nextDot = document.getElementById(`dot-${currentQuestionIndex + 1}`);
            
            // Mark current panel as safe
            currentPanel.classList.add('safe');
            
            // Update step indicators
            currentDot.classList.remove('current');
            currentDot.classList.add('completed');
            if (nextDot) nextDot.classList.add('current');
            
            // Move player forward
            player.classList.add('moving');
            player.style.left = (currentQuestionIndex * 110 + 25) + 'px';
            
            // Move to next question
            currentQuestionIndex++;
            
            setTimeout(() => {
                player.classList.remove('moving');
                displayQuestion();
            }, 1200);
        }

        // Fall through broken glass
        function fallThroughGlass() {
            gameOver = true;
            const player = document.getElementById('player');
            const currentPanel = document.getElementById(`panel-${currentQuestionIndex}`);
            
            // Break the glass panel
            currentPanel.classList.add('broken');
            
            // Make player fall
            setTimeout(() => {
                player.classList.add('falling');
            }, 500);
            
            // Show game over screen
            setTimeout(() => {
                document.getElementById('stepsCompleted').textContent = currentQuestionIndex;
                document.getElementById('gameOverScreen').style.display = 'flex';
            }, 2000);
        }

        // Show success screen
        function showSuccess() {
            // Move player to end platform
            const player = document.getElementById('player');
            const endPlatform = document.getElementById('endPlatform');
            
            player.classList.add('moving');
            player.style.left = (totalSteps * 110 + 70) + 'px';
            
            // Mark end platform as reached
            setTimeout(() => {
                endPlatform.classList.add('reached');
                document.getElementById('finalScore').textContent = score + '/' + totalSteps;
                document.getElementById('successScreen').style.display = 'flex';
            }, 1200);
        }

        // Restart challenge
        function restartChallenge() {
            // Reset game state
            currentQuestionIndex = 0;
            score = 0;
            selectedAnswer = null;
            isAnswered = false;
            gameOver = false;
            
            // Hide screens
            document.getElementById('successScreen').style.display = 'none';
            document.getElementById('gameOverScreen').style.display = 'none';
            
            // Reset player position for restart
            const player = document.getElementById('player');
            player.classList.remove('moving', 'falling');
            player.style.left = '-110px';
            player.style.transform = '';
            
            // Reset end platform
            document.getElementById('endPlatform').classList.remove('reached');
            
            // Recreate bridge
            createGlassPanels();
            createStepIndicators();
            displayQuestion();
        }

        // Add keyboard navigation
        document.addEventListener('keydown', function(e) {
            if (gameOver) return;
            
            if (e.key === '1' || e.key.toLowerCase() === 't') {
                if (!isAnswered) selectAnswer(true);
            } else if (e.key === '2' || e.key.toLowerCase() === 'f') {
                if (!isAnswered) selectAnswer(false);
            } else if (e.key === 'Enter' || e.key === ' ') {
                if (isAnswered && document.getElementById('stepForwardBtn').classList.contains('show')) {
                    stepForward();
                }
            }
        });
    </script>
</body>
</html>
