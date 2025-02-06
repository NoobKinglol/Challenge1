<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>情侣默契挑战</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-image: url('https://images.unsplash.com/photo-1531297484001-80022131f5a1?ixlib=rb-1.2.1&auto=format&fit=crop&w=1920&q=80');
            background-size: cover;
            background-position: center;
            color: #fff;
            text-align: center;
            padding: 20px;
            margin: 0;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
            position: relative;
            transition: background-color 0.5s;
        }
        .container {
            max-width: 600px;
            background-color: rgba(0, 0, 0, 0.7);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.3);
            position: relative;
            z-index: 2;
        }
        h1 {
            color: #ff6f61;
            font-size: 2.5em;
            margin-bottom: 20px;
        }
        .question {
            font-size: 1.5em;
            margin: 20px 0;
            color: #fff;
        }
        .hint {
            color: #ccc;
            font-size: 1em;
            margin: 10px 0;
        }
        .input-box {
            margin: 20px 0;
        }
        .input-box input {
            width: 80%;
            padding: 10px;
            font-size: 1em;
            border: 1px solid #ccc;
            border-radius: 5px;
            background-color: rgba(255, 255, 255, 0.8);
        }
        .button {
            background-color: #ff6f61;
            color: #fff;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
            transition: background-color 0.3s;
        }
        .button:hover {
            background-color: #ff4a3d;
        }
        .score {
            font-size: 1.5em;
            margin: 20px 0;
            color: #ff6f61;
        }
        .reward {
            font-size: 2em;
            margin: 20px 0;
            color: #ff6f61;
        }
        .timer {
            font-size: 1.5em;
            color: #ff6f61;
            margin: 10px 0;
        }
        .results {
            text-align: left;
            margin-top: 20px;
            max-height: 300px;
            overflow-y: auto;
            background-color: rgba(255, 255, 255, 0.1);
            padding: 10px;
            border-radius: 5px;
        }
        .results div {
            margin: 10px 0;
        }
        /* 爱心特效 */
        .heart {
            position: absolute;
            top: -10%;
            font-size: 20px;
            color: #ff6f61;
            animation: fall 5s linear infinite;
            z-index: 1;
        }
        @keyframes fall {
            0% { top: -10%; transform: translateX(0); }
            100% { top: 110%; transform: translateX(50px); }
        }
        /* 火焰特效 */
        .fire {
            position: absolute;
            top: 50%;
            left: 50%;
            font-size: 40px;
            color: #ff4500;
            animation: burn 0.5s linear;
            z-index: 3;
        }
        @keyframes burn {
            0% { transform: scale(1); opacity: 1; }
            100% { transform: scale(3); opacity: 0; }
        }
        /* Buff激活特效 */
        .buff-active {
            background: linear-gradient(45deg, #ff9a9e, #fad0c4);
        }
        .buff-status {
            font-size: 1.2em;
            color: #ff69b4;
            margin: 10px 0;
            text-shadow: 0 0 10px #ff69b4;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>💖情侣默契挑战💖</h1>
        <div id="game">
            <div class="question" id="question"></div>
            <div class="hint" id="hint"></div>
            <div class="timer" id="timer">剩余时间：30秒</div>
            <div class="input-box">
                <input type="text" id="answer" placeholder="请输入你的答案..." />
            </div>
            <button class="button" id="next-btn">下一题</button>
            <div class="score" id="score">得分：0</div>
            <div class="buff-status" id="buff-status"></div>
            <div class="reward" id="reward"></div>
            <div class="results" id="results" style="display: none;"></div>
        </div>
    </div>

    <!-- 爱心特效 -->
    <div id="hearts-container"></div>

    <script>
        const questions = [
            { question: "你第一次见到我时，心里在想什么？", hint: "提示：可以是搞笑或真实的感受" },
            { question: "如果我们可以交换身份一天，你最想做什么？", hint: "提示：可以是搞笑或认真的计划" },
            { question: "你觉得我最像哪种动物？为什么？", hint: "提示：可以是可爱的、搞笑的或奇怪的" },
            { question: "如果我们一起去旅行，你最想去哪里？", hint: "提示：可以是浪漫的、冒险的或搞笑的" },
            { question: "你最喜欢我做的哪件事？", hint: "提示：可以是日常小事或特别的惊喜" },
            { question: "如果我们养宠物，你最想养什么？", hint: "提示：可以是猫、狗、兔子或其他奇怪的动物" },
            { question: "你觉得我最适合穿什么风格的衣服？", hint: "提示：可以是正式的、休闲的或搞笑的" },
            { question: "如果我们一起拍电影，你希望是什么类型的？", hint: "提示：可以是爱情片、喜剧片或动作片" },
            { question: "你最喜欢我们之间的哪个小习惯？", hint: "提示：可以是搞笑的、温馨的或奇怪的" },
            { question: "如果我可以瞬间学会一项技能，你希望是什么？", hint: "提示：可以是实用的、搞笑的或浪漫的" },
            { question: "你觉得我们之间最搞笑的一次经历是什么？", hint: "提示：可以是约会、旅行或日常生活中的趣事" },
            { question: "如果我们一起开一家店，你希望是什么类型的？", hint: "提示：可以是餐厅、书店或其他有趣的想法" },
            { question: "你最喜欢我做的哪道菜？", hint: "提示：可以是真实的或搞笑的（比如泡面）" },
            { question: "如果我们可以穿越到过去或未来，你希望去哪里？", hint: "提示：可以是浪漫的、搞笑的或科幻的" },
            { question: "你觉得我们之间最浪漫的时刻是什么？", hint: "提示：可以是约会、旅行或日常生活中的小事" },
            { question: "如果我们一起参加一个比赛，你希望是什么类型的？", hint: "提示：可以是运动、游戏或其他有趣的活动" },
            { question: "你最喜欢我送的哪件礼物？", hint: "提示：可以是实用的、搞笑的或浪漫的" },
            { question: "如果我们可以变成超级英雄，你希望我们有什么超能力？", hint: "提示：可以是搞笑的、浪漫的或实用的" },
            { question: "你觉得我们之间最默契的一件事是什么？", hint: "提示：可以是日常生活中的小事或特别的经历" },
            { question: "如果我们一起写一本书，你希望是什么类型的？", hint: "提示：可以是爱情小说、科幻小说或其他有趣的想法" },
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let timeLeft = 30;
        let timer;
        const userAnswers = [];
        let gameState = {
            combo: 0,
            isBuffActive: false,
            doubleScore: false
        };

        const questionElement = document.getElementById("question");
        const hintElement = document.getElementById("hint");
        const timerElement = document.getElementById("timer");
        const answerInput = document.getElementById("answer");
        const nextButton = document.getElementById("next-btn");
        const scoreElement = document.getElementById("score");
        const rewardElement = document.getElementById("reward");
        const resultsElement = document.getElementById("results");
        const buffStatusElement = document.getElementById("buff-status");
        const heartsContainer = document.getElementById("hearts-container");

        // 爱心特效
        function createHearts() {
            const heart = document.createElement("div");
            heart.className = "heart";
            heart.innerHTML = "❤️";
            heart.style.left = Math.random() * 100 + "vw";
            heart.style.animationDuration = Math.random() * 3 + 2 + "s";
            heartsContainer.appendChild(heart);
            setTimeout(() => heart.remove(), 5000);
        }

        // 火焰特效
        function createFire() {
            const fire = document.createElement("div");
            fire.className = "fire";
            fire.innerHTML = "🔥";
            fire.style.left = Math.random() * 50 + 25 + "%";
            fire.style.top = Math.random() * 50 + 25 + "%";
            document.body.appendChild(fire);
            setTimeout(() => fire.remove(), 500);
        }

        // 更新分数
        function updateScore(basePoints) {
            // 爱心加成
            const hasHeart = answerInput.value.includes('❤') || answerInput.value.includes('❤️');
            let finalPoints = basePoints + (hasHeart ? 1 : 0);

            // Buff加成
            if (gameState.doubleScore) {
                finalPoints *= 2;
                buffStatusElement.textContent = "💞 情侣Buff生效中！";
            }

            // 连击系统
            if (basePoints === 5) {
                gameState.combo++;
                if (gameState.combo === 3) {
                    gameState.doubleScore = true;
                    document.body.classList.add("buff-active");
                    alert("✨ 情侣Buff激活！分数翻倍！");
                }
            } else {
                gameState.combo = 0;
                gameState.doubleScore = false;
                document.body.classList.remove("buff-active");
                buffStatusElement.textContent = "";
            }

            score = Math.min(score + finalPoints, 100);
            scoreElement.textContent = `得分：${score}`;
            if (finalPoints > 0) {
                createHearts();
            } else {
                createFire();
            }
        }

        // 加载题目
        function loadQuestion() {
            if (currentQuestionIndex < questions.length) {
                questionElement.textContent = questions[currentQuestionIndex].question;
                hintElement.textContent = questions[currentQuestionIndex].hint;
                answerInput.value = "";
                timeLeft = 30;
                timerElement.textContent = `剩余时间：${timeLeft}秒`;
                startTimer();
            } else {
                endGame();
            }
        }

        // 计时器
        function startTimer() {
            clearInterval(timer);
            timer = setInterval(() => {
                timeLeft--;
                timerElement.textContent = `剩余时间：${timeLeft}秒`;
                if (timeLeft <= 0) {
                    clearInterval(timer);
                    alert("⏰ 时间到！扣1分！");
                    createFire();
                    updateScore(-1);
                    userAnswers.push({ question: questions[currentQuestionIndex].question, answer: "超时" });
                    nextQuestion();
                }
            }, 1000);
        }

        // 下一题
        function nextQuestion() {
            currentQuestionIndex++;
            if (currentQuestionIndex < questions.length) {
                loadQuestion();
            } else {
                endGame();
            }
        }

        // 结束游戏
        function endGame() {
            clearInterval(timer);
            let reward = "";
            if (score >= 95) {
                reward = "💯 逆天默契！解锁成就：灵魂伴侣！奖励三天家务全包+全身按摩💆‍♂️";
            } else if (score >= 80) {
                reward = "😘 心有灵犀！奖励专属定制情书💌+晚安吻套餐";
            } else if (score >= 60) {
                reward = "❤️ 合格伴侣！奖励周末约会通行证🎫";
            } else {
                reward = "⛽️ 革命尚未成功！惩罚给对方洗脚三天🦶";
            }
            rewardElement.innerHTML = reward;

            // 显示详细答题报告
            resultsElement.style.display = "block";
            resultsElement.innerHTML = "<h2>答题结果：</h2>";
            userAnswers.forEach((item, index) => {
                resultsElement.innerHTML += `
                    <div>
                        <strong>第 ${index + 1} 题：</strong>${item.question}<br>
                        <strong>你的答案：</strong>${item.answer}
                    </div>
                `;
            });

            resultsElement.innerHTML += `
                <div>
                    <strong>总分：</strong>100分<br>
                    <strong>你的得分：</strong>${score}分
                </div>
            `;

            nextButton.disabled = true;
        }

        // 下一题按钮事件
        nextButton.addEventListener("click", () => {
            const answer = answerInput.value.trim();
            let points = 0;

            if (answer === "") {
                alert("🚫 跳过题目！扣1分！");
                points = -1;
            } else if (answer.includes("不知道") || answer.toLowerCase().includes("i don't know")) {
                alert("🤷 回答不知道！扣2分！");
                points = -2;
            } else {
                alert("🎉 回答正确！加5分！");
                points = 5;
            }

            updateScore(points);
            userAnswers.push({ question: questions[currentQuestionIndex].question, answer: answer || "跳过" });
            nextQuestion();
        });

        // 初始化游戏
        loadQuestion();
    </script>
</body>
</html>



