<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>🎮 МАЙНКРАФТ - ЛОББИ + УДАЛЕНИЕ МИРА (ФИКС)</title>
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            box-sizing: border-box;
        }
        body {
            background: #0a0a1a;
            font-family: system-ui, monospace;
            margin: 0;
            padding: 12px;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .container {
            background: #0a0a15;
            border-radius: 24px;
            padding: 15px;
            border: 2px solid #e94560;
            max-width: 550px;
            width: 100%;
        }
        .lobby, .game { display: none; }
        .lobby.active, .game.active { display: block; }
        h2 {
            color: #ffd966;
            text-align: center;
            margin: 0 0 15px 0;
            font-size: 22px;
        }
        .world-list {
            margin: 15px 0;
            max-height: 350px;
            overflow-y: auto;
        }
        .world-item {
            background: #1a1a2e;
            border-radius: 16px;
            padding: 12px;
            margin: 8px 0;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            border: 1px solid #e94560;
        }
        .world-name { font-weight: bold; color: #ffd966; font-size: 14px; }
        .world-info { font-size: 10px; color: #aaa; margin-top: 4px; }
        .join-btn, .delete-btn {
            border: none;
            padding: 6px 12px;
            border-radius: 20px;
            color: white;
            cursor: pointer;
            font-size: 12px;
            margin-left: 5px;
        }
        .join-btn { background: #e94560; }
        .delete-btn { background: #8b0000; }
        input {
            background: #1a1a2e;
            border: 1px solid #e94560;
            padding: 10px;
            border-radius: 30px;
            color: white;
            width: 100%;
            margin: 8px 0;
            text-align: center;
            font-size: 14px;
        }
        button {
            background: #e94560;
            border: none;
            padding: 10px;
            border-radius: 30px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            width: 100%;
            margin: 5px 0;
            font-size: 14px;
        }
        button:active { transform: scale(0.96); }
        .create-btn { background: #8b00ff; border: 1px solid gold; }
        .back-btn { background: #333; margin-top: 10px; }
        .stats {
            display: flex;
            gap: 8px;
            margin-bottom: 12px;
            justify-content: center;
            flex-wrap: wrap;
        }
        .stat {
            background: #1a1a2e;
            padding: 5px 12px;
            border-radius: 20px;
            color: #ffd966;
            font-size: 12px;
            font-weight: bold;
        }
        canvas {
            display: block;
            width: 100%;
            height: auto;
            background: #1a3a2a;
            border-radius: 12px;
            margin-bottom: 12px;
            cursor: crosshair;
        }
        .hotbar {
            display: flex;
            gap: 6px;
            justify-content: center;
            margin-bottom: 12px;
            flex-wrap: wrap;
        }
        .slot {
            background: #1a1a2e;
            border: 2px solid #8b5a2b;
            border-radius: 10px;
            width: 50px;
            padding: 4px;
            text-align: center;
            cursor: pointer;
        }
        .slot.selected { border: 2px solid gold; box-shadow: 0 0 8px gold; }
        .slot-emoji { font-size: 22px; }
        .slot-count { font-size: 9px; color: #ffd966; }
        .action-buttons {
            display: flex;
            gap: 8px;
            justify-content: center;
            margin-bottom: 12px;
            flex-wrap: wrap;
        }
        .action-btn {
            background: #1a1a2e;
            border: 1px solid #e94560;
            padding: 10px 12px;
            border-radius: 40px;
            font-size: 13px;
            font-weight: bold;
            color: white;
            cursor: pointer;
            text-align: center;
            flex: 1;
            min-width: 70px;
        }
        .action-btn:active { background: #e94560; transform: scale(0.96); }
        .target-info {
            text-align: center;
            background: #0a0a10;
            padding: 6px;
            border-radius: 20px;
            font-size: 11px;
            color: #ffd966;
            margin-top: 10px;
        }
        .coord-box {
            position: fixed;
            bottom: 120px;
            left: 20px;
            background: #1a1a2e;
            padding: 6px 12px;
            border-radius: 20px;
            border: 1px solid #e94560;
            font-size: 11px;
            color: #ffd966;
            font-weight: bold;
            z-index: 25;
            pointer-events: none;
        }
        .joystick-base {
            position: fixed;
            bottom: 20px;
            left: 20px;
            width: 90px;
            height: 90px;
            background: rgba(30,30,50,0.85);
            border-radius: 55px;
            border: 2px solid #e94560;
            touch-action: none;
            z-index: 20;
        }
        .joystick-thumb {
            position: absolute;
            width: 40px;
            height: 40px;
            background: #e94560;
            border-radius: 25px;
            top: 25px;
            left: 25px;
            touch-action: none;
            z-index: 21;
        }
        .craft-toggle {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: #8b00ff;
            border: 1px solid gold;
            border-radius: 40px;
            padding: 8px 14px;
            font-size: 12px;
            color: white;
            cursor: pointer;
            z-index: 15;
        }
        .craft-panel {
            position: fixed;
            bottom: 120px;
            right: 10px;
            background: #1a1a2e;
            border: 2px solid gold;
            border-radius: 16px;
            padding: 10px;
            width: 190px;
            z-index: 25;
            display: none;
        }
        .craft-panel.show { display: block; }
        .craft-item {
            background: #2a2a3e;
            border: 1px solid #e94560;
            border-radius: 10px;
            padding: 8px;
            margin: 6px 0;
            cursor: pointer;
            text-align: center;
            font-size: 11px;
            color: #ffd966;
            font-weight: bold;
        }
    </style>
</head>
<body>
<div class="container">
    <!-- ЛОББИ -->
    <div id="lobby" class="lobby active">
        <h2>🏠 ЛОББИ МИРОВ</h2>
        <input type="text" id="newWorldName" placeholder="Название нового мира">
        <button id="createWorldBtn" class="create-btn">✨ СОЗДАТЬ НОВЫЙ МИР</button>
        <div class="world-list" id="worldList"></div>
        <button id="joinByIdBtn">🔗 ВОЙТИ ПО ID</button>
        <input type="text" id="joinWorldId" placeholder="ID мира">
    </div>

    <!-- ИГРА -->
    <div id="game" class="game">
        <div class="stats">
            <div class="stat">❤️ <span id="hp">20</span></div>
            <div class="stat">🗡️ <span id="dmg">1</span></div>
            <div class="stat">⭐ XP: <span id="xp">0</span></div>
        </div>
        
        <canvas id="canvas" width="500" height="450"></canvas>
        
        <div class="hotbar" id="hotbar"></div>
        
        <div class="action-buttons">
            <div class="action-btn" id="mineBtn">⛏️ КОПАТЬ</div>
            <div class="action-btn" id="placeBtn">🧱 СТРОИТЬ</div>
            <div class="action-btn" id="jumpBtn">🦘 ПРЫЖОК</div>
            <div class="action-btn" id="attackBtn">⚔️ АТАКА</div>
        </div>
        
        <div class="target-info" id="targetInfo">🎯 Наведи на блок</div>
        <button id="exitToLobby" class="back-btn">🚪 ВЫЙТИ В ЛОББИ</button>
    </div>
    
    <div class="coord-box" id="coordBox">📍 X: 0, Y: 0</div>
    <div class="joystick-base" id="joystickBase">
        <div class="joystick-thumb" id="joystickThumb"></div>
    </div>
    <div class="craft-toggle" id="craftToggle">🔨 КРАФТ</div>
    <div class="craft-panel" id="craftPanel">
        <div style="text-align:center; color:gold; font-size:13px;">🔧 КРАФТ</div>
        <div class="craft-item" data-craft="planks">🪵 ДОСКА (1 дерево → 4)</div>
        <div class="craft-item" data-craft="stick">🥢 ПАЛКА (2 доски → 4)</div>
        <div class="craft-item" data-craft="pickaxe">⛏️ КИРКА (3 камень + 2 палки)</div>
        <div class="craft-item" data-craft="sword">⚔️ МЕЧ (2 железо + 1 палка)</div>
    </div>
</div>

<script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const W = 500, H = 450;
    canvas.width = W;
    canvas.height = H;
    
    // ========== МИРЫ ==========
    let worlds = JSON.parse(localStorage.getItem('minecraft_worlds_list')) || {};
    let currentWorldId = null;
    let gameActive = false;
    
    // ========== ИГРОВЫЕ ПЕРЕМЕННЫЕ ==========
    let world = {};
    const worldWidth = 120, worldHeight = 80;
    let cameraX = 0, cameraY = 0;
    let targetBlockX = 10, targetBlockY = 20;
    
    const blocks = {
        0: { name: 'ВОЗДУХ', solid: false, emoji: '⬜', color: '#1a1a2a', drop: null, xp: 0 },
        1: { name: 'ТРАВА', solid: true, emoji: '🟩', color: '#4a8a4a', drop: 1, xp: 0 },
        2: { name: 'ЗЕМЛЯ', solid: true, emoji: '🟫', color: '#8a5a3a', drop: 2, xp: 0 },
        3: { name: 'КАМЕНЬ', solid: true, emoji: '⬜', color: '#6a6a6a', drop: 3, xp: 0 },
        4: { name: 'ДРЕВЕСИНА', solid: true, emoji: '🪵', color: '#8a5a2a', drop: 4, xp: 2 },
        5: { name: 'ДОСКА', solid: true, emoji: '🪵', color: '#a0724a', drop: 5, xp: 0 },
        6: { name: 'ЛИСТВА', solid: false, emoji: '🍃', color: '#3a8a3a', drop: null, xp: 0 },
        7: { name: 'ПЕСОК', solid: true, emoji: '🟨', color: '#d4c47a', drop: 7, xp: 0 },
        8: { name: 'УГОЛЬ', solid: true, emoji: '⚫', color: '#4a4a4a', drop: 8, xp: 5 },
        9: { name: 'ЖЕЛЕЗО', solid: true, emoji: '🔩', color: '#b0b0b0', drop: 9, xp: 10 },
        10: { name: 'ЗОЛОТО', solid: true, emoji: '🪙', color: '#d4af37', drop: 10, xp: 15 },
        11: { name: 'АЛМАЗ', solid: true, emoji: '💎', color: '#4aebff', drop: 11, xp: 30 },
        12: { name: 'БЕДРОК', solid: true, emoji: '🪨', color: '#aa4444', drop: null, xp: 0 }
    };
    
    let inventory = [];
    let selectedSlot = 0;
    let playerDamage = 1;
    let xp = 0;
    let monsters = [];
    let isAttacking = false, isMining = false;
    let moveLeft = false, moveRight = false;
    let vy = 0, onGround = true;
    let player = { x: 200, y: 200, hp: 20 };
    
    const recipes = {
        planks: { name: 'ДОСКА', need: { 4: 1 }, result: { item: 5, count: 4 } },
        stick: { name: 'ПАЛКА', need: { 5: 2 }, result: { item: 14, count: 4 } },
        pickaxe: { name: 'КИРКА', need: { 3: 3, 14: 2 }, result: { tool: true } },
        sword: { name: 'МЕЧ', need: { 9: 2, 14: 1 }, result: { dmg: 5 } }
    };
    
    function generateWorldFor(worldId) {
        let newWorld = {};
        let baseHeight = 45;
        for(let x = 0; x < worldWidth; x++) {
            let height = baseHeight + Math.sin(x * 0.08) * 4 + Math.cos(x * 0.12) * 2;
            height = Math.floor(height);
            for(let y = 0; y < worldHeight; y++) {
                if(y >= worldHeight - 4) newWorld[`${x},${y}`] = 12;
                else if(y > height + 4) newWorld[`${x},${y}`] = 3;
                else if(y > height) newWorld[`${x},${y}`] = 2;
                else if(y === height) newWorld[`${x},${y}`] = 1;
                else newWorld[`${x},${y}`] = 0;
            }
            if(x > 5 && x < worldWidth - 5 && Math.random() < 0.06) {
                let treeHeight = 3 + Math.floor(Math.random() * 3);
                for(let t = 0; t < treeHeight; t++) {
                    let yPos = height - 1 - t;
                    if(yPos > 0) newWorld[`${x},${yPos}`] = 4;
                }
                for(let lx = -1; lx <= 1; lx++) {
                    for(let ly = -2; ly <= 0; ly++) {
                        let yPos = height - 3 + ly;
                        let xPos = x + lx;
                        if(xPos > 0 && xPos < worldWidth && yPos > 0 && yPos < worldHeight) {
                            if(Math.random() < 0.6) newWorld[`${xPos},${yPos}`] = 6;
                        }
                    }
                }
            }
            for(let y = 0; y < worldHeight - 5; y++) {
                if(newWorld[`${x},${y}`] === 3 && Math.random() < 0.015) {
                    let depth = worldHeight - y;
                    if(depth > 10 && depth < 40 && Math.random() < 0.4) newWorld[`${x},${y}`] = 8;
                    else if(depth > 20 && depth < 60 && Math.random() < 0.2) newWorld[`${x},${y}`] = 9;
                    else if(depth > 35 && depth < 80 && Math.random() < 0.08) newWorld[`${x},${y}`] = 10;
                    else if(depth > 50 && Math.random() < 0.03) newWorld[`${x},${y}`] = 11;
                }
            }
        }
        return newWorld;
    }
    
    function getBlock(x, y) {
        if(x < 0 || x >= worldWidth || y < 0 || y >= worldHeight) return 0;
        return world[`${x},${y}`] || 0;
    }
    
    function setBlock(x, y, block) {
        if(x < 0 || x >= worldWidth || y < 0 || y >= worldHeight) return;
        if(getBlock(x, y) === 12) return;
        world[`${x},${y}`] = block;
        saveCurrentWorld();
    }
    
    function saveCurrentWorld() {
        if(currentWorldId && worlds[currentWorldId]) {
            worlds[currentWorldId].world = world;
            worlds[currentWorldId].player = { x: player.x, y: player.y, hp: player.hp, xp: xp, inventory: inventory, playerDamage: playerDamage };
            localStorage.setItem('minecraft_worlds_list', JSON.stringify(worlds));
        }
    }
    
    function loadWorld(worldId) {
        let w = worlds[worldId];
        if(w && w.world) {
            world = w.world;
            inventory = w.player?.inventory || [{ id: 4, count: 5 }, { id: 3, count: 20 }];
            xp = w.player?.xp || 0;
            playerDamage = w.player?.playerDamage || 1;
            player.x = w.player?.x || 200;
            player.y = w.player?.y || 200;
            player.hp = w.player?.hp || 20;
        } else {
            world = generateWorldFor(worldId);
            inventory = [{ id: 4, count: 5 }, { id: 3, count: 20 }];
            xp = 0;
            playerDamage = 1;
            player.x = 200;
            player.y = 200;
            player.hp = 20;
            worlds[worldId] = { world: world, player: { x: player.x, y: player.y, hp: player.hp, xp: xp, inventory: inventory, playerDamage: playerDamage } };
            localStorage.setItem('minecraft_worlds_list', JSON.stringify(worlds));
        }
        monsters = [];
        updateUI();
        updateHotbar();
        for(let y = 0; y < worldHeight; y++) {
            let block = getBlock(Math.floor(player.x / 16), y);
            if(block !== 0 && blocks[block].solid) {
                player.y = y * 16 - 12;
                break;
            }
        }
    }
    
    function deleteWorld(worldId) {
        if(confirm(`Удалить мир "${worldId}"? Весь прогресс пропадёт!`)) {
            delete worlds[worldId];
            localStorage.setItem('minecraft_worlds_list', JSON.stringify(worlds));
            renderWorldList();
            if(currentWorldId === worldId) exitToLobby();
        }
    }
    
    function updateUI() {
        document.getElementById('hp').innerHTML = Math.floor(player.hp);
        document.getElementById('dmg').innerHTML = playerDamage;
        document.getElementById('xp').innerHTML = xp;
    }
    
    function updateHotbar() {
        const container = document.getElementById('hotbar');
        container.innerHTML = '';
        for(let i = 0; i < Math.min(inventory.length, 8); i++) {
            let item = inventory[i];
            let block = blocks[item.id] || { emoji: '📦' };
            container.innerHTML += `<div class="slot ${selectedSlot === i ? 'selected' : ''}" data-slot="${i}"><div class="slot-emoji">${block.emoji}</div><div class="slot-count">${item.count}</div></div>`;
        }
        document.querySelectorAll('.slot').forEach(slot => {
            slot.addEventListener('click', () => { selectedSlot = parseInt(slot.dataset.slot); updateHotbar(); });
        });
    }
    
    function addXP(amount) { xp += amount; document.getElementById('xp').innerHTML = xp; }
    
    function craftItem(craftId) {
        let recipe = recipes[craftId];
        if(!recipe) return;
        for(let [id, need] of Object.entries(recipe.need)) {
            let has = inventory.find(i => i.id == id)?.count || 0;
            if(has < need) { alert(`Не хватает ${blocks[id]?.name}`); return; }
        }
        for(let [id, need] of Object.entries(recipe.need)) {
            let inv = inventory.find(i => i.id == id);
            if(inv) { inv.count -= need; if(inv.count <= 0) inventory.splice(inventory.indexOf(inv), 1); }
        }
        if(recipe.result.dmg) playerDamage = recipe.result.dmg;
        if(recipe.result.item) {
            let existing = inventory.find(i => i.id == recipe.result.item);
            if(existing) existing.count += recipe.result.count;
            else inventory.push({ id: recipe.result.item, count: recipe.result.count });
        }
        updateHotbar();
        updateUI();
    }
    
    // ========== ФИЗИКА ==========
    function updatePhysics() {
        let oldX = player.x, oldY = player.y;
        let speed = 3;
        if(moveLeft) player.x -= speed;
        if(moveRight) player.x += speed;
        
        let leftTile = Math.floor((player.x - 8) / 16);
        let rightTile = Math.floor((player.x + 8) / 16);
        let topTile = Math.floor((player.y - 12) / 16);
        let bottomTile = Math.floor((player.y + 12) / 16);
        
        for(let x = leftTile; x <= rightTile; x++) {
            for(let y = topTile; y <= bottomTile; y++) {
                let block = getBlock(x, y);
                if(block !== 0 && blocks[block].solid) {
                    let blockLeft = x * 16, blockRight = x * 16 + 16;
                    if(player.x + 8 > blockLeft && player.x - 8 < blockRight) {
                        if(player.x < blockLeft) player.x = blockLeft - 8;
                        if(player.x > blockRight) player.x = blockRight + 8;
                    }
                }
            }
        }
        
        vy += 0.5;
        player.y += vy;
        
        leftTile = Math.floor((player.x - 8) / 16);
        rightTile = Math.floor((player.x + 8) / 16);
        topTile = Math.floor((player.y - 12) / 16);
        bottomTile = Math.floor((player.y + 12) / 16);
        
        let newY = player.y;
        onGround = false;
        for(let x = leftTile; x <= rightTile; x++) {
            for(let y = topTile; y <= bottomTile; y++) {
                let block = getBlock(x, y);
                if(block !== 0 && blocks[block].solid) {
                    let blockTop = y * 16, blockBottom = y * 16 + 16;
                    if(vy > 0 && oldY + 12 <= blockTop && player.y + 12 > blockTop) {
                        newY = blockTop - 12;
                        vy = 0;
                        onGround = true;
                    } else if(vy < 0 && oldY - 12 >= blockBottom && player.y - 12 < blockBottom) {
                        newY = blockBottom + 12;
                        vy = 0;
                    }
                }
            }
        }
        player.y = newY;
        
        player.x = Math.min(Math.max(player.x, 12), worldWidth * 16 - 28);
        player.y = Math.min(Math.max(player.y, 12), worldHeight * 16 - 28);
        
        cameraX = player.x - W/2;
        cameraY = player.y - H/2;
        cameraX = Math.min(Math.max(cameraX, 0), worldWidth * 16 - W);
        cameraY = Math.min(Math.max(cameraY, 0), worldHeight * 16 - H);
        
        document.getElementById('coordBox').innerHTML = `📍 X: ${Math.floor(player.x/16)}, Y: ${Math.floor(player.y/16)}`;
    }
    
    function jump() { 
        if(onGround) { 
            vy = -8; 
            onGround = false; 
        } 
    }
    
    function updateTargetFromScreen(screenX, screenY) {
        let worldX = screenX + cameraX;
        let worldY = screenY + cameraY;
        targetBlockX = Math.floor(worldX / 16);
        targetBlockY = Math.floor(worldY / 16);
        targetBlockX = Math.min(Math.max(targetBlockX, 0), worldWidth - 1);
        targetBlockY = Math.min(Math.max(targetBlockY, 0), worldHeight - 1);
    }
    
    function mineBlock() {
        isMining = true;
        setTimeout(() => { isMining = false; }, 150);
        let block = getBlock(targetBlockX, targetBlockY);
        if(block === 0 || block === 12) return;
        let px = player.x / 16, py = player.y / 16;
        if(Math.abs(px - targetBlockX) > 3.5 || Math.abs(py - targetBlockY) > 3.5) return;
        if(blocks[block].xp > 0) addXP(blocks[block].xp);
        if(blocks[block].drop) {
            let added = false;
            for(let inv of inventory) {
                if(inv.id === blocks[block].drop) { inv.count++; added = true; break; }
            }
            if(!added) inventory.push({ id: blocks[block].drop, count: 1 });
        }
        setBlock(targetBlockX, targetBlockY, 0);
        updateHotbar();
    }
    
    function placeBlock() {
        if(selectedSlot >= inventory.length) return;
        let item = inventory[selectedSlot];
        if(!item || item.count <= 0) return;
        let blockId = item.id;
        if(!blocks[blockId]?.solid) return;
        let px = player.x / 16, py = player.y / 16;
        if(Math.abs(px - targetBlockX) > 3.5 || Math.abs(py - targetBlockY) > 3.5) return;
        if(getBlock(targetBlockX, targetBlockY) === 0) {
            setBlock(targetBlockX, targetBlockY, blockId);
            item.count--;
            if(item.count === 0) inventory.splice(selectedSlot, 1);
            updateHotbar();
        }
    }
    
    function attackMonster() {
        isAttacking = true;
        setTimeout(() => { isAttacking = false; }, 150);
        for(let i = 0; i < monsters.length; i++) {
            let m = monsters[i];
            let dist = Math.hypot(player.x - m.x, player.y - m.y);
            if(dist < 30) {
                m.hp -= playerDamage;
                if(m.hp <= 0) {
                    addXP(m.xp);
                    monsters.splice(i,1);
                }
                break;
            }
        }
    }
    
    function spawnMonster() {
        if(monsters.length > 6) return;
        let x = player.x + (Math.random() - 0.5) * 300;
        let y = player.y;
        for(let i = 0; i < 15; i++) {
            let block = getBlock(Math.floor(x/16), Math.floor((y + i*16)/16));
            if(block !== 0 && blocks[block].solid) { y = y + i*16 - 16; break; }
        }
        monsters.push({ x: x, y: y, hp: 30, maxHp: 30, emoji: '🧟', xp: 12 });
    }
    
    setInterval(() => { if(gameActive && Math.random() < 0.02) spawnMonster(); }, 5000);
    
    function updateMonsters() {
        for(let i = 0; i < monsters.length; i++) {
            let m = monsters[i];
            let dx = player.x - m.x, dy = player.y - m.y;
            let dist = Math.hypot(dx, dy);
            if(dist < 100) {
                m.x += Math.sign(dx) * 1;
                let below = getBlock(Math.floor(m.x/16), Math.floor((m.y+10)/16)+1);
                if(below === 0 || !blocks[below].solid) m.y += 2;
                else m.y = Math.floor(m.y/16)*16 + 10;
                if(dist < 25) {
                    player.hp -= 1;
                    if(player.hp <= 0) {
                        player.hp = 20;
                        player.x = 200;
                        player.y = 200;
                        xp = 0;
                        updateUI();
                    }
                    monsters.splice(i,1);
                    i--;
                }
            }
        }
        document.getElementById('hp').innerHTML = Math.floor(player.hp);
    }
    
    function draw() {
        ctx.fillStyle = '#5a8ad4';
        ctx.fillRect(0, 0, W, H);
        
        let startX = Math.max(0, Math.floor(cameraX / 16));
        let endX = Math.min(worldWidth, Math.floor((cameraX + W) / 16) + 2);
        let startY = Math.max(0, Math.floor(cameraY / 16));
        let endY = Math.min(worldHeight, Math.floor((cameraY + H) / 16) + 2);
        
        for(let x = startX; x < endX; x++) {
            for(let y = startY; y < endY; y++) {
                let block = getBlock(x, y);
                if(block !== 0) {
                    let screenX = x * 16 - cameraX;
                    let screenY = y * 16 - cameraY;
                    ctx.fillStyle = blocks[block].color;
                    ctx.fillRect(screenX, screenY, 16, 16);
                    ctx.fillStyle = 'rgba(0,0,0,0.2)';
                    ctx.fillRect(screenX + 2, screenY + 2, 12, 12);
                    ctx.font = '12px system-ui';
                    ctx.fillStyle = 'white';
                    ctx.fillText(blocks[block].emoji, screenX + 2, screenY + 13);
                }
            }
        }
        
        for(let m of monsters) {
            let screenX = m.x - cameraX;
            let screenY = m.y - cameraY;
            ctx.fillStyle = '#4a6a3a';
            ctx.beginPath();
            ctx.arc(screenX, screenY, 10, 0, Math.PI*2);
            ctx.fill();
            ctx.font = '22px system-ui';
            ctx.fillStyle = 'white';
            ctx.fillText('🧟', screenX - 8, screenY + 8);
            ctx.fillStyle = '#ff6666';
            ctx.fillRect(screenX - 10, screenY - 15, 20 * (m.hp/m.maxHp), 4);
        }
        
        let screenX = player.x - cameraX;
        let screenY = player.y - cameraY;
        
        if(isAttacking) {
            ctx.beginPath();
            ctx.moveTo(screenX + 10, screenY - 5);
            ctx.lineTo(screenX + 40, screenY - 15);
            ctx.lineTo(screenX + 35, screenY + 5);
            ctx.fillStyle = '#ffaa66';
            ctx.fill();
        }
        if(isMining) {
            ctx.beginPath();
            ctx.moveTo(screenX + 8, screenY + 5);
            ctx.lineTo(screenX + 30, screenY + 15);
            ctx.lineTo(screenX + 22, screenY + 22);
            ctx.fillStyle = '#cccc99';
            ctx.fill();
        }
        
        ctx.fillStyle = '#ffcc00';
        ctx.beginPath();
        ctx.arc(screenX, screenY, 10, 0, Math.PI*2);
        ctx.fill();
        ctx.fillStyle = '#8b5a2b';
        ctx.fillRect(screenX - 4, screenY - 7, 8, 10);
        ctx.fillStyle = 'white';
        ctx.beginPath();
        ctx.arc(screenX - 4, screenY - 2, 2, 0, Math.PI*2);
        ctx.fill();
        ctx.beginPath();
        ctx.arc(screenX + 4, screenY - 2, 2, 0, Math.PI*2);
        ctx.fill();
        ctx.fillStyle = 'black';
        ctx.beginPath();
        ctx.arc(screenX - 4, screenY - 2, 1, 0, Math.PI*2);
        ctx.fill();
        ctx.beginPath();
        ctx.arc(screenX + 4, screenY - 2, 1, 0, Math.PI*2);
        ctx.fill();
        
        let targetScreenX = targetBlockX * 16 - cameraX;
        let targetScreenY = targetBlockY * 16 - cameraY;
        let targetBlock = getBlock(targetBlockX, targetBlockY);
        ctx.strokeStyle = 'gold';
        ctx.lineWidth = 2;
        ctx.strokeRect(targetScreenX, targetScreenY, 16, 16);
        
        document.getElementById('targetInfo').innerHTML = `🎯 ${blocks[targetBlock]?.emoji || '⬜'} ${blocks[targetBlock]?.name || 'ВОЗДУХ'}`;
    }
    
    // ========== ЛОББИ ==========
    function renderWorldList() {
        const list = document.getElementById('worldList');
        list.innerHTML = '';
        if(Object.keys(worlds).length === 0) {
            list.innerHTML = '<div style="color:#aaa; text-align:center; padding:20px;">Нет миров. Создайте первый!</div>';
            return;
        }
        for(let id in worlds) {
            let w = worlds[id];
            list.innerHTML += `
                <div class="world-item" data-id="${id}">
                    <div>
                        <div class="world-name">📁 ${id}</div>
                        <div class="world-info">⭐ XP: ${w.player?.xp || 0} | 🗡️ Урон: ${w.player?.playerDamage || 1}</div>
                    </div>
                    <div>
                        <button class="join-btn" data-id="${id}">ВОЙТИ</button>
                        <button class="delete-btn" data-id="${id}">🗑️</button>
                    </div>
                </div>
            `;
        }
        document.querySelectorAll('.join-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                e.stopPropagation();
                enterWorld(btn.dataset.id);
            });
        });
        document.querySelectorAll('.delete-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                e.stopPropagation();
                deleteWorld(btn.dataset.id);
            });
        });
        document.querySelectorAll('.world-item').forEach(item => {
            item.addEventListener('click', () => enterWorld(item.dataset.id));
        });
    }
    
    function createNewWorld() {
        let name = document.getElementById('newWorldName').value.trim();
        if(!name) name = 'Мир_' + Math.floor(Math.random() * 1000);
        if(worlds[name]) { alert('Мир с таким названием уже существует!'); return; }
        enterWorld(name);
    }
    
    function joinById() {
        let id = document.getElementById('joinWorldId').value.trim();
        if(!id) return;
        if(!worlds[id] && !confirm('Мир не найден. Создать новый?')) return;
        enterWorld(id);
    }
    
    function enterWorld(worldId) {
        currentWorldId = worldId;
        loadWorld(worldId);
        gameActive = true;
        document.getElementById('lobby').classList.remove('active');
        document.getElementById('game').classList.add('active');
        moveLeft = false; moveRight = false; vy = 0;
        draw();
    }
    
    function exitToLobby() {
        saveCurrentWorld();
        gameActive = false;
        currentWorldId = null;
        document.getElementById('game').classList.remove('active');
        document.getElementById('lobby').classList.add('active');
        renderWorldList();
    }
    
    document.getElementById('createWorldBtn').addEventListener('click', createNewWorld);
    document.getElementById('joinByIdBtn').addEventListener('click', joinById);
    document.getElementById('exitToLobby').addEventListener('click', exitToLobby);
    
    // ========== УПРАВЛЕНИЕ ==========
    // Кнопка прыжка (отдельно, без джойстика)
    document.getElementById('jumpBtn').addEventListener('click', () => { if(gameActive) jump(); });
    
    // Остальные кнопки
    document.getElementById('mineBtn').addEventListener('click', () => { if(gameActive) mineBlock(); draw(); });
    document.getElementById('placeBtn').addEventListener('click', () => { if(gameActive) placeBlock(); draw(); });
    document.getElementById('attackBtn').addEventListener('click', () => { if(gameActive) attackMonster(); });
    
    // ДЖОЙСТИК (движение)
    const joystickBase = document.getElementById('joystickBase');
    const joystickThumb = document.getElementById('joystickThumb');
    let active = false;
    
    function updateJoystick(e) {
        if(!active || !gameActive) return;
        const touch = e.touches ? e.touches[0] : e;
        const rect = joystickBase.getBoundingClientRect();
        const centerX = rect.left + rect.width/2;
        let dx = touch.clientX - centerX;
        let maxDist = rect.width/2 - 20;
        if(Math.abs(dx) > maxDist) dx = dx > 0 ? maxDist : -maxDist;
        joystickThumb.style.transform = `translate(${dx}px, 0px)`;
        if(dx < -10) { moveLeft = true; moveRight = false; }
        else if(dx > 10) { moveRight = true; moveLeft = false; }
        else { moveLeft = false; moveRight = false; }
    }
    function startJoystick(e) { 
        active = true; 
        updateJoystick(e); 
        e.preventDefault(); 
    }
    function endJoystick(e) { 
        active = false; 
        joystickThumb.style.transform = 'translate(0px, 0px)'; 
        moveLeft = false; 
        moveRight = false; 
        e.preventDefault(); 
    }
    joystickBase.addEventListener('touchstart', startJoystick);
    joystickBase.addEventListener('touchmove', updateJoystick);
    joystickBase.addEventListener('touchend', endJoystick);
    
    // Клавиатура для ПК
    window.addEventListener('keydown', (e) => {
        if(!gameActive) return;
        if(e.key === 'ArrowLeft') { moveLeft = true; moveRight = false; }
        if(e.key === 'ArrowRight') { moveRight = true; moveLeft = false; }
        if(e.key === 'ArrowUp') jump();
        if(e.key === ' ') { e.preventDefault(); attackMonster(); }
    });
    window.addEventListener('keyup', (e) => {
        if(e.key === 'ArrowLeft') moveLeft = false;
        if(e.key === 'ArrowRight') moveRight = false;
    });
    
    function handleCanvasTap(e) {
        if(!gameActive) return;
        const rect = canvas.getBoundingClientRect();
        const scaleX = canvas.width / rect.width, scaleY = canvas.height / rect.height;
        let clientX, clientY;
        if(e.touches) { clientX = e.touches[0].clientX; clientY = e.touches[0].clientY; e.preventDefault(); }
        else { clientX = e.clientX; clientY = e.clientY; }
        let canvasX = (clientX - rect.left) * scaleX, canvasY = (clientY - rect.top) * scaleY;
        updateTargetFromScreen(canvasX, canvasY);
        draw();
    }
    canvas.addEventListener('click', handleCanvasTap);
    canvas.addEventListener('touchstart', handleCanvasTap);
    
    const craftPanel = document.getElementById('craftPanel');
    document.getElementById('craftToggle').addEventListener('click', () => craftPanel.classList.toggle('show'));
    document.querySelectorAll('.craft-item').forEach(item => {
        item.addEventListener('click', () => {
            if(gameActive) craftItem(item.dataset.craft);
            craftPanel.classList.remove('show');
        });
    });
    
    function gameLoop() {
        if(gameActive) {
            updatePhysics();
            updateMonsters();
        }
        draw();
        requestAnimationFrame(gameLoop);
    }
    
    renderWorldList();
    gameLoop();
</script>
</body>
</html>