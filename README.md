[index.html](https://github.com/user-attachments/files/31449753/index.html)
<!DOCTYPE html>
<html lang="ko">
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CPS Click Speed Test</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #111827;
      color: white;
      font-family: Arial, sans-serif;
    }

    .container {
      width: 90%;
      max-width: 600px;
      text-align: center;
    }

    h1 {
      font-size: 42px;
      margin-bottom: 10px;
    }

    .subtitle {
      color: #9ca3af;
      margin-bottom: 30px;
    }

    .stats {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 12px;
      margin-bottom: 25px;
    }

    .stat {
      background: #1f2937;
      border-radius: 12px;
      padding: 15px;
    }

    .stat-title {
      font-size: 14px;
      color: #9ca3af;
    }

    .stat-value {
      font-size: 28px;
      font-weight: bold;
      margin-top: 5px;
    }

    #clickButton {
      width: 100%;
      height: 280px;
      border: none;
      border-radius: 20px;
      background: #2563eb;
      color: white;
      font-size: 45px;
      font-weight: bold;
      cursor: pointer;
      transition: transform 0.05s, background 0.2s;
      user-select: none;
    }

    #clickButton:hover {
      background: #3b82f6;
    }

    #clickButton:active {
      transform: scale(0.97);
    }

    #clickButton.disabled {
      background: #374151;
      cursor: not-allowed;
    }

    .controls {
      margin-top: 25px;
    }

    button {
      border: none;
      border-radius: 10px;
      padding: 12px 20px;
      margin: 5px;
      background: #374151;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }

    button:hover {
      background: #4b5563;
    }

    #startButton {
      background: #16a34a;
    }

    #startButton:hover {
      background: #22c55e;
    }

    .result {
      margin-top: 25px;
      min-height: 30px;
      font-size: 22px;
      font-weight: bold;
    }

    .record {
      margin-top: 15px;
      color: #fbbf24;
      font-size: 18px;
    }

    @media (max-width: 600px) {
      h1 {
        font-size: 32px;
      }

      #clickButton {
        height: 220px;
        font-size: 35px;
      }

      .stats {
        grid-template-columns: 1fr;
      }
    }
  </style>
</head>

<body>

  <div class="container">

    <h1>⚡ CPS TEST</h1>
    <div class="subtitle">Clicks Per Second</div>

    <!-- 측정 정보 -->
    <div class="stats">

      <div class="stat">
        <div class="stat-title">CPS</div>
        <div class="stat-value" id="cps">0.00</div>
      </div>

      <div class="stat">
        <div class="stat-title">CLICKS</div>
        <div class="stat-value" id="clicks">0</div>
      </div>

      <div class="stat">
        <div class="stat-title">TIME</div>
        <div class="stat-value" id="time">5.00</div>
      </div>

    </div>

    <!-- 클릭 버튼 -->
    <button id="clickButton" disabled>
      CLICK!
    </button>

    <!-- 조작 버튼 -->
    <div class="controls">

      <button id="startButton">
        START
      </button>

      <button onclick="setTime(1)">
        1 SECOND
      </button>

      <button onclick="setTime(5)">
        5 SECONDS
      </button>

      <button onclick="setTime(10)">
        10 SECONDS
      </button>

      <button onclick="setTime(30)">
        30 SECONDS
      </button>

    </div>

    <div class="result" id="result"></div>

    <div class="record">
      🏆 BEST CPS: <span id="best">0.00</span>
    </div>

  </div>

<script>

  // -------------------------
  // 변수
  // -------------------------

  let clickCount = 0;
  let startTime = 0;
  let timer = null;
  let running = false;

  // 기본 테스트 시간
  let testDuration = 5;

  // 최고 기록 불러오기
  let bestCPS = parseFloat(
    localStorage.getItem("bestCPS")
  ) || 0;

  // HTML 요소
  const clickButton = document.getElementById("clickButton");
  const startButton = document.getElementById("startButton");

  const cpsDisplay = document.getElementById("cps");
  const clickDisplay = document.getElementById("clicks");
  const timeDisplay = document.getElementById("time");
  const resultDisplay = document.getElementById("result");
  const bestDisplay = document.getElementById("best");

  bestDisplay.textContent = bestCPS.toFixed(2);


  // -------------------------
  // 테스트 시간 설정
  // -------------------------

  function setTime(seconds) {

    if (running) return;

    testDuration = seconds;

    timeDisplay.textContent =
      seconds.toFixed(2);

    resultDisplay.textContent = "";
  }


  // -------------------------
  // 테스트 시작
  // -------------------------

  function startTest() {

    // 이미 실행 중이면 무시
    if (running) return;

    clickCount = 0;
    running = true;

    startTime = performance.now();

    clickDisplay.textContent = "0";
    cpsDisplay.textContent = "0.00";

    resultDisplay.textContent = "";

    clickButton.disabled = false;
    clickButton.classList.remove("disabled");

    startButton.textContent = "RUNNING...";

    // 10ms마다 시간 업데이트
    timer = setInterval(updateTest, 10);
  }


  // -------------------------
  // 클릭 처리
  // -------------------------

  clickButton.addEventListener("click", function () {

    if (!running) return;

    clickCount++;

    clickDisplay.textContent = clickCount;

    // 현재 경과 시간
    const elapsed =
      (performance.now() - startTime) / 1000;

    // 현재 CPS 계산
    if (elapsed > 0) {

      const currentCPS =
        clickCount / elapsed;

      cpsDisplay.textContent =
        currentCPS.toFixed(2);
    }
  });


  // -------------------------
  // 시간 업데이트
  // -------------------------

  function updateTest() {

    const elapsed =
      (performance.now() - startTime) / 1000;

    const remaining =
      testDuration - elapsed;

    // 시간이 끝났으면 종료
    if (remaining <= 0) {

      finishTest();

      return;
    }

    timeDisplay.textContent =
      remaining.toFixed(2);

    // 실시간 CPS
    if (elapsed > 0) {

      const currentCPS =
        clickCount / elapsed;

      cpsDisplay.textContent =
        currentCPS.toFixed(2);
    }
  }


  // -------------------------
  // 테스트 종료
  // -------------------------

  function finishTest() {

    clearInterval(timer);

    running = false;

    clickButton.disabled = true;
    clickButton.classList.add("disabled");

    startButton.textContent = "START";

    timeDisplay.textContent =
      "0.00";

    // 최종 CPS
    const cps =
      clickCount / testDuration;

    cpsDisplay.textContent =
      cps.toFixed(2);

    resultDisplay.innerHTML =
      `🎉 테스트 종료!<br>
       ${clickCount}번 클릭 / ${testDuration}초
       <br>
       최종 CPS: ${cps.toFixed(2)}`;

    // 최고 기록 갱신
    if (cps > bestCPS) {

      bestCPS = cps;

      localStorage.setItem(
        "bestCPS",
        bestCPS
      );

      bestDisplay.textContent =
        bestCPS.toFixed(2);

      resultDisplay.innerHTML +=
        `<br>🏆 NEW RECORD!`;
    }
  }


  // -------------------------
  // START 버튼
  // -------------------------

  startButton.addEventListener(
    "click",
    startTest
  );


  // -------------------------
  // R 키로 초기화
  // -------------------------

  document.addEventListener(
    "keydown",
    function(event) {

      if (
        event.key.toLowerCase() === "r" &&
        !running
      ) {

        clickCount = 0;

        clickDisplay.textContent = "0";
        cpsDisplay.textContent = "0.00";

        timeDisplay.textContent =
          testDuration.toFixed(2);

        resultDisplay.textContent = "";
      }

    }
  );

</script>

</body>
</html>
