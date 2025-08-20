---
title: AI 写的塔防游戏
date: 2025/8/20 09:13:22
tags: 
  - html
  - AI
  - 游戏
---
## AI 写的塔防游戏
```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>高级回合制塔防游戏</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            color: white;
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        
        header {
            text-align: center;
            margin-bottom: 20px;
            padding: 20px;
            background: rgba(0, 0, 0, 0.7);
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
        }
        
        h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }
        
        .game-stats {
            display: flex;
            justify-content: center;
            gap: 30px;
            margin-top: 10px;
        }
        
        .stat {
            background: rgba(255, 255, 255, 0.1);
            padding: 10px 20px;
            border-radius: 10px;
            border: 2px solid rgba(255, 255, 255, 0.2);
        }
        
        .game-area {
            display: grid;
            grid-template-columns: 800px 350px;
            gap: 20px;
            margin-bottom: 20px;
        }
        
        .game-board {
            display: grid;
            grid-template-columns: repeat(12, 60px);
            grid-template-rows: repeat(12, 60px);
            gap: 2px;
            background: rgba(0, 0, 0, 0.8);
            padding: 10px;
            border-radius: 15px;
            border: 3px solid #ffd700;
            box-shadow: 0 0 20px rgba(255, 215, 0, 0.3);
        }
        
        .cell {
            width: 60px;
            height: 60px;
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            position: relative;
            transition: all 0.2s ease;
            border-radius: 5px;
        }
        
        .cell:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: scale(1.05);
        }
        
        .cell.path {
            background: linear-gradient(45deg, #4a90e2, #7b68ee);
        }
        
        .cell.tower-cell {
            background: rgba(139, 0, 0, 0.3);
        }
        
        .cell.start {
            background: linear-gradient(45deg, #00b09b, #96c93d);
        }
        
        .cell.end {
            background: linear-gradient(45deg, #ff416c, #ff4b2b);
        }
        
        .cell.obstacle {
            background: linear-gradient(45deg, #654321, #8b4513);
        }
        
        .unit {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            position: absolute;
            z-index: 10;
            box-shadow: 0 0 10px currentColor;
            animation: pulse 1.5s infinite;
        }
        
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }
        
        .player-unit {
            background: linear-gradient(45deg, #00f260, #0575e6);
            color: #00f260;
        }
        
        .ai-unit {
            background: linear-gradient(45deg, #ff416c, #ff4b2b);
            color: #ff416c;
        }
        
        .tower {
            width: 45px;
            height: 45px;
            border-radius: 8px;
            position: absolute;
            z-index: 5;
            border: 3px solid;
            display: flex;
            justify-content: center;
            align-items: center;
            font-weight: bold;
            font-size: 18px;
        }
        
        .player-tower {
            background: linear-gradient(45deg, #2196f3, #1976d2);
            border-color: #0d47a1;
            color: white;
        }
        
        .ai-tower {
            background: linear-gradient(45deg, #9c27b0, #7b1fa2);
            border-color: #4a148c;
            color: white;
        }
        
        .tower-range {
            position: absolute;
            border: 2px dashed rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            z-index: 2;
            pointer-events: none;
        }
        
        .control-panel {
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border-radius: 15px;
            border: 3px solid #00bcd4;
            box-shadow: 0 0 20px rgba(0, 188, 212, 0.3);
        }
        
        .resource-display {
            background: rgba(255, 215, 0, 0.2);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            border: 2px solid #ffd700;
        }
        
        .resource-bar {
            height: 20px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 10px;
            margin: 10px 0;
            overflow: hidden;
        }
        
        .resource-fill {
            height: 100%;
            border-radius: 10px;
            transition: width 0.5s ease;
        }
        
        .player-resource { background: linear-gradient(90deg, #00f260, #0575e6); }
        .ai-resource { background: linear-gradient(90deg, #ff416c, #ff4b2b); }
        
        .actions {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .btn {
            padding: 12px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s ease;
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            border: 2px solid rgba(255, 255, 255, 0.3);
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }
        
        .btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none;
        }
        
        .btn-primary {
            background: linear-gradient(45deg, #00b09b, #96c93d);
        }
        
        .btn-danger {
            background: linear-gradient(45deg, #ff416c, #ff4b2b);
        }
        
        .tower-info, .unit-info {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
            margin: 10px 0;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .upgrades {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-top: 15px;
        }
        
        .upgrade-btn {
            padding: 8px;
            font-size: 12px;
            background: rgba(255, 215, 0, 0.3);
            border: 1px solid #ffd700;
        }
        
        .log-container {
            background: rgba(0, 0, 0, 0.7);
            padding: 15px;
            border-radius: 10px;
            border: 2px solid #7b68ee;
            max-height: 300px;
            overflow-y: auto;
        }
        
        .log-entry {
            padding: 8px;
            margin: 5px 0;
            border-radius: 5px;
            font-size: 14px;
            border-left: 4px solid;
        }
        
        .player-log { border-color: #00f260; background: rgba(0, 242, 96, 0.1); }
        .ai-log { border-color: #ff416c; background: rgba(255, 65, 108, 0.1); }
        .system-log { border-color: #2196f3; background: rgba(33, 150, 243, 0.1); }
        .combat-log { border-color: #ffd700; background: rgba(255, 215, 0, 0.1); }
        
        .tech-tree {
            background: rgba(0, 0, 0, 0.7);
            padding: 20px;
            border-radius: 15px;
            border: 3px solid #9c27b0;
            margin-top: 20px;
        }
        
        .tech-category {
            margin-bottom: 15px;
        }
        
        .tech-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 8px;
            margin: 5px 0;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.2s ease;
        }
        
        .tech-item:hover {
            background: rgba(255, 255, 255, 0.2);
        }
        
        .tech-item.unavailable {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }
        
        .modal-content {
            background: rgba(30, 30, 50, 0.95);
            padding: 30px;
            border-radius: 20px;
            border: 3px solid #00bcd4;
            max-width: 500px;
            text-align: center;
        }
        
        .modal h2 {
            margin-bottom: 20px;
            color: #00bcd4;
        }
        
        .modal-btn {
            padding: 12px 30px;
            margin: 15px 10px;
            font-size: 18px;
            background: linear-gradient(45deg, #00b09b, #96c93d);
            border: none;
            border-radius: 8px;
            cursor: pointer;
            color: white;
        }
        
        .health-bar {
            position: absolute;
            bottom: 5px;
            left: 5px;
            right: 5px;
            height: 4px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 2px;
            overflow: hidden;
        }
        
        .health-fill {
            height: 100%;
            background: linear-gradient(90deg, #ff416c, #00f260);
            transition: width 0.3s ease;
        }
        
        .effect {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 20;
            animation: effect 0.5s ease-out;
        }
        
        @keyframes effect {
            0% { opacity: 1; transform: scale(0.5); }
            100% { opacity: 0; transform: scale(1.5); }
        }
        
        .damage-text {
            position: absolute;
            color: #ff416c;
            font-weight: bold;
            font-size: 14px;
            pointer-events: none;
            z-index: 25;
            animation: floatUp 1s ease-out;
        }
        
        @keyframes floatUp {
            0% { transform: translateY(0); opacity: 1; }
            100% { transform: translateY(-30px); opacity: 0; }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>⚔️ 高级回合制塔防游戏 🛡️</h1>
            <div class="game-stats">
                <div class="stat">回合: <span id="currentTurn">1</span></div>
                <div class="stat">玩家分数: <span id="playerScore">0</span></div>
                <div class="stat">AI分数: <span id="aiScore">0</span></div>
                <div class="stat">游戏状态: <span id="gameStatus">进行中</span></div>
            </div>
        </header>
        
        <div class="game-area">
            <div class="game-board" id="gameBoard"></div>
            
            <div class="control-panel">
                <div class="resource-display">
                    <h3>💰 资源</h3>
                    <div>玩家: <span id="playerResources">150</span> (+<span id="playerIncome">25</span>/回合)</div>
                    <div class="resource-bar">
                        <div class="resource-fill player-resource" style="width: 100%"></div>
                    </div>
                    <div>AI: <span id="aiResources">150</span> (+<span id="aiIncome">25</span>/回合)</div>
                    <div class="resource-bar">
                        <div class="resource-fill ai-resource" style="width: 100%"></div>
                    </div>
                </div>
                
                <div class="actions">
                    <button class="btn btn-primary" id="buildBasicTowerBtn">建造基础塔 (80💰)</button>
                    <button class="btn btn-primary" id="buildAdvancedTowerBtn">建造高级塔 (150💰)</button>
                    <button class="btn btn-primary" id="sendBasicUnitBtn">派遣基础单位 (40💰)</button>
                    <button class="btn btn-primary" id="sendAdvancedUnitBtn">派遣高级单位 (80💰)</button>
                    <button class="btn" id="upgradeEconomyBtn">升级经济 (100💰)</button>
                    <button class="btn" id="researchTechBtn">研究科技 (120💰)</button>
                    <button class="btn btn-danger" id="specialAbilityBtn">特殊能力 (200💰)</button>
                    <button class="btn" id="endTurnBtn">结束回合</button>
                </div>
                
                <div class="tower-info">
                    <h4>🏰 塔信息</h4>
                    <div>基础塔: 伤害 15 | 范围 3 | 造价 80</div>
                    <div>高级塔: 伤害 25 | 范围 4 | 造价 150</div>
                </div>
                
                <div class="unit-info">
                    <h4>⚔️ 单位信息</h4>
                    <div>基础单位: 生命 120 | 速度 1 | 造价 40</div>
                    <div>高级单位: 生命 200 | 速度 2 | 造价 80</div>
                </div>
                
                <div class="log-container" id="gameLog"></div>
            </div>
        </div>
        
        <div class="tech-tree">
            <h3>🔬 科技树</h3>
            <div class="tech-category">
                <h4>军事科技</h4>
                <div class="tech-item" data-tech="range">射程提升 (+1范围) - 80💰</div>
                <div class="tech-item unavailable" data-tech="damage">伤害提升 (+20%) - 120💰</div>
                <div class="tech-item unavailable" data-tech="speed">攻击速度提升 - 150💰</div>
            </div>
            <div class="tech-category">
                <h4>经济科技</h4>
                <div class="tech-item" data-tech="income">收入提升 (+10/回合) - 100💰</div>
                <div class="tech-item unavailable" data-tech="discount">建筑折扣 (-20%) - 200💰</div>
            </div>
        </div>
    </div>
    
    <div class="modal" id="gameOverModal">
        <div class="modal-content">
            <h2 id="gameOverTitle">游戏结束</h2>
            <p id="gameOverMessage"></p>
            <button class="modal-btn" onclick="resetGame()">再来一局</button>
        </div>
    </div>

    <script>
        // 游戏状态和配置
        const gameConfig = {
            boardSize: 12,
            startingResources: 150,
            baseIncome: 25,
            maxTurns: 50
        };
        
        const gameState = {
            currentPlayer: 'player',
            playerResources: gameConfig.startingResources,
            aiResources: gameConfig.startingResources,
            playerIncome: gameConfig.baseIncome,
            aiIncome: gameConfig.baseIncome,
            turn: 1,
            board: [],
            playerUnits: [],
            aiUnits: [],
            playerTowers: [],
            aiTowers: [],
            playerTech: new Set(),
            aiTech: new Set(),
            path: [],
            obstacles: [],
            playerScore: 0,
            aiScore: 0,
            gameOver: false,
            selectedCell: null
        };
        
        // 塔类型配置
        const towerTypes = {
            basic: { damage: 15, range: 3, cost: 80, symbol: 'T1' },
            advanced: { damage: 25, range: 4, cost: 150, symbol: 'T2' }
        };
        
        // 单位类型配置
        const unitTypes = {
            basic: { health: 120, speed: 1, cost: 40, symbol: 'U1' },
            advanced: { health: 200, speed: 2, cost: 80, symbol: 'U2' }
        };
        
        // 科技效果配置
        const techEffects = {
            range: { cost: 80, effect: 'towerRange+1' },
            damage: { cost: 120, effect: 'towerDamage+20%' },
            speed: { cost: 150, effect: 'attackSpeed+1' },
            income: { cost: 100, effect: 'income+10' },
            discount: { cost: 200, effect: 'buildCost-20%' }
        };
        
        // 初始化游戏板
        function initializeBoard() {
            const gameBoard = document.getElementById('gameBoard');
            gameBoard.innerHTML = '';
            gameState.board = [];
            
            // 创建游戏板
            for (let y = 0; y < gameConfig.boardSize; y++) {
                gameState.board[y] = [];
                for (let x = 0; x < gameConfig.boardSize; x++) {
                    gameState.board[y][x] = { 
                        type: 'empty', 
                        unit: null, 
                        tower: null,
                        obstacle: false
                    };
                    
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.dataset.x = x;
                    cell.dataset.y = y;
                    cell.addEventListener('click', () => handleCellClick(x, y));
                    gameBoard.appendChild(cell);
                }
            }
            
            // 生成复杂路径
            generateComplexPath();
            
            // 生成障碍物
            generateObstacles();
            
            updateBoardDisplay();
        }
        
        // 生成复杂路径
        function generateComplexPath() {
            gameState.path = [];
            const directions = [
                {dx: 1, dy: 0}, {dx: 0, dy: 1}, 
                {dx: -1, dy: 0}, {dx: 0, dy: -1}
            ];
            
            let x = 0, y = Math.floor(Math.random() * 4) + 2;
            gameState.path.push({x, y});
            gameState.board[y][x].type = 'start';
            
            while (x < gameConfig.boardSize - 1) {
                // 随机选择方向（优先向右）
                const dir = Math.random() < 0.6 ? directions[0] : 
                           directions[Math.floor(Math.random() * directions.length)];
                
                const newX = x + dir.dx;
                const newY = y + dir.dy;
                
                if (newX >= 0 && newX < gameConfig.boardSize && 
                    newY >= 0 && newY < gameConfig.boardSize &&
                    !gameState.path.some(p => p.x === newX && p.y === newY)) {
                    
                    x = newX;
                    y = newY;
                    gameState.path.push({x, y});
                    gameState.board[y][x].type = 'path';
                }
            }
            
            gameState.board[y][x].type = 'end';
        }
        
        // 生成障碍物
        function generateObstacles() {
            gameState.obstacles = [];
            for (let i = 0; i < 15; i++) {
                const x = Math.floor(Math.random() * gameConfig.boardSize);
                const y = Math.floor(Math.random() * gameConfig.boardSize);
                
                if (gameState.board[y][x].type === 'empty') {
                    gameState.board[y][x].type = 'obstacle';
                    gameState.board[y][x].obstacle = true;
                    gameState.obstacles.push({x, y});
                }
            }
        }
        
        // 处理单元格点击
        function handleCellClick(x, y) {
            if (gameState.currentPlayer !== 'player' || gameState.gameOver) return;
            
            const cell = gameState.board[y][x];
            gameState.selectedCell = {x, y};
            
            if (cell.type === 'empty' && !cell.tower && !cell.obstacle) {
                showBuildOptions(x, y);
            } else if (cell.tower && cell.tower.owner === 'player') {
                showTowerOptions(x, y);
            }
        }
        
        // 显示建造选项
        function showBuildOptions(x, y) {
            const basicCost = towerTypes.basic.cost;
            const advancedCost = towerTypes.advanced.cost;
            
            if (confirm(`在位置 (${x}, ${y}) 建造:\n\n基础塔 - ${basicCost}💰\n高级塔 - ${advancedCost}💰`)) {
                const choice = prompt('选择要建造的塔类型 (输入 1 或 2):\n1. 基础塔\n2. 高级塔');
                
                if (choice === '1') {
                    buildTower(x, y, 'player', 'basic');
                } else if (choice === '2') {
                    buildTower(x, y, 'player', 'advanced');
                }
            }
        }
        
        // 建造塔
        function buildTower(x, y, owner, type) {
            const towerConfig = towerTypes[type];
            const cost = towerConfig.cost;
            
            if (owner === 'player' && gameState.playerResources < cost) {
                addLog('资源不足，无法建造防御塔', 'system');
                return false;
            }
            
            if (owner === 'ai' && gameState.aiResources < cost) {
                return false;
            }
            
            gameState.board[y][x].tower = { 
                owner, 
                type,
                damage: towerConfig.damage,
                range: towerConfig.range,
                attacks: 0
            };
            
            if (owner === 'player') {
                gameState.playerResources -= cost;
                gameState.playerTowers.push({ x, y, type });
                addLog(`玩家建造了${type === 'basic' ? '基础' : '高级'}防御塔 (${x}, ${y})`, 'player');
            } else {
                gameState.aiResources -= cost;
                gameState.aiTowers.push({ x, y, type });
                addLog(`AI建造了${type === 'basic' ? '基础' : '高级'}防御塔 (${x}, ${y})`, 'ai');
            }
            
            updateBoardDisplay();
            return true;
        }
        
        // 派遣单位
        function sendUnit(owner, type) {
            const unitConfig = unitTypes[type];
            const cost = unitConfig.cost;
            
            if (owner === 'player' && gameState.playerResources < cost) {
                addLog('资源不足，无法派遣单位', 'system');
                return false;
            }
            
            if (owner === 'ai' && gameState.aiResources < cost) {
                return false;
            }
            
            const start = gameState.path[0];
            const unit = { 
                owner, 
                type,
                health: unitConfig.health,
                maxHealth: unitConfig.health,
                position: 0,
                x: start.x, 
                y: start.y,
                speed: unitConfig.speed,
                damageTaken: 0
            };
            
            gameState.board[start.y][start.x].unit = unit;
            
            if (owner === 'player') {
                gameState.playerResources -= cost;
                gameState.playerUnits.push(unit);
                addLog(`玩家派遣了${type === 'basic' ? '基础' : '高级'}单位`, 'player');
            } else {
                gameState.aiResources -= cost;
                gameState.aiUnits.push(unit);
                addLog(`AI派遣了${type === 'basic' ? '基础' : '高级'}单位`, 'ai');
            }
            
            updateBoardDisplay();
            return true;
        }
        
        // AI智能决策
        function aiDecision() {
            const strategy = Math.random();
            let actionTaken = false;
            
            // 40%几率建造塔
            if (strategy < 0.4 && gameState.aiResources >= towerTypes.basic.cost) {
                const bestSpot = findBestTowerSpot();
                if (bestSpot) {
                    const towerType = gameState.aiResources >= towerTypes.advanced.cost && Math.random() < 0.3 ? 'advanced' : 'basic';
                    actionTaken = buildTower(bestSpot.x, bestSpot.y, 'ai', towerType);
                }
            }
            // 30%几率派遣单位
            else if (strategy < 0.7 && gameState.aiResources >= unitTypes.basic.cost) {
                const unitType = gameState.aiResources >= unitTypes.advanced.cost && Math.random() < 0.4 ? 'advanced' : 'basic';
                actionTaken = sendUnit('ai', unitType);
            }
            // 20%几率研究科技
            else if (strategy < 0.9 && gameState.aiResources >= 80) {
                actionTaken = researchTech('ai');
            }
            // 10%几率升级经济
            else if (gameState.aiResources >= 100) {
                actionTaken = upgradeEconomy('ai');
            }
            
            // 如果没采取行动，结束回合
            setTimeout(() => {
                if (!actionTaken) addLog('AI选择保存资源', 'ai');
                endTurn();
            }, 1000);
        }
        
        // 寻找最佳塔位置
        function findBestTowerSpot() {
            let bestSpot = null;
            let maxCoverage = 0;
            
            for (let y = 0; y < gameConfig.boardSize; y++) {
                for (let x = 0; x < gameConfig.boardSize; x++) {
                    if (gameState.board[y][x].type === 'empty' && 
                        !gameState.board[y][x].tower && 
                        !gameState.board[y][x].obstacle) {
                        
                        let coverage = 0;
                        for (const pathPos of gameState.path) {
                            const distance = Math.max(Math.abs(pathPos.x - x), Math.abs(pathPos.y - y));
                            if (distance <= 3) coverage++;
                        }
                        
                        if (coverage > maxCoverage) {
                            maxCoverage = coverage;
                            bestSpot = { x, y };
                        }
                    }
                }
            }
            
            return bestSpot;
        }
        
        // 研究科技
        function researchTech(owner) {
            const availableTechs = Array.from(Object.entries(techEffects))
                .filter(([tech]) => !gameState[owner === 'player' ? 'playerTech' : 'aiTech'].has(tech))
                .filter(([_, config]) => (owner === 'player' ? gameState.playerResources : gameState.aiResources) >= config.cost);
            
            if (availableTechs.length === 0) return false;
            
            const [tech] = availableTechs[Math.floor(Math.random() * availableTechs.length)];
            const cost = techEffects[tech].cost;
            
            if (owner === 'player') {
                gameState.playerResources -= cost;
                gameState.playerTech.add(tech);
                addLog(`玩家研究了科技: ${tech}`, 'player');
            } else {
                gameState.aiResources -= cost;
                gameState.aiTech.add(tech);
                addLog(`AI研究了科技: ${tech}`, 'ai');
            }
            
            applyTechEffect(owner, tech);
            return true;
        }
        
        // 应用科技效果
        function applyTechEffect(owner, tech) {
            switch (tech) {
                case 'range':
                    // 增加所有塔的范围
                    const towers = owner === 'player' ? gameState.playerTowers : gameState.aiTowers;
                    towers.forEach(tower => {
                        gameState.board[tower.y][tower.x].tower.range += 1;
                    });
                    break;
                case 'income':
                    if (owner === 'player') {
                        gameState.playerIncome += 10;
                    } else {
                        gameState.aiIncome += 10;
                    }
                    break;
            }
        }
        
        // 升级经济
        function upgradeEconomy(owner) {
            const cost = 100;
            
            if (owner === 'player' && gameState.playerResources < cost) {
                return false;
            }
            
            if (owner === 'ai' && gameState.aiResources < cost) {
                return false;
            }
            
            if (owner === 'player') {
                gameState.playerResources -= cost;
                gameState.playerIncome += 5;
                addLog('玩家升级了经济 (+5收入/回合)', 'player');
            } else {
                gameState.aiResources -= cost;
                gameState.aiIncome += 5;
                addLog('AI升级了经济 (+5收入/回合)', 'ai');
            }
            
            return true;
        }
        
        // 移动单位
        function moveUnits() {
            // 移动所有单位
            [...gameState.playerUnits, ...gameState.aiUnits].forEach(unit => {
                if (unit.health > 0) {
                    moveUnit(unit);
                }
            });
        }
        
        // 移动单个单位
        function moveUnit(unit) {
            gameState.board[unit.y][unit.x].unit = null;
            
            unit.position += unit.speed;
            
            if (unit.position >= gameState.path.length) {
                // 单位到达终点
                unitReachedEnd(unit);
                return;
            }
            
            const newPos = gameState.path[unit.position];
            unit.x = newPos.x;
            unit.y = newPos.y;
            gameState.board[unit.y][unit.x].unit = unit;
        }
        
        // 单位到达终点
        function unitReachedEnd(unit) {
            const index = unit.owner === 'player' 
                ? gameState.playerUnits.indexOf(unit) 
                : gameState.aiUnits.indexOf(unit);
            
            if (index > -1) {
                if (unit.owner === 'player') {
                    gameState.playerUnits.splice(index, 1);
                    gameState.playerScore += unit.type === 'advanced' ? 3 : 1;
                    addLog(`玩家单位到达终点! +${unit.type === 'advanced' ? 3 : 1}分`, 'system');
                } else {
                    gameState.aiUnits.splice(index, 1);
                    gameState.aiScore += unit.type === 'advanced' ? 3 : 1;
                    addLog(`AI单位到达终点! +${unit.type === 'advanced' ? 3 : 1}分`, 'system');
                }
                checkGameOver();
            }
        }
        
        // 塔攻击
        function towersAttack() {
            const currentPlayer = gameState.currentPlayer;
            const attacker = currentPlayer === 'player' ? 'player' : 'ai';
            const defender = currentPlayer === 'player' ? 'ai' : 'player';
            
            const towers = attacker === 'player' ? gameState.playerTowers : gameState.aiTowers;
            
            towers.forEach(tower => {
                const towerData = gameState.board[tower.y][tower.x].tower;
                const targets = defender === 'player' ? gameState.playerUnits : gameState.aiUnits;
                
                const inRange = targets.filter(unit => {
                    const distance = Math.max(Math.abs(unit.x - tower.x), Math.abs(unit.y - tower.y));
                    return distance <= towerData.range && unit.health > 0;
                });
                
                if (inRange.length > 0) {
                    // 选择最近的目标
                    inRange.sort((a, b) => a.position - b.position);
                    const target = inRange[0];
                    
                    // 造成伤害
                    target.health -= towerData.damage;
                    target.damageTaken += towerData.damage;
                    
                    // 显示伤害效果
                    showDamageEffect(target.x, target.y, towerData.damage);
                    
                    addLog(`${attacker === 'player' ? '玩家' : 'AI'}塔攻击${defender === 'player' ? '玩家' : 'AI'}单位, 造成 ${towerData.damage} 伤害`, 'combat');
                    
                    // 检查是否消灭目标
                    if (target.health <= 0) {
                        gameState.board[target.y][target.x].unit = null;
                        const targetIndex = targets.indexOf(target);
                        if (targetIndex > -1) targets.splice(targetIndex, 1);
                        
                        if (attacker === 'player') {
                            gameState.playerScore += target.type === 'advanced' ? 2 : 1;
                        } else {
                            gameState.aiScore += target.type === 'advanced' ? 2 : 1;
                        }
                        
                        addLog(`${defender === 'player' ? '玩家' : 'AI'}单位被消灭!`, 'combat');
                    }
                }
            });
        }
        
        // 显示伤害效果
        function showDamageEffect(x, y, damage) {
            const cell = document.querySelector(`.cell[data-x="${x}"][data-y="${y}"]`);
            if (!cell) return;
            
            const damageText = document.createElement('div');
            damageText.className = 'damage-text';
            damageText.textContent = `-${damage}`;
            damageText.style.left = '50%';
            damageText.style.top = '50%';
            cell.appendChild(damageText);
            
            setTimeout(() => damageText.remove(), 1000);
        }
        
        // 检查游戏结束
        function checkGameOver() {
            if (gameState.turn >= gameConfig.maxTurns) {
                endGame(gameState.playerScore > gameState.aiScore ? 'player' : 
                       gameState.aiScore > gameState.playerScore ? 'ai' : 'draw');
                return;
            }
            
            if (gameState.playerScore >= 20) {
                endGame('player');
            } else if (gameState.aiScore >= 20) {
                endGame('ai');
            }
        }
        
        // 结束游戏
        function endGame(winner) {
            gameState.gameOver = true;
            const modal = document.getElementById('gameOverModal');
            const title = document.getElementById('gameOverTitle');
            const message = document.getElementById('gameOverMessage');
            
            if (winner === 'player') {
                title.textContent = '🎉 玩家胜利! 🎉';
                message.textContent = `恭喜! 你以 ${gameState.playerScore} : ${gameState.aiScore} 的比分获胜!`;
            } else if (winner === 'ai') {
                title.textContent = '😵 AI胜利! 😵';
                message.textContent = `AI以 ${gameState.aiScore} : ${gameState.playerScore} 的比分获胜!`;
            } else {
                title.textContent = '🤝 平局! 🤝';
                message.textContent = `比赛以 ${gameState.playerScore} : ${gameState.aiScore} 的比分结束!`;
            }
            
            modal.style.display = 'flex';
        }
        
        // 重置游戏
        function resetGame() {
            document.getElementById('gameOverModal').style.display = 'none';
            Object.assign(gameState, {
                currentPlayer: 'player',
                playerResources: gameConfig.startingResources,
                aiResources: gameConfig.startingResources,
                playerIncome: gameConfig.baseIncome,
                aiIncome: gameConfig.baseIncome,
                turn: 1,
                playerUnits: [],
                aiUnits: [],
                playerTowers: [],
                aiTowers: [],
                playerTech: new Set(),
                aiTech: new Set(),
                playerScore: 0,
                aiScore: 0,
                gameOver: false
            });
            
            initializeBoard();
            updateUI();
            addLog('游戏开始! 玩家先行动', 'system');
        }
        
        // 更新UI
        function updateUI() {
            document.getElementById('currentTurn').textContent = gameState.turn;
            document.getElementById('playerScore').textContent = gameState.playerScore;
            document.getElementById('aiScore').textContent = gameState.aiScore;
            document.getElementById('playerResources').textContent = gameState.playerResources;
            document.getElementById('aiResources').textContent = gameState.aiResources;
            document.getElementById('playerIncome').textContent = gameState.playerIncome;
            document.getElementById('aiIncome').textContent = gameState.aiIncome;
            
            // 更新资源条
            document.querySelector('.player-resource').style.width = 
                `${Math.min(100, (gameState.playerResources / 500) * 100)}%`;
            document.querySelector('.ai-resource').style.width = 
                `${Math.min(100, (gameState.aiResources / 500) * 100)}%`;
        }
        
        // 添加日志
        function addLog(message, type) {
            const log = document.getElementById('gameLog');
            const entry = document.createElement('div');
            entry.className = `log-entry ${type}-log`;
            entry.textContent = `[${new Date().toLocaleTimeString()}] ${message}`;
            log.appendChild(entry);
            log.scrollTop = log.scrollHeight;
        }
        
        // 更新游戏板显示
        function updateBoardDisplay() {
            const cells = document.querySelectorAll('.cell');
            
            for (let y = 0; y < gameConfig.boardSize; y++) {
                for (let x = 0; x < gameConfig.boardSize; x++) {
                    const index = y * gameConfig.boardSize + x;
                    const cell = cells[index];
                    const boardCell = gameState.board[y][x];
                    
                    cell.className = 'cell';
                    cell.innerHTML = '';
                    
                    // 设置单元格类型
                    if (boardCell.type === 'path') cell.classList.add('path');
                    if (boardCell.type === 'start') cell.classList.add('start');
                    if (boardCell.type === 'end') cell.classList.add('end');
                    if (boardCell.type === 'obstacle') cell.classList.add('obstacle');
                    
                    // 添加塔
                    if (boardCell.tower) {
                        const tower = document.createElement('div');
                        tower.className = `tower ${boardCell.tower.owner}-tower`;
                        tower.textContent = boardCell.tower.type === 'basic' ? 'T1' : 'T2';
                        cell.appendChild(tower);
                        cell.classList.add('tower-cell');
                        
                        // 显示塔范围
                        const rangeIndicator = document.createElement('div');
                        rangeIndicator.className = 'tower-range';
                        rangeIndicator.style.width = `${boardCell.tower.range * 120 + 60}px`;
                        rangeIndicator.style.height = `${boardCell.tower.range * 120 + 60}px`;
                        rangeIndicator.style.left = `${-boardCell.tower.range * 60}px`;
                        rangeIndicator.style.top = `${-boardCell.tower.range * 60}px`;
                        cell.appendChild(rangeIndicator);
                    }
                    
                    // 添加单位
                    if (boardCell.unit) {
                        const unit = document.createElement('div');
                        unit.className = `unit ${boardCell.unit.owner}-unit`;
                        cell.appendChild(unit);
                        
                        // 添加血条
                        const healthBar = document.createElement('div');
                        healthBar.className = 'health-bar';
                        const healthFill = document.createElement('div');
                        healthFill.className = 'health-fill';
                        healthFill.style.width = `${(boardCell.unit.health / boardCell.unit.maxHealth) * 100}%`;
                        healthBar.appendChild(healthFill);
                        cell.appendChild(healthBar);
                    }
                }
            }
            
            updateUI();
        }
        
        // 结束回合
        function endTurn() {
            // 移动单位
            moveUnits();
            
            // 塔攻击
            towersAttack();
            
            // 检查游戏结束
            checkGameOver();
            if (gameState.gameOver) return;
            
            // 切换玩家并增加资源
            gameState.currentPlayer = gameState.currentPlayer === 'player' ? 'ai' : 'player';
            gameState.turn++;
            
            if (gameState.currentPlayer === 'player') {
                gameState.playerResources += gameState.playerIncome;
                addLog(`玩家获得${gameState.playerIncome}资源`, 'system');
            } else {
                gameState.aiResources += gameState.aiIncome;
                addLog(`AI获得${gameState.aiIncome}资源`, 'system');
            }
            
            updateBoardDisplay();
            
            // 如果是AI回合
            if (gameState.currentPlayer === 'ai') {
                setTimeout(aiDecision, 1000);
            } else {
                addLog(`玩家回合开始 (回合 ${gameState.turn})`, 'player');
            }
        }
        
        // 初始化游戏
        function initGame() {
            initializeBoard();
            
            // 设置按钮事件
            document.getElementById('buildBasicTowerBtn').addEventListener('click', () => {
                addLog('点击地图上的空单元格来建造防御塔', 'system');
            });
            
            document.getElementById('buildAdvancedTowerBtn').addEventListener('click', () => {
                addLog('点击地图上的空单元格来建造防御塔', 'system');
            });
            
            document.getElementById('sendBasicUnitBtn').addEventListener('click', () => {
                sendUnit('player', 'basic');
            });
            
            document.getElementById('sendAdvancedUnitBtn').addEventListener('click', () => {
                sendUnit('player', 'advanced');
            });
            
            document.getElementById('upgradeEconomyBtn').addEventListener('click', () => {
                upgradeEconomy('player');
            });
            
            document.getElementById('researchTechBtn').addEventListener('click', () => {
                researchTech('player');
            });
            
            document.getElementById('specialAbilityBtn').addEventListener('click', () => {
                if (gameState.playerResources >= 200) {
                    gameState.playerResources -= 200;
                    addLog('玩家使用了特殊能力!', 'player');
                    // 这里可以添加特殊能力效果
                }
            });
            
            document.getElementById('endTurnBtn').addEventListener('click', endTurn);
            
            addLog('游戏开始! 玩家先行动', 'system');
        }
        
        // 启动游戏
        window.onload = initGame;
    </script>
</body>
</html>
```