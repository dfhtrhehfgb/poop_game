<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>바나나의 똥피하기 대작전 - 무한 가속 스피드업</title>
    <style>
        body {
            background-color: #1a1a1a;
            color: white;
            font-family: 'Arial', sans-serif;
            text-align: center;
            margin: 0;
            padding: 5px;
            user-select: none;
        }
        h1 { font-size: 20px; margin: 4px 0; }
        h3 { margin: 4px 0; font-size: 13px; color: #ffcc00; }
        p { margin: 4px 0; font-size: 12px; }
        .screen { display: none; }
        .active { display: block; }
        
        .btn-container { margin: 8px 0; }
        button {
            padding: 8px 16px;
            font-size: 13px;
            margin: 4px;
            cursor: pointer;
            border: none;
            border-radius: 8px;
            font-weight: bold;
            display: inline-block;
            width: 80%;
            max-width: 220px;
            transition: transform 0.1s, background-color 0.2s;
        }
        button:hover { transform: scale(1.03); }
        .btn-easy { background-color: #4CAF50; color: white; }
        .btn-normal { background-color: #2196F3; color: white; }
        .btn-hard { background-color: #ff9800; color: white; }
        .btn-impossible { background-color: #d32f2f; color: white; }
        .btn-vs { background-color: #9c27b0; color: white; }
        .btn-coop { background-color: #e91e63; color: white; }
        .btn-dict { background-color: #607d8b; color: white; }
        .btn-quit { background-color: #555; color: white; padding: 4px 10px; font-size: 12px; width: auto; margin: 0; }
        .btn-skip { background-color: #777; color: white; margin-top: 5px; }

        #game-board {
            width: 100%;
            max-width: 500px;
            height: 440px;
            background-color: #332211;
            margin: 0 auto;
            border-radius: 10px;
            position: relative;
            overflow: hidden;
            border: 3px solid #654321;
        }
        
        #blackout-overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.95);
            pointer-events: none;
            display: none;
            z-index: 2;
        }

        .hud {
            display: flex;
            justify-content: space-between;
            align-items: center;
            width: 100%;
            max-width: 500px;
            margin: 0 auto;
            padding: 4px 0;
            font-size: 12px;
        }
        
        .player {
            position: absolute;
            bottom: 20px;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 22px;
            transition: background-color 0.2s;
            z-index: 5;
        }
        #player1 { background-color: #ffcc00; }
        #player2 { background-color: #00ffff; }
        
        .reversed { background-color: #ff00ff !important; box-shadow: 0 0 12px #ff00ff; }
        .slowed { background-color: #00bfff !important; }
        .mushroom-buff { background-color: #ff4500 !important; box-shadow: 0 0 12px #ff4500; }

        .poop {
            position: absolute;
            width: 30px;
            height: 30px;
            background-color: #8B4513;
            border-radius: 50% 50% 40% 40%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 16px;
            z-index: 4;
        }

        .boss {
            position: absolute;
            width: 120px;
            height: 120px;
            background-color: #4a154b;
            border: 4px dashed #ff00ff;
            border-radius: 18px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            font-size: 55px;
            z-index: 4;
            box-shadow: 0 0 20px rgba(255,0,255,0.7);
        }
        .boss-hp-bar-container {
            position: absolute;
            top: 6px;
            left: 50%;
            transform: translateX(-50%);
            width: 80%;
            max-width: 360px;
            height: 12px;
            background: #444;
            border-radius: 6px;
            border: 2px solid #222;
            overflow: hidden;
            display: none;
            z-index: 6;
        }
        .boss-hp-fill {
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, #ff4500, #ff0055);
            transition: width 0.1s linear;
        }

        #wave-banner {
            position: absolute;
            top: 40%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 22px;
            font-weight: bold;
            color: #ffeb3b;
            text-shadow: 0 0 10px #000, 0 0 20px #ff0000;
            display: none;
            z-index: 10;
            pointer-events: none;
            text-align: center;
            width: 90%;
        }
        
        .rankings-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 6px;
            width: 100%;
            max-width: 480px;
            margin: 4px auto;
        }
        .ranking-box {
            background: #2d2d2d;
            padding: 5px;
            border-radius: 8px;
        }
        .ranking-item {
            padding: 2px 0;
            border-bottom: 1px solid #444;
            display: flex;
            justify-content: space-between;
            font-size: 11px;
            color: #aaa;
        }

        .dict-container {
            width: 92%;
            max-width: 460px;
            height: 360px;
            overflow-y: auto;
            margin: 6px auto;
            background: #2d2d2d;
            padding: 8px;
            border-radius: 8px;
            text-align: left;
        }
        .dict-item {
            display: flex;
            align-items: flex-start;
            padding: 6px;
            border-bottom: 1px solid #444;
        }
        .dict-item.good-item {
            border-left: 4px solid #4CAF50;
            background-color: rgba(76, 175, 80, 0.08);
        }
        .dict-icon { font-size: 22px; width: 32px; text-align: center; flex-shrink: 0; margin-top: 2px; }
        .dict-info { margin-left: 6px; width: 100%; }
        .dict-info h4 { margin: 0 0 2px 0; font-size: 12px; color: #ffcc00; }
        .dict-item.good-item .dict-info h4 { color: #81c784; }
        .dict-info p { margin: 0 0 2px 0; font-size: 11px; color: #ccc; }
        .dict-tip {
            margin: 2px 0 0 0;
            font-size: 10px;
            color: #ff9800;
            background: #1a1a1a;
            padding: 3px 5px;
            border-radius: 4px;
            border-left: 2px solid #ff9800;
        }
        .dict-item.good-item .dict-tip {
            color: #81c784;
            border-left-color: #81c784;
        }
        .dict-specs {
            font-size: 9px;
            color: #00ffff;
            background: #111;
            padding: 1px 4px;
            border-radius: 3px;
            display: inline-block;
            margin-bottom: 2px;
        }
        
        #input-modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.8);
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 10;
        }
        .modal-content {
            background: #2d2d2d;
            padding: 16px;
            border-radius: 12px;
            border: 2px solid #555;
            width: 80%;
            max-width: 260px;
            text-align: center;
        }
        input[type="text"] {
            padding: 6px;
            font-size: 14px;
            width: 80%;
            margin: 6px 0;
            border-radius: 6px;
            border: none;
            text-align: center;
        }
    </style>
</head>
<body>

    <!-- 메인 화면 -->
    <div id="screen-menu" class="screen active">
        <h1>🐶 강아지의 똥피하기 대작전 - 무한 가속 스피드업</h1>
        <p style="color: #aaa; font-size: 11px; margin-bottom: 4px;">시간이 흐를수록 속도가 폭발적으로 빨라집니다!</p>
        
        <h3 style="color: #4CAF50; margin: 6px 0 3px 0;">👤 1인 플레이</h3>
        <div class="btn-container" style="margin-top: 0;">
            <button class="btn-easy" onclick="showSubMenu('1p')">1인 플레이 시작</button>
        </div>

        <h3 style="color: #e91e63; margin: 6px 0 3px 0;">👥 2인 플레이</h3>
        <div class="btn-container" style="margin-top: 0;">
            <button class="btn-coop" onclick="showSubMenu('2p')">2인 플레이 시작</button>
        </div>

        <div class="btn-container" style="margin-top: 3px;">
            <button class="btn-dict" onclick="openDict()">📖 똥 도감 & 공략법</button>
        </div>

        <h3 style="margin: 6px 0 3px 0; font-size: 12px;">🏆 난이도별 TOP 3 랭킹</h3>
        <div class="rankings-grid">
            <div class="ranking-box">
                <h4 style="margin: 2px 0; font-size: 11px; color: #4CAF50;">🟢 쉬움</h4>
                <div id="ranking-list-쉬움"></div>
            </div>
            <div class="ranking-box">
                <h4 style="margin: 2px 0; font-size: 11px; color: #2196F3;">🔵 보통</h4>
                <div id="ranking-list-보통"></div>
            </div>
            <div class="ranking-box">
                <h4 style="margin: 2px 0; font-size: 11px; color: #ff9800;">🟠 어려움</h4>
                <div id="ranking-list-어려움"></div>
            </div>
            <div class="ranking-box">
                <h4 style="margin: 2px 0; font-size: 11px; color: #d32f2f;">🔥 불가능</h4>
                <div id="ranking-list-불가능"></div>
            </div>
        </div>
        <div style="margin-top: 4px;">
            <div class="ranking-box" style="width: 100%; max-width: 480px; margin: 0 auto; box-sizing: border-box;">
                <h4 style="margin: 2px 0; font-size: 11px; color: #e91e63;">🤝 2인 합동 TOP 3 (합동 난이도별 통합)</h4>
                <div id="ranking-list-합동"></div>
            </div>
        </div>
    </div>

    <!-- 1인 플레이 난이도 선택 서브 화면 -->
    <div id="screen-menu-1p" class="screen">
        <h1>👤 1인 플레이 난이도 선택</h1>
        <div class="btn-container">
            <button class="btn-easy" onclick="startGame('쉬움', 1)">쉬움 모드</button><br>
            <button class="btn-normal" onclick="startGame('보통', 1)">보통 모드</button><br>
            <button class="btn-hard" onclick="startGame('어려움', 1)">어려움 모드</button><br>
            <button class="btn-impossible" onclick="startGame('불가능', 1)">🔥 불가능 모드</button><br>
            <button class="btn-quit" onclick="returnToMenu()" style="width: 80%; max-width: 220px; padding: 8px; font-size: 13px; margin-top: 6px;">뒤로 가기</button>
        </div>
    </div>

    <!-- 2인 플레이 모드 선택 서브 화면 -->
    <div id="screen-menu-2p" class="screen">
        <h1>👥 2인 플레이 모드 선택</h1>
        <div class="btn-container">
            <button class="btn-vs" onclick="startGame('대전', 2)">👥 2인용 대전 모드 (점수 없음)</button><br>
            <button class="btn-coop" onclick="showSubMenu('coop-diff')">🤝 2인용 합동 모드 (난이도 선택)</button><br>
            <button class="btn-quit" onclick="returnToMenu()" style="width: 80%; max-width: 220px; padding: 8px; font-size: 13px; margin-top: 6px;">뒤로 가기</button>
        </div>
    </div>

    <!-- 2인 합동 모드 난이도 선택 서브 화면 -->
    <div id="screen-menu-coop-diff" class="screen">
        <h1>🤝 2인 합동 모드 난이도 선택</h1>
        <div class="btn-container">
            <button class="btn-easy" onclick="startGame('쉬움', 3)">쉬움 합동 모드</button><br>
            <button class="btn-normal" onclick="startGame('보통', 3)">보통 합동 모드</button><br>
            <button class="btn-hard" onclick="startGame('어려움', 3)">어려움 합동 모드</button><br>
            <button class="btn-impossible" onclick="startGame('불가능', 3)">🔥 불가능 합동 모드</button><br>
            <button class="btn-quit" onclick="showSubMenu('2p')" style="width: 80%; max-width: 220px; padding: 8px; font-size: 13px; margin-top: 6px;">뒤로 가기</button>
        </div>
    </div>

    <!-- 똥 도감 & 공략법 화면 -->
    <div id="screen-dict" class="screen">
        <h1>📖 똥 & 아이템 도감 (완벽 공략)</h1>
        <p style="font-size: 11px; color: #81c784; margin-bottom: 2px;">🟩 초록색 테두리로 표시된 항목은 플레이어에게 이로운 좋은 아이템입니다!</p>
        <div class="dict-container" id="dict-list-container"></div>
        <button class="btn-normal" onclick="returnToMenu()" style="width: auto; padding: 6px 16px; margin-top: 4px;">메인으로</button>
    </div>

    <!-- 게임 플레이 화면 -->
    <div id="screen-game" class="screen">
        <div class="hud" id="hud-container">
            <span id="ui-lives">❤️❤️❤️❤️❤️</span>
            <span id="ui-wave" style="color: #ffeb3b; font-weight: bold;">웨이브 1</span>
            <span id="ui-score">점수: 0</span>
            <button class="btn-quit" onclick="quitGame()">나가기</button>
        </div>
        
        <div id="game-board">
            <div id="blackout-overlay"></div>
            <div class="boss-hp-bar-container" id="boss-hp-container">
                <div class="boss-hp-fill" id="boss-hp-fill"></div>
            </div>
            <div id="wave-banner">WAVE 1</div>
            <div id="player1" class="player" style="left: calc(30% - 20px);">🐶</div>
            <div id="player2" class="player" style="left: calc(70% - 20px); display: none;">🐱</div>
        </div>
        <p style="color: #aaa;" id="control-guide">1P: A / D 키로 이동하세요!</p>
    </div>

    <!-- 게임 오버 화면 -->
    <div id="screen-gameover" class="screen">
        <h1 id="gameover-title">💩 게임 오버!</h1>
        <p id="killer-info" style="font-size: 15px; color: #ff6b6b; margin: 6px 0; font-weight: bold;"></p>
        <p id="final-score" style="font-size: 16px; font-weight: bold; margin: 6px 0;"></p>
        <p id="cheer-msg" style="color: #ffeb3b; font-size: 14px; font-weight: bold; margin: 8px 0;"></p>
        <button class="btn-normal" onclick="returnToMenu()" style="width: auto; margin-top: 4px;">메인으로 돌아가기</button>
    </div>

    <!-- 랭킹 등록 모달 -->
    <div id="input-modal">
        <div class="modal-content">
            <h3 style="margin-top: 0;">🎉 기록 달성!</h3>
            <p id="modal-score-text" style="color: #aaa;"></p>
            <input type="text" id="player-name" placeholder="이름을 입력하세요" maxlength="8">
            <br>
            <button class="btn-normal" onclick="saveRanking()" style="width: 100%; padding: 6px; margin: 3px 0;">랭킹 등록</button>
            <button class="btn-skip" onclick="skipRanking()" style="width: 100%; padding: 6px; margin: 3px 0;">건너뛰기</button>
        </div>
    </div>

<script>
    const poopDictionary = [
        { icon: '💩', name: '기본 똥', desc: '가장 흔하고 평범한 똥입니다.', size: '보통 (30px)', speed: '보통', type: 'normal', isGood: false, tip: '정직한 궤도이므로 빈 공간을 살짝 비켜서 피하세요.' },
        { icon: '😜', name: '마구똥', desc: '크기, 속도가 랜덤하게 날뛰는 예측 불허의 똥입니다.', size: '랜덤', speed: '랜덤', type: 'crazy', isGood: false, tip: '근처 자리를 피하고 여유 있게 넓은 간격으로 자리를 잡으세요.' },
        { icon: '💥', name: '폭발 똥', desc: '플레이어 쪽보다 살짝 위에서 터지며 사방으로 기본 똥을 정확히 6개 소환합니다!', size: '보통 (30px)', speed: '보통', type: 'explosive', isGood: false, tip: '터지는 타이밍에 근처에 있으면 파편에 맞으니 미리 멀리 비켜서세요.' },
        { icon: '🌀', name: '회오리 똥', desc: '지그재그 곡선으로 낙하합니다.', size: '보통 (30px)', speed: '보통', type: 'zigzag', isGood: false, tip: '좌우 움직임의 폭을 잘 보고 반대 방향으로 크게 돌아서 피하세요.' },
        { icon: '⚡', name: '번개 똥', desc: '눈 깜짝할 사이에 떨어지는 초고속 똥입니다.', size: '보통 (30px)', speed: '매우 빠름', type: 'fast', isGood: false, tip: '평소에 구석에 몰리지 말고 중앙 주변을 유지해야 반응하기 수월합니다.' },
        { icon: '💫', name: '양방향 커브 똥', desc: '화면 좌우 모서리에서 반대편 모서리 대각선 방향으로 유연하게 곡선을 그리며 교차 낙하합니다.', size: '보통 (30px)', speed: '보통', type: 'curve', isGood: false, tip: '양쪽에서 대각선으로 꺾여 들어오므로 반대편 빈 공간을 미리 파악하세요.' },
        { icon: '💣', name: '폭탄 똥', desc: '맞으면 체력이 무려 2칸이나 깎입니다!', size: '보통 (30px)', speed: '약간 빠름', type: 'bomb', isGood: false, tip: '목숨을 대량으로 깎으므로 최우선으로 눈여겨보고 피해야 합니다.' },
        { icon: '💩', name: '거대 왕똥', desc: '화면을 압도하는 엄청나게 거대하고 듬직한 왕똥입니다!', size: '특대형 (120px)', speed: '느림', type: 'giant', isGood: false, tip: '덩치가 매우 크므로 미리 좌우 중 빈 곳을 파악해 자리를 선점하세요.' },
        { icon: '💖', name: '회복 하트', desc: '체력을 1칸 회복해 줍니다 (최대 5칸 제한).', size: '작음 (26px)', speed: '보통', type: 'heal', isGood: true, tip: '위험을 감수하더라도 체력이 부족하다면 최우선으로 챙기세요.' },
        { icon: '🔄', name: '반전 똥', desc: '3초 동안 좌우 이동 키를 반대로 뒤집습니다.', size: '보통 (30px)', speed: '보통', type: 'reverse', isGood: false, tip: '당황하지 말고 반대 키를 누르거나 손을 떼고 잠시 이동을 멈추세요.' },
        { icon: '❄️', name: '얼음 똥', desc: '3초 동안 이동 속도를 느리게 만듭니다.', size: '보통 (30px)', speed: '보통', type: 'ice', isGood: false, tip: '맞은 직후에는 최대한 무리한 움직임을 피하고 안전지대로 가세요.' },
        { icon: '👻', name: '유령 똥', desc: '0.5초마다 보였다가 사라지는 기묘한 똥입니다.', size: '보통 (30px)', speed: '보통', type: 'ghost', isGood: false, tip: '처음 떨어질 때의 위치를 잘 기억해두고 그라인더처럼 지나가세요.' },
        { icon: '🦠', name: '분열 똥', desc: '플레이어 쪽보다 살짝 위에서 양옆으로 작은 분열 똥들을 추가로 생성합니다.', size: '보통 (30px)', speed: '약간 빠름', type: 'split', isGood: false, tip: '분열하기 전에 미리 옆 라인으로 비켜서서 다중 피격을 차단하세요.' },
        { icon: '🍄', name: '독버섯 똥', desc: '먹으면 3초 동안 화면의 다른 모든 똥들이 유령 똥처럼 깜빡입니다!', size: '보통 (30px)', speed: '보통', type: 'mushroom', isGood: false, tip: '주변의 모든 똥이 투명해지므로 직전 위치를 기억하는 시각적 암기력이 필수입니다.' },
        { icon: '🧲', name: '자석 똥', desc: '플레이어 쪽으로 살짝 유도되는 위험한 똥입니다.', size: '보통 (30px)', speed: '보통', type: 'magnet', isGood: false, tip: '가만히 서 있으면 추격해 오니 확실하게 무빙을 쳐서 유도각을 따돌리세요.' },
        { icon: '🌈', name: '무지개 똥', desc: '알록달록 무지개빛을 뿜어내며 추가 점수를 줍니다!', size: '작음 (26px)', speed: '보통', type: 'rainbow', isGood: true, tip: '주변이 안전하다면 무조건 획득해 점수를 쭉쭉 올리세요.' },
        { icon: '💸', name: '점수 강탈 똥', desc: '닿으면 목숨 대신 지금까지 모은 점수를 왕창 빼앗아 갑니다!', size: '보통 (30px)', speed: '보통', type: 'steal', isGood: false, tip: '하트나 버섯과 아이콘이 헷갈리지 않도록 주의하며 피하세요.' },
        { icon: '🧹', name: '황금 빗자루', desc: '화면의 모든 똥을 말끔하게 쓸어버리는 특급 청소 아이템입니다!', size: '작음 (26px)', speed: '보통', type: 'sweep', isGood: true, tip: '위험한 위기 순간에 먹으면 화면이 깨끗하게 정리되며 보너스 점수를 얻습니다.' },
        
        { icon: '🌑', name: '일식(블랙아웃) 똥', desc: '화면 전체가 암흑으로 변해 3초간 코앞의 똥만 간신히 볼 수 있습니다.', size: '보통 (30px)', speed: '보통', type: 'blackout', isGood: false, tip: '시야가 가려지므로 똥이 떨어지기 전 위치를 미리 예측하고 움직이세요.' },
        { icon: '🐧', name: '펭귄 미끄럼 똥', desc: '3초 동안 키를 떼도 관성 때문에 얼음판 위처럼 미끄러져 이동합니다.', size: '보통 (30px)', speed: '보통', type: 'slip', isGood: false, tip: '멈추고 싶은 위치보다 미리 손을 떼어 관성 거리를 계산해야 합니다.' },
        { icon: '💰', name: '세금 징수 똥', desc: '닿으면 지금까지 모은 점수의 정확히 30%를 강제로 압수당합니다!', size: '보통 (30px)', speed: '보통', type: 'tax', isGood: false, tip: '목숨은 안 깎이지만 점수가 팍 깎이니 악착같이 피해야 합니다.' },
        { icon: '🎭', name: '가면 무도회 똥', desc: '3초 동안 모든 똥들의 모양이 전부 똑같은 똥(💩)으로 변합니다.', size: '보통 (30px)', speed: '보통', type: 'mask', isGood: false, tip: '진짜 하트나 좋은 아이템도 전부 기본 똥으로 보여 구분이 불가능해집니다.' },

        { icon: '👹', name: '중간보스 (대장 똥)', desc: '웨이브 5 클리어 후 등장하는 위협적인 중간보스입니다!', size: '특대형', speed: '보통', type: 'mid_boss', isGood: false, tip: '구석 꼼수를 쓸 수 없도록 자석 똥과 저격 번개 폭격을 쓰니 계속 움직이세요.' },
        { icon: '👑', name: '최종보스 (마왕 똥)', desc: '웨이브 15 클리어 후 등장하는 게임의 최종 지배자입니다!', size: '특대형', speed: '빠름', type: 'final_boss', isGood: false, tip: '폭발 똥과 랜덤 저격 폭격을 피하며 아래쪽 빈틈을 찾으세요.' }
    ];

    function initDictUI() {
        let container = document.getElementById('dict-list-container');
        container.innerHTML = '';
        poopDictionary.forEach((item, index) => {
            let div = document.createElement('div');
            div.classList.add('dict-item');
            if (item.isGood) div.classList.add('good-item');
            div.innerHTML = `
                <div class="dict-icon">${item.icon}</div>
                <div class="dict-info">
                    <h4>${index + 1}. ${item.name} ${item.isGood ? '✨ (좋은 아이템)' : ''}</h4>
                    <p>${item.desc}</p>
                    <div class="dict-specs">크기: ${item.size} | 속도: ${item.speed}</div>
                    <div class="dict-tip">💡 공략: ${item.tip}</div>
                </div>
            `;
            container.appendChild(div);
        });
    }
    initDictUI();

    function showSubMenu(type) {
        document.getElementById('screen-menu').classList.remove('active');
        document.getElementById('screen-menu-1p').classList.remove('active');
        document.getElementById('screen-menu-2p').classList.remove('active');
        document.getElementById('screen-menu-coop-diff').classList.remove('active');

        if (type === '1p') {
            document.getElementById('screen-menu-1p').classList.add('active');
        } else if (type === '2p') {
            document.getElementById('screen-menu-2p').classList.add('active');
        } else if (type === 'coop-diff') {
            document.getElementById('screen-menu-coop-diff').classList.add('active');
        }
    }

    function openDict() {
        document.getElementById('screen-menu').classList.remove('active');
        document.getElementById('screen-dict').classList.add('active');
    }

    let audioCtx = null;
    let bgmTimer = null;
    let bgmStep = 0;

    function initAudio() {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (audioCtx.state === 'suspended') audioCtx.resume();
    }

    function playHitSound() {
        if (!audioCtx) return;
        let osc = audioCtx.createOscillator();
        let gain = audioCtx.createGain();
        osc.type = 'sawtooth';
        osc.frequency.setValueAtTime(150, audioCtx.currentTime);
        osc.frequency.exponentialRampToValueAtTime(40, audioCtx.currentTime + 0.15);
        gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
        gain.gain.linearRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.15);
    }

    function playItemSound(isGood) {
        if (!audioCtx) return;
        let osc = audioCtx.createOscillator();
        let gain = audioCtx.createGain();
        osc.type = 'sine';
        if (isGood) {
            osc.frequency.setValueAtTime(300, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(600, audioCtx.currentTime + 0.15);
        } else {
            osc.frequency.setValueAtTime(200, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(80, audioCtx.currentTime + 0.15);
        }
        gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
        gain.gain.linearRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.15);
    }

    function startBGM() {
        stopBGM();
        bgmStep = 0;
        playBGMStep();
    }

    function playBGMStep() {
        if (!document.getElementById('screen-game').classList.contains('active')) return;
        if (audioCtx) {
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            const notes = [261.63, 293.66, 329.63, 349.23, 392.00, 440.00];
            osc.frequency.value = notes[bgmStep % notes.length];
            osc.type = 'triangle';
            gain.gain.setValueAtTime(0.08, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.12);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.12);
            bgmStep++;
        }
        bgmTimer = setTimeout(playBGMStep, Math.max(150, 350 - (currentWave * 10)));
    }

    function stopBGM() {
        if (bgmTimer) {
            clearTimeout(bgmTimer);
            bgmTimer = null;
        }
    }

    let gameMode = 1;
    let currentDifficultyKey = '보통';
    let score = 0;
    let scoreAcc = 0;
    let sharedLives = 5;
    let p1Lives = 5;
    let p2Lives = 5;
    let poops = [];
    let gameLoopId = null;
    let p1X = 140;
    let p2X = 320;
    let boardWidth = 500;
    let boardHeight = 440;
    let lastKiller = { name: '기본 똥', icon: '💩' };
    
    let spawnTimer = 0;
    let baseDifficultySpawnRate = 1200;
    let currentSpawnRate = 1200;

    let currentWave = 1;
    let waveProgressTime = 0;
    let isBossActive = false;
    let bossObj = null;

    let p1Reversed = false, p1Slowed = false, p1Slip = false;
    let p2Reversed = false, p2Slowed = false, p2Slip = false;
    let p1VelocityX = 0, p2VelocityX = 0;
    let isMushroomActive = false;
    let isBlackoutActive = false;
    let isMaskActive = false;

    let p1Timers = { rev: null, slow: null, slip: null };
    let p2Timers = { rev: null, slow: null, slip: null };
    let mushroomTimer = null;
    let blackoutTimer = null;
    let maskTimer = null;

    let p1Left = false, p1Right = false;
    let p2Left = false, p2Right = false;

    function getRankings(key) {
        let saved = localStorage.getItem(key);
        return saved ? JSON.parse(saved) : [];
    }

    function displayRankings() {
        const diffs = ['쉬움', '보통', '어려움', '불가능'];
        diffs.forEach(diff => {
            let container = document.getElementById(`ranking-list-${diff}`);
            if (!container) return;
            let rankings = getRankings(`poop_rankings_${diff}_v31`);
            container.innerHTML = '';
            if (rankings.length === 0) {
                container.innerHTML = '<div class="ranking-item" style="justify-content: center;">기록 없음</div>';
            } else {
                rankings.forEach((item, index) => {
                    let div = document.createElement('div');
                    div.classList.add('ranking-item');
                    div.innerHTML = `<span>${index + 1}.${item.name}</span><span>${item.score}점</span>`;
                    container.appendChild(div);
                });
            }
        });

        let coopContainer = document.getElementById('ranking-list-합동');
        if (coopContainer) {
            let coopRankings = getRankings('poop_rankings_합동_v31');
            coopContainer.innerHTML = '';
            if (coopRankings.length === 0) {
                coopContainer.innerHTML = '<div class="ranking-item" style="justify-content: center;">기록 없음</div>';
            } else {
                coopRankings.forEach((item, index) => {
                    let div = document.createElement('div');
                    div.classList.add('ranking-item');
                    div.innerHTML = `<span>${index + 1}.${item.name}</span><span>${item.score}점</span>`;
                    coopContainer.appendChild(div);
                });
            }
        }
    }
    displayRankings();

    function startGame(difficulty, mode) {
        initAudio();
        gameMode = mode;
        currentDifficultyKey = difficulty;
        score = 0;
        scoreAcc = 0;

        if (gameMode === 2) {
            p1Lives = 5;
            p2Lives = 5;
        } else if (gameMode === 3) {
            sharedLives = 5;
        } else {
            p1Lives = 5;
        }

        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];
        if (bossObj && bossObj.element) {
            bossObj.element.remove();
            bossObj = null;
        }
        document.getElementById('boss-hp-container').style.display = 'none';

        p1X = 140;
        p2X = 320;
        p1VelocityX = 0;
        p2VelocityX = 0;
        spawnTimer = 0;
        currentWave = 1;
        waveProgressTime = 0;
        isBossActive = false;
        
        p1Reversed = false; p1Slowed = false; p1Slip = false;
        p2Reversed = false; p2Slowed = false; p2Slip = false;
        isMushroomActive = false;
        isBlackoutActive = false;
        isMaskActive = false;
        document.getElementById('blackout-overlay').style.display = 'none';

        clearAllTimers();
        
        let p1Elem = document.getElementById('player1');
        let p2Elem = document.getElementById('player2');
        p1Elem.className = 'player';
        p2Elem.className = 'player';

        if (gameMode === 2) {
            p2Elem.style.display = 'flex';
            document.getElementById('control-guide').innerText = '👥 대전 모드! 1P(A/D) vs 2P(방향키) - 시간 지날수록 가속!';
            baseDifficultySpawnRate = 1000;
        } else if (gameMode === 3) {
            p2Elem.style.display = 'flex';
            document.getElementById('control-guide').innerText = `🤝 합동 모드(${difficulty})! 1P(A/D), 2P(방향키) 목숨 5개 공유 - 무한 가속!`;
            if (difficulty === '쉬움') baseDifficultySpawnRate = 1500;
            else if (difficulty === '보통') baseDifficultySpawnRate = 1200;
            else if (difficulty === '어려움') baseDifficultySpawnRate = 900;
            else if (difficulty === '불가능') baseDifficultySpawnRate = 350;
        } else {
            p2Elem.style.display = 'none';
            document.getElementById('control-guide').innerText = '1P: A/D 또는 좌우 방향키 - 시간이 갈수록 엄청 빨라집니다!';
            if (difficulty === '쉬움') baseDifficultySpawnRate = 1500;
            else if (difficulty === '보통') baseDifficultySpawnRate = 1200;
            else if (difficulty === '어려움') baseDifficultySpawnRate = 900;
            else if (difficulty === '불가능') baseDifficultySpawnRate = 350;
        }
        currentSpawnRate = baseDifficultySpawnRate;

        document.getElementById('screen-menu').classList.remove('active');
        document.getElementById('screen-menu-1p').classList.remove('active');
        document.getElementById('screen-menu-2p').classList.remove('active');
        document.getElementById('screen-menu-coop-diff').classList.remove('active');
        document.getElementById('screen-gameover').classList.remove('active');
        document.getElementById('screen-dict').classList.remove('active');
        document.getElementById('input-modal').style.display = 'none';
        document.getElementById('screen-game').classList.add('active');
        
        updateHUD();
        showWaveBanner(`웨이브 ${currentWave}`);

        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        lastTime = 0;
        gameLoopId = requestAnimationFrame(updateGame);
        startBGM();
    }

    function showWaveBanner(text) {
        let banner = document.getElementById('wave-banner');
        banner.innerText = text;
        banner.style.display = 'block';
        setTimeout(() => { banner.style.display = 'none'; }, 1800);
    }

    let lastTime = 0;
    function updateGame(timestamp) {
        if (!document.getElementById('screen-game').classList.contains('active')) return;

        if (!lastTime) lastTime = timestamp;
        let dt = timestamp - lastTime;
        lastTime = timestamp;
        if (dt > 100) dt = 16;

        if (gameMode !== 2) {
            scoreAcc += dt;
            if (scoreAcc >= 100) {
                score += Math.floor(scoreAcc / 100);
                scoreAcc %= 100;
            }
        }

        if (!isBossActive && (gameMode === 1 || gameMode === 3)) {
            waveProgressTime += dt;
            if (waveProgressTime >= 12000) {
                waveProgressTime = 0;
                currentWave++;
                currentSpawnRate = Math.max(180, baseDifficultySpawnRate - ((currentWave - 1) * 65));

                if (currentWave === 6) {
                    spawnBoss('mid');
                } else if (currentWave === 16) {
                    spawnBoss('final');
                } else if (currentWave > 16) {
                    showWaveBanner(`🔥 무한 광속 모드 (웨이브 ${currentWave})`);
                } else {
                    showWaveBanner(`⚡ 웨이브 ${currentWave} (속도 증가!)`);
                }
            }
        }

        updateHUD();

        let p1BaseSpeed = p1Slowed ? 3.0 : 6.5;
        let p1ActiveLeft = p1Reversed ? p1Right : p1Left;
        let p1ActiveRight = p1Reversed ? p1Left : p1Right;
        
        if (p1Slip) {
            if (p1ActiveLeft) p1VelocityX -= 0.6;
            if (p1ActiveRight) p1VelocityX += 0.6;
            p1VelocityX = Math.max(Math.min(p1VelocityX, 9.5), -9.5);
            p1VelocityX *= 0.94;
            p1X += p1VelocityX;
        } else {
            let targetSpeed = 0;
            if (p1ActiveLeft) targetSpeed = -p1BaseSpeed;
            if (p1ActiveRight) targetSpeed = p1BaseSpeed;
            p1X += targetSpeed;
        }
        if (p1X < 0) { p1X = 0; p1VelocityX = 0; }
        if (p1X > boardWidth - 40) { p1X = boardWidth - 40; p1VelocityX = 0; }
        document.getElementById('player1').style.left = p1X + 'px';

        if (gameMode === 2 || gameMode === 3) {
            let p2BaseSpeed = p2Slowed ? 3.0 : 6.5;
            let p2ActiveLeft = p2Reversed ? p2Right : p2Left;
            let p2ActiveRight = p2Reversed ? p2Left : p2Right;

            if (p2Slip) {
                if (p2ActiveLeft) p2VelocityX -= 0.6;
                if (p2ActiveRight) p2VelocityX += 0.6;
                p2VelocityX = Math.max(Math.min(p2VelocityX, 9.5), -9.5);
                p2VelocityX *= 0.94;
                p2X += p2VelocityX;
            } else {
                let targetSpeed2 = 0;
                if (p2ActiveLeft) targetSpeed2 = -p2BaseSpeed;
                if (p2ActiveRight) targetSpeed2 = p2BaseSpeed;
                p2X += targetSpeed2;
            }
            if (p2X < 0) { p2X = 0; p2VelocityX = 0; }
            if (p2X > boardWidth - 40) { p2X = boardWidth - 40; p2VelocityX = 0; }
            document.getElementById('player2').style.left = p2X + 'px';
        }

        if (!isBossActive) {
            spawnTimer += dt;
            if (spawnTimer >= currentSpawnRate) {
                spawnPoop();
                spawnTimer = 0;
            }
        } else if (bossObj && (gameMode === 1 || gameMode === 3)) {
            bossObj.x += bossObj.vx;
            if (bossObj.x < 10 || bossObj.x > boardWidth - 130) {
                bossObj.vx *= -1;
            }
            bossObj.element.style.left = bossObj.x + 'px';

            bossObj.attackTimer += dt;
            if (bossObj.attackTimer > Math.max(500, 1200 - (currentWave * 30))) {
                bossObj.attackTimer = 0;
                bossAttackPattern(bossObj);
            }
        }

        for (let i = poops.length - 1; i >= 0; i--) {
            let p = poops[i];
            if (!p || !p.element) {
                poops.splice(i, 1);
                continue;
            }

            let waveMultiplier = 1 + ((currentWave - 1) * 0.12);
            let dropSpeed = (p.speed || 2.0) * waveMultiplier;
            p.y += dropSpeed;

            if (p.y <= 0) {
                p.y = 0;
                p.speed = Math.abs(p.speed);
                if (p.vy) p.vy *= -1;
            }

            if (isMaskActive) {
                p.element.innerText = '💩';
            } else {
                p.element.innerText = p.icon;
            }

            if (isBlackoutActive) {
                let pDist1 = Math.abs((p.x + 15) - (p1X + 20)) + Math.abs((p.y + 15) - (boardHeight - 40));
                let pDist2 = 9999;
                if ((gameMode === 2 && p2Lives > 0) || gameMode === 3) {
                    pDist2 = Math.abs((p.x + 15) - (p2X + 20)) + Math.abs((p.y + 15) - (boardHeight - 40));
                }
                let minDist = Math.min(pDist1, pDist2);

                if (minDist < 110) {
                    p.element.style.opacity = '1';
                } else {
                    p.element.style.opacity = '0';
                }
            } else if (p.movement === 'ghost' || isMushroomActive) {
                let cycleTime = (timestamp + p.timeOffset) % 1000;
                p.element.style.opacity = (cycleTime < 500) ? '1' : '0';
            } else {
                p.element.style.opacity = '1';
            }

            if (p.movement === 'zigzag') {
                p.x += Math.sin(p.y / 15) * 4.5;
            } else if (p.movement === 'crazy') {
                p.crazyVX += (Math.random() - 0.5) * 1.8;
                p.crazyVX = Math.max(Math.min(p.crazyVX, 6.0), -6.0);
                p.x += p.crazyVX;
            } else if (p.movement === 'curve') {
                p.x += p.curveDir * 2.6;
                p.y += dropSpeed * 0.85;
                if (p.vx) p.x += p.vx;
            } else if (p.movement === 'mini') {
                p.x += p.vx;
                p.y += p.vy;
            } else if (p.movement === 'magnet') {
                let targetX = p1X;
                if ((gameMode === 2 && p2Lives > 0) || gameMode === 3) {
                    let dist1 = Math.abs((p.x + 15) - (p1X + 20));
                    let dist2 = Math.abs((p.x + 15) - (p2X + 20));
                    targetX = (dist1 < dist2) ? p1X : p2X;
                }
                if (p.x < targetX) p.x += 2.0;
                else if (p.x > targetX) p.x -= 2.0;
            }

            if (p.type === 'explosive' && !p.hasExploded && p.y > boardHeight - 210) {
                p.hasExploded = true;
                playItemSound(false);
                let expX = p.x;
                let expY = p.y;
                p.element.remove();
                poops.splice(i, 1);

                for (let m = 0; m < 6; m++) {
                    let angle = (m / 6) * Math.PI * 2;
                    spawnDefaultMiniPoop(expX, expY, Math.cos(angle) * 3.5, Math.sin(angle) * 3.5);
                }
                continue;
            }

            if (p.type === 'split' && !p.hasSplit && p.y > boardHeight - 210) {
                p.hasSplit = true;
                spawnSplitChild(p.x - 20, p.y, p.speed, 'curve', '💫', '양방향 커브 똥', -1, -1.5);
                spawnSplitChild(p.x + 20, p.y, p.speed, 'curve', '💫', '양방향 커브 똥', 1, 1.5);
            }

            let pSize = p.size;
            if (p.x < 5) { p.x = 5; if (p.crazyVX) p.crazyVX *= -1; }
            if (p.x > boardWidth - pSize - 5) { p.x = boardWidth - pSize - 5; if (p.crazyVX) p.crazyVX *= -1; }

            p.element.style.top = p.y + 'px';
            p.element.style.left = p.x + 'px';

            if (p.y + pSize >= boardHeight - 55 && p.y <= boardHeight - 15) {
                if (gameMode !== 3 && p1Lives > 0 && p.x + pSize > p1X && p.x < p1X + 40) {
                    let type = p.type;
                    let icon = p.icon;
                    p.element.remove();
                    poops.splice(i, 1);
                    handleCollision(1, type, icon);
                    continue;
                }
                if (gameMode === 2 && p2Lives > 0 && p.x + pSize > p2X && p.x < p2X + 40) {
                    let type = p.type;
                    let icon = p.icon;
                    p.element.remove();
                    poops.splice(i, 1);
                    handleCollision(2, type, icon);
                    continue;
                }
                if (gameMode === 3 && sharedLives > 0) {
                    if (p.x + pSize > p1X && p.x < p1X + 40) {
                        let type = p.type;
                        let icon = p.icon;
                        p.element.remove();
                        poops.splice(i, 1);
                        handleCollision(1, type, icon);
                        continue;
                    } else if (p.x + pSize > p2X && p.x < p2X + 40) {
                        let type = p.type;
                        let icon = p.icon;
                        p.element.remove();
                        poops.splice(i, 1);
                        handleCollision(2, type, icon);
                        continue;
                    }
                }
            }

            if (p.y > boardHeight || p.y < -60 || p.x < -40 || p.x > boardWidth + 40) {
                p.element.remove();
                poops.splice(i, 1);
            }
        }

        if (gameMode === 1 && p1Lives <= 0) {
            endGame('1P 패배');
            return;
        } else if (gameMode === 2) {
            if (p1Lives <= 0 && p2Lives <= 0) {
                endGame('동반 패배');
                return;
            } else if (p1Lives <= 0) {
                endGame('플레이어 2 승리!');
                return;
            } else if (p2Lives <= 0) {
                endGame('플레이어 1 승리!');
                return;
            }
        } else if (gameMode === 3 && sharedLives <= 0) {
            endGame('합동 게임 오버!');
            return;
        }

        gameLoopId = requestAnimationFrame(updateGame);
    }

    function spawnBoss(type) {
        isBossActive = true;
        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];

        const board = document.getElementById('game-board');
        let bossElem = document.createElement('div');
        bossElem.classList.add('boss');

        let isFinal = (type === 'final');
        let bossName = isFinal ? '최종보스 (마왕 똥)' : '중간보스 (대장 똥)';
        let bossIcon = isFinal ? '👑' : '👹';
        let maxHp = isFinal ? 25 : 12;

        bossElem.innerText = bossIcon;
        bossElem.style.top = '30px';
        bossElem.style.left = '190px';
        board.appendChild(bossElem);

        bossObj = {
            element: bossElem, x: 190, y: 30,
            vx: isFinal ? 4.0 : 2.8,
            hp: maxHp, maxHp: maxHp,
            type: type, name: bossName, icon: bossIcon, attackTimer: 0
        };

        document.getElementById('boss-hp-container').style.display = 'block';
        updateBossHPBar();
        showWaveBanner(isFinal ? '🔥 최종보스 광속 등장!' : '⚡ 중간보스 등장!');
    }

    function updateBossHPBar() {
        if (!bossObj) return;
        let pct = (bossObj.hp / bossObj.maxHp) * 100;
        document.getElementById('boss-hp-fill').style.width = Math.max(0, pct) + '%';
    }

    function bossAttackPattern(boss) {
        let spawnX = boss.x + 40;
        let spawnY = boss.y + 110;
        let isFinal = (boss.type === 'final');
        let patternChoice = Math.random();

        if (patternChoice < 0.4) {
            let icon = isFinal ? '💥' : '🧲';
            let type = isFinal ? 'explosive' : 'magnet';
            let movement = isFinal ? 'normal' : 'magnet';

            const board = document.getElementById('game-board');
            let pElem = document.createElement('div');
            pElem.classList.add('poop');
            pElem.innerText = icon;
            pElem.style.top = spawnY + 'px';
            pElem.style.left = spawnX + 'px';
            board.appendChild(pElem);

            poops.push({
                element: pElem, x: spawnX, y: spawnY, icon: icon,
                speed: isFinal ? 3.8 : 3.0, type: type, movement: movement,
                size: 30, curveDir: 0, crazyVX: 0, hasSplit: false, hasExploded: false,
                name: isFinal ? '마왕의 폭발 똥' : '대장의 유도 자석 똥', timeOffset: 0
            });
        } else if (patternChoice < 0.75) {
            for (let i = 0; i < (isFinal ? 3 : 2); i++) {
                let rx = Math.random() * (boardWidth - 40);
                spawnSplitChild(rx, 40, 3.0, 'crazy', '😜', '마구똥', (Math.random() - 0.5) * 2.5, (Math.random() - 0.5) * 2.5);
            }
        } else {
            let targetX = p1X;
            if ((gameMode === 2 && p2Lives > 0) || gameMode === 3) {
                targetX = Math.random() < 0.5 ? p1X : p2X;
            }
            
            const board = document.getElementById('game-board');
            let laserElem = document.createElement('div');
            laserElem.style.position = 'absolute';
            laserElem.style.top = '0px';
            laserElem.style.left = (targetX + 15) + 'px';
            laserElem.style.width = '4px';
            laserElem.style.height = '100%';
            laserElem.style.backgroundColor = 'rgba(255, 0, 0, 0.7)';
            laserElem.style.boxShadow = '0 0 8px #ff0000';
            laserElem.style.zIndex = '3';
            board.appendChild(laserElem);

            setTimeout(() => {
                if (!document.getElementById('screen-game').classList.contains('active')) {
                    laserElem.remove();
                    return;
                }
                laserElem.remove();

                let lightningElem = document.createElement('div');
                lightningElem.classList.add('poop');
                lightningElem.innerText = '⚡';
                lightningElem.style.top = '0px';
                lightningElem.style.left = targetX + 'px';
                board.appendChild(lightningElem);

                poops.push({
                    element: lightningElem, x: targetX, y: 0, icon: '⚡',
                    speed: 7.0, type: 'fast', movement: 'normal',
                    size: 30, curveDir: 0, crazyVX: 0, hasSplit: false, hasExploded: true,
                    name: '보스의 저격 번개 똥', timeOffset: 0
                });
            }, 600);
        }

        boss.hp--;
        updateBossHPBar();

        if (boss.hp <= 0) {
            boss.element.remove();
            let defeatedType = bossObj.type;
            bossObj = null;
            document.getElementById('boss-hp-container').style.display = 'none';
            isBossActive = false;
            score += 300;
            
            if (defeatedType === 'final') {
                showWaveBanner('🏆 마왕 똥 격파 완료!\n초광속 무한 모드 진입!');
                currentWave = 17;
            } else {
                currentWave = 6;
                showWaveBanner('웨이브 6 진입!');
            }
        }
    }

    function spawnPoop() {
        const board = document.getElementById('game-board');
        let poopElem = document.createElement('div');
        poopElem.classList.add('poop');

        let randomIndex = Math.floor(Math.random() * poopDictionary.length);
        let selectedPoop = poopDictionary[randomIndex];

        while (selectedPoop.type === 'mid_boss' || selectedPoop.type === 'final_boss') {
            randomIndex = Math.floor(Math.random() * poopDictionary.length);
            selectedPoop = poopDictionary[randomIndex];
        }

        let icon = selectedPoop.icon;
        poopElem.innerText = icon;
        
        let poopType = selectedPoop.type;
        let movement = 'normal';
        let speed = 2.0 + Math.random() * 1.2;
        let pSize = 30;
        let curveDir = 0, crazyVX = 0;
        let randomX = 20 + Math.random() * (boardWidth - 80);
        let startY = 0;

        if (poopType === 'crazy') {
            movement = 'crazy';
            pSize = Math.floor(40 + Math.random() * 22);
            speed = 1.6 + Math.random() * 2.5;
            crazyVX = (Math.random() - 0.5) * 7.0;
            poopElem.style.width = pSize + 'px';
            poopElem.style.height = pSize + 'px';
            poopElem.style.fontSize = (pSize * 0.55) + 'px';
        } else if (poopType === 'explosive') {
            movement = 'explosive';
        } else if (poopType === 'zigzag') {
            movement = 'zigzag';
        } else if (poopType === 'fast') {
            speed *= 1.8;
        } else if (poopType === 'curve') {
            movement = 'curve';
            startY = 10;
            if (Math.random() < 0.5) {
                randomX = 0;
                curveDir = 1.8;
            } else {
                randomX = boardWidth - 40;
                curveDir = -1.8;
            }
        } else if (poopType === 'giant') {
            pSize = 120;
            speed *= 0.5;
            poopElem.style.width = '120px';
            poopElem.style.height = '120px';
            poopElem.style.fontSize = '68px';
        } else if (poopType === 'ghost') {
            movement = 'ghost';
        } else if (poopType === 'magnet') {
            movement = 'magnet';
        } else if (poopType === 'split') {
            movement = 'split';
        }

        poopElem.style.top = startY + 'px';
        poopElem.style.left = randomX + 'px';
        board.appendChild(poopElem);

        poops.push({ 
            element: poopElem, x: randomX, y: startY, icon: icon,
            speed: speed, type: poopType, movement: movement,
            size: pSize, curveDir: curveDir, crazyVX: crazyVX,
            hasSplit: false, hasExploded: false,
            name: selectedPoop.name, timeOffset: Math.random() * 1000
        });
    }

    function spawnSplitChild(x, y, speed, type, icon, name, curveDir, vx) {
        const board = document.getElementById('game-board');
        let childElem = document.createElement('div');
        childElem.classList.add('poop');
        childElem.innerText = icon;
        x = Math.min(Math.max(x, 10), boardWidth - 40);
        childElem.style.top = y + 'px';
        childElem.style.left = x + 'px';
        board.appendChild(childElem);

        poops.push({
            element: childElem, x: x, y: y, icon: icon,
            speed: speed * 1.2, type: type, movement: 'curve',
            size: 30, curveDir: curveDir, vx: vx, crazyVX: 0,
            hasSplit: true, hasExploded: true, name: name, timeOffset: Math.random() * 1000
        });
    }

    function spawnDefaultMiniPoop(x, y, vx, vy) {
        const board = document.getElementById('game-board');
        let miniElem = document.createElement('div');
        miniElem.classList.add('poop');
        miniElem.innerText = '💩';
        miniElem.style.width = '22px';
        miniElem.style.height = '22px';
        miniElem.style.fontSize = '13px';
        x = Math.min(Math.max(x, 5), boardWidth - 25);
        y = Math.min(Math.max(y, 5), boardHeight - 25);
        miniElem.style.top = y + 'px';
        miniElem.style.left = x + 'px';
        board.appendChild(miniElem);

        poops.push({
            element: miniElem, x: x, y: y, icon: '💩',
            speed: 1.4, type: 'normal', movement: 'mini',
            size: 22, curveDir: 0, vx: vx, vy: vy,
            hasSplit: true, hasExploded: true, name: '기본 똥', timeOffset: 0
        });
    }

    function handleCollision(playerNum, type, icon) {
        let matchedDict = poopDictionary.find(p => p.icon === icon);
        lastKiller = { name: matchedDict ? matchedDict.name : '알 수 없는 똥', icon: icon };

        if (type === 'heal') {
            playItemSound(true);
            if (gameMode === 3) {
                if (sharedLives < 5) sharedLives++;
            } else {
                if (playerNum === 1 && p1Lives < 5) p1Lives++;
                if (playerNum === 2 && p2Lives < 5) p2Lives++;
            }
            updateHUD();
            return;
        }

        if (type === 'sweep') {
            playItemSound(true);
            poops.forEach(p => { if (p && p.element) p.element.remove(); });
            poops = [];
            if (gameMode !== 2) score += 100;
            
            let banner = document.getElementById('wave-banner');
            banner.innerText = '🧹 화면 청소 완료!';
            banner.style.display = 'block';
            setTimeout(() => { banner.style.display = 'none'; }, 900);

            updateHUD();
            return;
        }

        if (type === 'mushroom') {
            playItemSound(true);
            isMushroomActive = true;
            let pElem = document.getElementById(`player${playerNum}`);
            if (pElem) pElem.classList.add('mushroom-buff');
            if (mushroomTimer) clearTimeout(mushroomTimer);
            mushroomTimer = setTimeout(() => {
                isMushroomActive = false;
                let p1 = document.getElementById('player1');
                let p2 = document.getElementById('player2');
                if (p1) p1.classList.remove('mushroom-buff');
                if (p2) p2.classList.remove('mushroom-buff');
            }, 3000);
            return;
        }

        if (type === 'rainbow') {
            playItemSound(true);
            if (gameMode !== 2) score += 50;
            updateHUD();
            return;
        }

        if (type === 'steal') {
            playHitSound();
            if (gameMode !== 2) score = Math.max(0, score - 50);
            updateHUD();
            return;
        }

        if (type === 'blackout') {
            playItemSound(false);
            isBlackoutActive = true;
            document.getElementById('blackout-overlay').style.display = 'block';
            if (blackoutTimer) clearTimeout(blackoutTimer);
            blackoutTimer = setTimeout(() => {
                isBlackoutActive = false;
                document.getElementById('blackout-overlay').style.display = 'none';
            }, 3000);
            return;
        }

        if (type === 'slip') {
            playItemSound(false);
            if (playerNum === 1) {
                p1Slip = true;
                if (p1Timers.slip) clearTimeout(p1Timers.slip);
                p1Timers.slip = setTimeout(() => { p1Slip = false; p1VelocityX = 0; }, 3000);
            } else {
                p2Slip = true;
                if (p2Timers.slip) clearTimeout(p2Timers.slip);
                p2Timers.slip = setTimeout(() => { p2Slip = false; p2VelocityX = 0; }, 3000);
            }
            return;
        }

        if (type === 'tax') {
            playHitSound();
            if (gameMode !== 2) {
                score = Math.floor(score * 0.7);
            }
            updateHUD();
            return;
        }

        if (type === 'mask') {
            playItemSound(false);
            isMaskActive = true;
            if (maskTimer) clearTimeout(maskTimer);
            maskTimer = setTimeout(() => {
                isMaskActive = false;
            }, 3000);
            return;
        }

        if (type === 'bomb') {
            playHitSound();
            if (gameMode === 3) {
                sharedLives -= 2;
            } else {
                if (playerNum === 1) p1Lives -= 2;
                else p2Lives -= 2;
            }
        } else if (type === 'reverse') {
            playItemSound(false);
            let pElem = document.getElementById(`player${playerNum}`);
            if (pElem) pElem.classList.add('reversed');
            if (playerNum === 1) {
                p1Reversed = true;
                if (p1Timers.rev) clearTimeout(p1Timers.rev);
                p1Timers.rev = setTimeout(() => { p1Reversed = false; if (pElem) pElem.classList.remove('reversed'); }, 3000);
            } else {
                p2Reversed = true;
                if (p2Timers.rev) clearTimeout(p2Timers.rev);
                p2Timers.rev = setTimeout(() => { p2Reversed = false; if (pElem) pElem.classList.remove('reversed'); }, 3000);
            }
        } else if (type === 'ice') {
            playItemSound(false);
            let pElem = document.getElementById(`player${playerNum}`);
            if (pElem) pElem.classList.add('slowed');
            if (playerNum === 1) {
                p1Slowed = true;
                if (p1Timers.slow) clearTimeout(p1Timers.slow);
                p1Timers.slow = setTimeout(() => { p1Slowed = false; if (pElem) pElem.classList.remove('slowed'); }, 3000);
            } else {
                p2Slowed = true;
                if (p2Timers.slow) clearTimeout(p2Timers.slow);
                p2Timers.slow = setTimeout(() => { p2Slowed = false; if (pElem) pElem.classList.remove('slowed'); }, 3000);
            }
        } else {
            playHitSound();
            if (gameMode === 3) {
                sharedLives--;
            } else {
                if (playerNum === 1) p1Lives--;
                else p2Lives--;
            }
        }

        updateHUD();
    }

    function updateHUD() {
        let statusMsg = isMushroomActive ? " (🍄 버프중!)" : "";
        let waveText = isBossActive ? '보스전' : (currentWave > 16 ? `무한(W${currentWave})` : `웨이브 ${currentWave}`);
        
        let livesText = '';
        if (gameMode === 2) {
            livesText = `1P: ${'❤️'.repeat(Math.max(0, p1Lives))} | 2P: ${'💙'.repeat(Math.max(0, p2Lives))}`;
        } else if (gameMode === 3) {
            livesText = `합동 목숨(${currentDifficultyKey}): ${'💖'.repeat(Math.max(0, sharedLives))}`;
        } else {
            livesText = '❤️'.repeat(Math.max(0, p1Lives));
        }

        document.getElementById('ui-lives').innerText = livesText;
        document.getElementById('ui-wave').innerText = waveText;
        
        if (gameMode === 2) {
            document.getElementById('ui-score').innerText = `대전 생존전`;
        } else {
            document.getElementById('ui-score').innerText = `점수: ${score}${statusMsg}`;
        }
    }

    window.addEventListener('keydown', (e) => {
        if (!document.getElementById('screen-game').classList.contains('active')) return;
        
        if (e.key === 'a' || e.key === 'A') { p1Left = true; e.preventDefault(); }
        if (e.key === 'd' || e.key === 'D') { p1Right = true; e.preventDefault(); }

        if (gameMode === 2 || gameMode === 3) {
            if (e.key === 'ArrowLeft') { p2Left = true; e.preventDefault(); }
            if (e.key === 'ArrowRight') { p2Right = true; e.preventDefault(); }
        } else {
            if (e.key === 'ArrowLeft') { p1Left = true; e.preventDefault(); }
            if (e.key === 'ArrowRight') { p1Right = true; e.preventDefault(); }
        }
    });

    window.addEventListener('keyup', (e) => {
        if (e.key === 'a' || e.key === 'A') p1Left = false;
        if (e.key === 'd' || e.key === 'D') p1Right = false;
        if (e.key === 'ArrowLeft') p2Left = false;
        if (e.key === 'ArrowRight') p2Right = false;
        if (gameMode === 1) {
            if (e.key === 'ArrowLeft') p1Left = false;
            if (e.key === 'ArrowRight') p1Right = false;
        }
    });

    function quitGame() {
        stopBGM();
        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        clearAllTimers();
        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];
        if (bossObj && bossObj.element) { bossObj.element.remove(); bossObj = null; }
        document.getElementById('boss-hp-container').style.display = 'none';
        document.getElementById('blackout-overlay').style.display = 'none';
        
        document.getElementById('input-modal').style.display = 'none';
        document.getElementById('screen-game').classList.remove('active');
        document.getElementById('screen-menu').classList.add('active');
    }

    function returnToMenu() {
        stopBGM();
        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        clearAllTimers();
        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];
        if (bossObj && bossObj.element) { bossObj.element.remove(); bossObj = null; }
        document.getElementById('boss-hp-container').style.display = 'none';
        document.getElementById('blackout-overlay').style.display = 'none';

        document.getElementById('input-modal').style.display = 'none';
        document.getElementById('screen-gameover').classList.remove('active');
        document.getElementById('screen-dict').classList.remove('active');
        document.getElementById('screen-menu-1p').classList.remove('active');
        document.getElementById('screen-menu-2p').classList.remove('active');
        document.getElementById('screen-menu-coop-diff').classList.remove('active');
        document.getElementById('screen-menu').classList.add('active');
        displayRankings();
    }

    function clearAllTimers() {
        if (p1Timers.rev) clearTimeout(p1Timers.rev);
        if (p1Timers.slow) clearTimeout(p1Timers.slow);
        if (p1Timers.slip) clearTimeout(p1Timers.slip);
        if (p2Timers.rev) clearTimeout(p2Timers.rev);
        if (p2Timers.slow) clearTimeout(p2Timers.slow);
        if (p2Timers.slip) clearTimeout(p2Timers.slip);
        if (mushroomTimer) clearTimeout(mushroomTimer);
        if (blackoutTimer) clearTimeout(blackoutTimer);
        if (maskTimer) clearTimeout(maskTimer);
    }

    function endGame(resultMsg) {
        stopBGM();
        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        clearAllTimers();
        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];
        if (bossObj && bossObj.element) { bossObj.element.remove(); bossObj = null; }
        document.getElementById('boss-hp-container').style.display = 'none';
        document.getElementById('blackout-overlay').style.display = 'none';
        
        document.getElementById('screen-game').classList.remove('active');
        document.getElementById('gameover-title').innerText = `💩 ${resultMsg}`;
        document.getElementById('killer-info').innerHTML = `최후의 원흉: <b>${lastKiller.icon} ${lastKiller.name}</b>`;
        
        if (gameMode === 2) {
            document.getElementById('final-score').innerText = `승패 결정 완료!`;
            document.getElementById('cheer-msg').innerText = `치열한 대결이었다!`;
        } else {
            document.getElementById('final-score').innerText = `최종 점수: ${score}점`;
            document.getElementById('cheer-msg').innerText = `"${gameMode === 3 ? '환상적인 광속 호흡이었다!' : '빛의 속도의 생존이었다!'}"`;
        }
        
        document.getElementById('input-modal').style.display = 'none';
        document.getElementById('screen-gameover').classList.add('active');
        
        if (gameMode !== 2 && score > 0 && (gameMode === 1 || gameMode === 3)) {
            document.getElementById('modal-score-text').innerText = `달성 점수: ${score}점`;
            setTimeout(() => {
                document.getElementById('input-modal').style.display = 'flex';
            }, 100);
        }
    }

    function saveRanking() {
        let nameInput = document.getElementById('player-name').value.trim();
        if (!nameInput) { alert('이름을 입력해주세요!'); return; }

        let storageKey = (gameMode === 3) ? 'poop_rankings_합동_v31' : `poop_rankings_${currentDifficultyKey}_v31`;
        let rankings = getRankings(storageKey);
        
        rankings.push({ name: nameInput, score: score });
        rankings.sort((a, b) => b.score - a.score);
        rankings = rankings.slice(0, 3);

        localStorage.setItem(storageKey, JSON.stringify(rankings));
        document.getElementById('input-modal').style.display = 'none';
        document.getElementById('player-name').value = '';
        displayRankings();
    }

    function skipRanking() {
        document.getElementById('input-modal').style.display = 'none';
        document.getElementById('player-name').value = '';
    }
</script>

</body>
</html>
