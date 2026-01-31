<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CS2 Simulator V29 - Login & Register</title>
    <style>
        :root {
            --bg: #0b0b0b;
            --card: #151515;
            --accent: #ff9d00;
            --blue: #4b69ff;
            --purple: #8847ff;
            --pink: #d32ce6;
            --red: #eb4b4b;
            --gold: #caab05;
            --text: #e0e0e0;
            --upgrade-grad: linear-gradient(180deg, #00ff44 0%, #008822 100%);
        }

        body {
            background-color: var(--bg);
            color: var(--text);
            font-family: 'Segoe UI', sans-serif;
            margin: 0;
            padding-bottom: 90px;
            overflow-x: hidden;
        }

        /* Auth Screen */
        #auth-screen { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; justify-content: center; align-items: center; }
        .auth-box { background: #111; padding: 30px; border-radius: 15px; border: 1px solid #333; text-align: center; width: 300px; }
        .auth-tabs { display: flex; gap: 10px; margin-bottom: 20px; }
        .auth-tab { flex: 1; padding: 8px; background: #222; cursor: pointer; border-radius: 5px; font-size: 0.8rem; border: 1px solid transparent; }
        .auth-tab.active { background: var(--accent); color: #000; font-weight: bold; }
        
        input, select { background: #222; border: 1px solid #444; color: white; padding: 10px; border-radius: 5px; width: 100%; margin-bottom: 10px; box-sizing: border-box; }
        
        .btn { background: var(--accent); color: #000; border: none; padding: 10px 15px; font-weight: bold; cursor: pointer; border-radius: 5px; text-transform: uppercase; font-size: 0.75rem; transition: 0.2s; }
        .btn:active { transform: scale(0.95); }

        /* UI */
        .header { padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; background: var(--card); border-bottom: 1px solid #333; }
        .balance { color: var(--accent); font-weight: bold; font-size: 1.2rem; }

        .page { display: none; padding: 20px; animation: fadeIn 0.2s; }
        .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        .case-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 15px; }
        .inv-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(110px, 1fr)); gap: 10px; }
        .card { background: var(--card); border: 1px solid #222; padding: 15px; text-align: center; border-radius: 10px; cursor: pointer; }
        .skin-card { background: #1a1a1a; padding: 8px; border-radius: 5px; font-size: 0.65rem; border-bottom: 4px solid #ccc; text-align: center; cursor: pointer; }

        .Mil-Spec { border-color: var(--blue) !important; }
        .Restricted { border-color: var(--purple) !important; }
        .Classified { border-color: var(--pink) !important; }
        .Covert { border-color: var(--red) !important; }
        .Gold { border-color: var(--gold) !important; }

        .spinner-wrap { position: relative; width: 100%; height: 120px; background: #050505; border: 2px solid #222; margin: 20px 0; overflow: hidden; border-radius: 10px; }
        .spinner-line { position: absolute; left: 50%; top: 0; bottom: 0; width: 3px; background: var(--accent); z-index: 10; transform: translateX(-50%); box-shadow: 0 0 10px var(--accent); }
        .spinner-track { display: flex; position: absolute; left: 0; top: 10px; transition: transform 5s cubic-bezier(0.1, 0, 0.1, 1); will-change: transform; align-items: center; }
        .spinner-item { min-width: 100px; max-width: 100px; height: 100px; margin: 0 5px; background: #111; border-bottom: 4px solid #fff; display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 0.5rem; text-align: center; padding: 5px; box-sizing: border-box; flex-shrink: 0; }

        .upgrade-container { display: flex; justify-content: space-around; align-items: center; margin-top: 30px; gap: 15px; }
        .upg-slot { flex: 1; height: 150px; border: 2px dashed #333; border-radius: 15px; background: #0a0a0a; display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center; cursor: pointer; transition: 0.3s; padding: 10px; }
        .upg-slot.filled { border-style: solid; border-color: #00ff44; background: #111; }
        .upg-chance-box { text-align: center; }
        .upg-circle { width: 70px; height: 70px; border: 4px solid #00ff44; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: bold; font-size: 1.2rem; color: #00ff44; margin-bottom: 5px; }

        #admin-tag { display: none; color: var(--red); font-size: 3rem; font-weight: 900; text-align: center; margin-bottom: 20px; }
        #admin-btn-trigger { position: fixed; bottom: 90px; right: 10px; color: #333; font-size: 0.6rem; cursor: pointer; z-index: 2000; border: none; background: transparent; }

        .bottom-nav { position: fixed; bottom: 0; width: 100%; height: 80px; background: #000; display: flex; justify-content: space-around; align-items: center; border-top: 2px solid #222; z-index: 1000; padding: 0 10px; box-sizing: border-box; }
        .nav-item { flex: 1; margin: 0 5px; height: 55px; background: var(--upgrade-grad); color: white !important; font-weight: bold; border-radius: 12px; display: flex; align-items: center; justify-content: center; box-shadow: 0 4px 15px rgba(0,255,68,0.2); border: 2px solid #00ff44; cursor: pointer; font-size: 0.7rem; text-transform: uppercase; }
        .nav-item.active { filter: brightness(1.3); border-color: #fff; }

        .modal { position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 2000; display: none; justify-content: center; align-items: center; }
        .modal-box { background: #111; border: 1px solid #333; padding: 20px; border-radius: 12px; width: 90%; max-width: 450px; text-align: center; }
    </style>
</head>
<body>

<button id="admin-btn-trigger" onclick="adminAuth()">админ</button>

<div id="auth-screen">
    <div class="auth-box">
        <h2 style="color:var(--accent); margin-bottom:15px;">CS2 SIMULATOR</h2>
        <div class="auth-tabs">
            <div id="tab-login" class="auth-tab active" onclick="switchAuth('login')">ВХОД</div>
            <div id="tab-reg" class="auth-tab" onclick="switchAuth('reg')">РЕГИСТРАЦИЯ</div>
        </div>
        <input type="text" id="user-nick" placeholder="Никнейм">
        <input type="password" id="user-pass" placeholder="Пароль">
        <button id="auth-btn" class="btn" style="width:100%" onclick="handleAuth()">Войти</button>
    </div>
</div>

<div class="header">
    <div id="display-name">...</div>
    <div class="balance"><span id="balance-val">0</span> ₽</div>
</div>

<!-- Модальные окна -->
<div id="admin-modal" class="modal">
    <div class="modal-box">
        <h3 style="color:var(--red)">ADMIN PANEL (232)</h3>
        <div id="adm-player-list" style="background:#080808; max-height:80px; overflow-y:auto; margin-bottom:10px; text-align:left; font-size:0.8rem;"></div>
        <input type="text" id="adm-target" placeholder="Ник игрока" readonly>
        <div style="display:flex; gap:5px; margin-bottom:10px;">
            <input type="number" id="adm-amount" placeholder="Сумма" style="margin:0;">
            <button class="btn" onclick="modifyBalance('add')">+</button>
            <button class="btn" onclick="modifyBalance('set')">=</button>
        </div>
        <select id="adm-item-select"></select>
        <button class="btn" style="width:100%; background:#fff; color:#000; margin-bottom:5px;" onclick="adminGiveItem()">ВЫДАТЬ</button>
        <button class="btn" style="width:100%; background:#222; color:#fff" onclick="closeModal('admin-modal')">ЗАКРЫТЬ</button>
    </div>
</div>

<div id="selection-modal" class="modal">
    <div class="modal-box">
        <h3 id="sel-title">Выбор предмета</h3>
        <div id="sel-grid" class="inv-grid" style="max-height:300px; overflow-y:auto; padding:10px; background:#050505;"></div>
        <button class="btn" style="width:100%; margin-top:10px; background:#444; color:#fff;" onclick="closeModal('selection-modal')">ОТМЕНА</button>
    </div>
</div>

<div id="case-modal" class="modal">
    <div class="modal-box">
        <h2 id="m-title">Кейс</h2>
        <div id="m-view">
            <div id="m-contents" class="inv-grid" style="max-height:200px; overflow-y:auto; background:#050505; padding:10px; margin-bottom:15px;"></div>
            <button class="btn" id="m-open-btn" style="width:100%">ОТКРЫТЬ</button>
            <button class="btn" style="background:#222; color:#fff; width:100%; margin-top:5px;" onclick="closeModal('case-modal')">НАЗАД</button>
        </div>
        <div id="m-anim" style="display:none;">
            <div class="spinner-wrap">
                <div class="spinner-line"></div>
                <div class="spinner-track" id="spinner-track"></div>
            </div>
            <div id="m-win-info" style="margin-top:10px; font-weight:bold; min-height:40px;">открытие...</div>
            <button class="btn" id="m-claim-btn" style="width:100%; display:none;" onclick="closeModal('case-modal')">ЗАБРАТЬ</button>
        </div>
    </div>
</div>

<!-- Страницы -->
<div id="page-cases" class="page active">
    <h3>Магазин</h3>
    <div class="case-grid">
        <div class="card" onclick="showCaseInfo('Revolution')">
            <img src="https://community.cloudflare.steamstatic.com/public/images/economy/status/11d8868853b0068350616b349195971485c2b0e9.png" width="70">
            <p>Revolution<br>500 ₽</p>
        </div>
        <div class="card" onclick="showCaseInfo('Knives')">
            <img src="https://community.cloudflare.steamstatic.com/economy/image/-9a81dlWLwJ2UUGcVs_nsVtzdOEdtWwKGZZLQHTxDZ7I56KU0Zwwo4NUX4oFJZEHLbXU5A1PIYQNqhpOSV-fRPasw8rsUFJ5KBFZ4u3vAFU1m7uNfW9K68-zk4XGkvT2auLXz28B6ZEi0-uUoo_z3Fex-0FpY2H6I4SXelU3Y1_Tr1PrxO_phJ_pvp_OzXUwsyEj7SrdlhGpwUYb7P3x1m0/120fx120f" width="70">
            <p>Knives<br>15 000 ₽</p>
        </div>
        <div class="card" onclick="showCaseInfo('Gloves')">
            <img src="https://community.cloudflare.steamstatic.com/economy/image/-9a81dlWLwJ2UUGcVs_nsVtzdOEdtWwKGZZLQHTxDZ7I56KU0Zwwo4NUX4oFJZEHLbXU5A1PIYQNqhpOSV-fRPasw8rsUFJ5KBFZ4u3vAFU1m7uNfW9K68-zk4XGkvT2auLXz28B6ZEi0-uUoo_z3Fex-0FpY2H6I4SXelU3Y1_Tr1PrxO_phJ_pvp_OzXUwsyEj7SrdlhGpwUYb7P3x1m0/120fx120f" width="70" style="filter: hue-rotate(90deg);">
            <p>Gloves<br>10 000 ₽</p>
        </div>
    </div>
</div>

<div id="page-upgrade" class="page">
    <h3 style="text-align:center">NFTBattle Upgrade</h3>
    <div class="upgrade-container">
        <div id="upg-source-slot" class="upg-slot" onclick="openUpgSelector('source')">
            <span>ВЫБРАТЬ СВОЙ СКИН</span>
        </div>
        <div class="upg-chance-box">
            <div class="upg-circle" id="upg-chance-text">0%</div>
            <span style="font-size:0.6rem; color:#888;">ШАНС</span>
        </div>
        <div id="upg-target-slot" class="upg-slot" onclick="openUpgSelector('target')">
            <span>ВЫБРАТЬ ЦЕЛЬ</span>
        </div>
    </div>
    <div id="upg-result-msg" style="text-align:center; margin-top:15px; font-weight:bold; height:20px;"></div>
    <button class="btn" style="width:100%; margin-top:20px; height:50px; background:var(--upgrade-grad); color:white; border:2px solid #00ff44;" onclick="runUpgrade()">УЛУЧШИТЬ</button>
</div>

<div id="page-inventory" class="page">
    <div id="admin-tag">ADMIN</div>
    <div style="display:flex; justify-content:space-between; align-items:center;">
        <h3>Инвентарь</h3>
        <div style="display:flex; gap:5px;">
            <button class="btn" style="background:var(--blue); color:white; font-size:0.6rem;" onclick="openPromo()">ПРОМОКОД</button>
            <button class="btn" style="background:red; color:white; font-size:0.6rem;" onclick="sellAll()">ПРОДАТЬ ВСЁ</button>
        </div>
    </div>
    <div id="inventory-grid" class="inv-grid"></div>
</div>

<div class="bottom-nav">
    <div class="nav-item active" onclick="showPage('cases', this)">МАГАЗИН</div>
    <div class="nav-item" onclick="showPage('upgrade', this)">UPGRADE</div>
    <div class="nav-item" onclick="showPage('inventory', this)">ПРОФИЛЬ</div>
</div>

<script>
    const caseData = {
        'Revolution': { price: 500, skins: [
            { name: "AK-47 | Slate", price: 350, rarity: "Mil-Spec" },
            { name: "M4A4 | Spider Lily", price: 700, rarity: "Restricted" },
            { name: "USP-S | Traitor", price: 2500, rarity: "Classified" },
            { name: "AWP | Neo-Noir", price: 6000, rarity: "Covert" },
            { name: "M4A1-S | Printstream", price: 15000, rarity: "Covert" }
        ]},
        'Knives': { price: 15000, skins: [
            { name: "★ Karambit | Doppler", price: 120000, rarity: "Gold" },
            { name: "★ M9 Bayonet | Fade", price: 150000, rarity: "Gold" },
            { name: "★ Butterfly | Lore", price: 250000, rarity: "Gold" },
            { name: "AWP | Dragon Lore", price: 1500000, rarity: "Gold" }
        ]},
        'Gloves': { price: 10000, skins: [
            { name: "★ Sport | Pandora", price: 350000, rarity: "Gold" },
            { name: "★ Specialist | Crimson", price: 55000, rarity: "Gold" },
            { name: "★ Driver | King Snake", price: 90000, rarity: "Gold" }
        ]}
    };

    let user = null;
    let usersDB = JSON.parse(localStorage.getItem('cs_db_v29') || '{}');
    let currentAuthMode = 'login';

    function switchAuth(mode) {
        currentAuthMode = mode;
        document.getElementById('tab-login').classList.toggle('active', mode === 'login');
        document.getElementById('tab-reg').classList.toggle('active', mode === 'reg');
        document.getElementById('auth-btn').innerText = mode === 'login' ? 'Войти' : 'Создать аккаунт';
    }

    function handleAuth() {
        const n = document.getElementById('user-nick').value.trim();
        const p = document.getElementById('user-pass').value.trim();
        if(!n || !p) return alert("Заполните все поля!");

        if(currentAuthMode === 'login') {
            if(usersDB[n]) {
                if(usersDB[n].password === p) {
                    user = usersDB[n];
                    loginSuccess();
                } else alert("Неверный пароль!");
            } else alert("Пользователь не найден. Пожалуйста, зарегистрируйтесь.");
        } else {
            if(usersDB[n]) return alert("Этот ник уже занят!");
            usersDB[n] = { nick: n, password: p, balance: 2000, inventory: [], usedPromos: [] };
            user = usersDB[n];
            save();
            loginSuccess();
        }
    }

    function loginSuccess() {
        document.getElementById('auth-screen').style.display = 'none';
        document.getElementById('display-name').innerText = user.nick;
        if(user.nick === "T3DO" && user.password === "1235") {
            document.getElementById('admin-tag').style.display = 'block';
        }
        updateUI();
        initAdm();
    }

    function save() { if(user) { usersDB[user.nick] = user; localStorage.setItem('cs_db_v29', JSON.stringify(usersDB)); } }
    function updateUI() { document.getElementById('balance-val').innerText = user.balance.toLocaleString(); save(); }

    function showPage(id, el) {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        document.getElementById('page-' + id).classList.add('active');
        document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
        el.classList.add('active');
        if(id === 'inventory') renderInv();
    }

    function showCaseInfo(name) {
        const c = caseData[name];
        document.getElementById('m-title').innerText = name;
        document.getElementById('m-contents').innerHTML = c.skins.map(s => `<div class="skin-card ${s.rarity}">${s.name}<br>${s.price}₽</div>`).join('');
        document.getElementById('m-open-btn').innerText = `ОТКРЫТЬ (${c.price}₽)`;
        document.getElementById('m-open-btn').onclick = () => startOpen(name);
        document.getElementById('m-view').style.display = 'block';
        document.getElementById('m-anim').style.display = 'none';
        document.getElementById('case-modal').style.display = 'flex';
    }

    function startOpen(name) {
        const c = caseData[name];
        if(user.balance < c.price) return alert("Мало денег!");
        user.balance -= c.price; updateUI();
        
        document.getElementById('m-view').style.display = 'none';
        document.getElementById('m-anim').style.display = 'block';
        document.getElementById('m-claim-btn').style.display = 'none';
        document.getElementById('m-win-info').innerText = 'открытие...';

        const win = c.skins[Math.floor(Math.random() * c.skins.length)];
        const track = document.getElementById('spinner-track');
        const winIndex = 40; 
        const itemWidth = 110; 
        
        track.style.transition = "none"; 
        track.style.transform = "translateX(0)";
        
        let html = ""; 
        for(let i=0; i < 50; i++) { 
            let s = (i === winIndex) ? win : c.skins[Math.floor(Math.random() * c.skins.length)]; 
            html += `<div class="spinner-item ${s.rarity}">${s.name}</div>`; 
        }
        track.innerHTML = html;

        setTimeout(() => {
            const containerWidth = document.querySelector('.spinner-wrap').offsetWidth;
            const centerOffset = containerWidth / 2;
            const randomizePos = Math.floor(Math.random() * 80) - 40; 
            const finalPos = (winIndex * itemWidth) - centerOffset + (itemWidth / 2) + randomizePos;
            
            track.style.transition = "transform 5s cubic-bezier(0.1, 0, 0.1, 1)";
            track.style.transform = `translateX(-${finalPos}px)`;
        }, 50);

        setTimeout(() => { 
            user.inventory.push({...win, id: Date.now()}); 
            document.getElementById('m-win-info').innerText = win.name; 
            document.getElementById('m-claim-btn').style.display = 'block'; 
            updateUI(); 
        }, 5200);
    }

    function renderInv() {
        document.getElementById('inventory-grid').innerHTML = user.inventory.map(s => `
            <div class="skin-card ${s.rarity}">
                <b>${s.name}</b><br>${s.price}₽
                <button class="btn" style="background:red; color:white; font-size:0.5rem; margin-top:5px;" onclick="sell('${s.id}')">ПРОДАТЬ</button>
            </div>
        `).join('');
    }

    function sell(id) { const idx = user.inventory.findIndex(x => x.id == id); if(idx > -1) { user.balance += user.inventory[idx].price; user.inventory.splice(idx, 1); updateUI(); renderInv(); } }
    function sellAll() { user.inventory.forEach(s => user.balance += s.price); user.inventory = []; updateUI(); renderInv(); }

    function openPromo() {
        const code = prompt("Введите промокод:");
        if(code === "TEDO1" && !user.usedPromos.includes("TEDO1")) { user.balance += 1000; user.usedPromos.push("TEDO1"); updateUI(); alert("+1000 ₽"); }
    }

    let upgSrc = null, upgTrg = null;
    function openUpgSelector(type) {
        const grid = document.getElementById('sel-grid');
        document.getElementById('selection-modal').style.display = 'flex';
        if(type === 'source') {
            grid.innerHTML = user.inventory.map(s => `<div class="skin-card ${s.rarity}" onclick="selectUpg('source', '${s.id}')"><b>${s.name}</b><br>${s.price}₽</div>`).join('');
        } else {
            let all = []; for(let k in caseData) all = all.concat(caseData[k].skins);
            grid.innerHTML = all.filter(s => upgSrc && s.price > upgSrc.price).map(s => `<div class="skin-card ${s.rari
