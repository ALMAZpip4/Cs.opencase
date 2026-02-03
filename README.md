<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CS2 Simulator V37</title>
  <style>
    :root {
      --bg: #0b0b0b;
      --card: #151515;
      --accent: #eb4b4b; 
      --blue: #4b69ff;
      --purple: #8847ff;
      --pink: #d32ce6;
      --red: #eb4b4b;
      --gold: #caab05;
      --text: #e0e0e0;
      --upgrade-grad: linear-gradient(180deg, #ff4b4b 0%, #880000 100%); 
    }

    body { background-color: var(--bg); color: var(--text); font-family: 'Segoe UI', sans-serif; margin: 0; padding-bottom: 90px; overflow-x: hidden; }
    #auth-screen { position: fixed; inset: 0; background: var(--bg); z-index: 9999; display: flex; justify-content: center; align-items: center; }
    .auth-box { background: #111; padding: 30px; border-radius: 15px; border: 1px solid #333; text-align: center; width: 300px; }
    .auth-tabs { display: flex; gap: 10px; margin-bottom: 20px; }
    .auth-tab { flex: 1; padding: 8px; background: #222; cursor: pointer; border-radius: 5px; font-size: 0.8rem; border: 1px solid transparent; }
    .auth-tab.active { background: var(--accent); color: #fff; font-weight: bold; }
    input, select { background: #222; border: 1px solid #444; color: white; padding: 10px; border-radius: 5px; width: 100%; margin-bottom: 10px; box-sizing: border-box; }
    .btn { background: var(--accent); color: #fff; border: none; padding: 10px 15px; font-weight: bold; cursor: pointer; border-radius: 5px; text-transform: uppercase; font-size: 0.75rem; transition: 0.2s; }
    .btn:active { transform: scale(0.95); }
    .header { padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; background: var(--card); border-bottom: 1px solid #333; }
    #display-name { cursor: pointer; text-decoration: underline; color: #fff; font-weight: bold; }
    .balance { color: var(--accent); font-weight: bold; font-size: 1.2rem; }
    .page { display: none; padding: 20px; animation: fadeIn 0.2s; }
    .page.active { display: block; }
    @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    .case-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 15px; }
    .inv-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(110px, 1fr)); gap: 10px; }
    .card { background: var(--card); border: 1px solid #222; padding: 15px; text-align: center; border-radius: 10px; cursor: pointer; }
    .skin-card { background: #1a1a1a; padding: 8px; border-radius: 5px; font-size: 0.65rem; border-bottom: 4px solid #ccc; text-align: center; cursor: pointer; position: relative; }
    .Mil-Spec { border-color: var(--blue) !important; }
    .Restricted { border-color: var(--purple) !important; }
    .Classified { border-color: var(--pink) !important; }
    .Covert { border-color: var(--red) !important; }
    .Gold { border-color: var(--gold) !important; }
    .spinner-wrap { position: relative; width: 100%; height: 130px; background: #050505; border: 2px solid #222; margin: 20px 0; overflow: hidden; border-radius: 10px; }
    .spinner-line { position: absolute; left: 50%; top: 0; bottom: 0; width: 4px; background: var(--accent); z-index: 100; transform: translateX(-50%); box-shadow: 0 0 15px var(--accent); }
    .spinner-track { display: flex; position: absolute; left: 0; top: 10px; transition: transform 5s cubic-bezier(0.1, 0, 0.1, 1); will-change: transform; align-items: center; }
    .spinner-item { min-width: 100px; width: 100px; height: 100px; margin: 0 5px; background: #111; border-bottom: 4px solid #fff; display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 0.55rem; text-align: center; padding: 5px; box-sizing: border-box; flex-shrink: 0; }
    .click-area { display: flex; flex-direction: column; align-items: center; justify-content: center; height: auto; margin-top: 20px; }
    .big-button { width: 180px; height: 180px; background: var(--upgrade-grad); border-radius: 50%; border: 5px solid white; color: white; font-size: 1.5rem; font-weight: bold; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.1s; user-select: none; box-shadow: 0 0 20px rgba(235, 75, 75, 0.5); margin-bottom: 20px; }
    .big-button:active { transform: scale(0.9); }
    .bottom-nav { position: fixed; bottom: 0; width: 100%; height: 80px; background: #000; display: flex; justify-content: space-around; align-items: center; border-top: 2px solid #222; z-index: 1000; padding: 0 5px; box-sizing: border-box; }
    .nav-item { flex: 1; margin: 0 2px; height: 50px; background: var(--upgrade-grad); color: white !important; font-weight: bold; border-radius: 10px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 0.55rem; text-transform: uppercase; text-align: center; border: 1px solid var(--red); }
    .nav-item.active { filter: brightness(1.4); border-color: #fff; }
    .nav-item.red-btn { border: 2px solid var(--red); }
    .modal { position: fixed; inset: 0; background: rgba(0,0,0,0.95); z-index: 2000; display: none; justify-content: center; align-items: center; }
    .modal-box { background: #111; border: 1px solid #333; padding: 20px; border-radius: 12px; width: 95%; max-width: 450px; text-align: center; max-height: 85vh; overflow-y: auto; }
    #admin-label { display: none; color: var(--red); font-size: 2.5rem; font-weight: 900; margin-bottom: 10px; text-align: center; width: 100%; }
  </style>
</head>
<body>

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
  <div id="display-name" onclick="openAdmin()">Загрузка...</div>
  <div class="balance"><span id="balance-val">0</span> ₽</div>
</div>

<div id="admin-modal" class="modal">
  <div class="modal-box">
    <h3 style="color:var(--red)">ADMIN PANEL</h3>
    <select id="adm-player-select"></select>
    <div style="display:flex; gap:5px; margin-bottom:10px;">
      <input type="number" id="adm-amount" placeholder="Сумма" style="margin:0;">
      <button class="btn" onclick="adminModifyBalance('add')">+</button>
      <button class="btn" onclick="adminModifyBalance('set')">=</button>
    </div>
    <p>Выдать скин:</p>
    <select id="adm-item-select"></select>
    <button class="btn" style="width:100%; background:#fff; color:#000; margin-bottom:10px;" onclick="adminGiveItem()">ВЫДАТЬ</button>
    <button class="btn" style="width:100%; background:#222; color:#fff" onclick="closeModal('admin-modal')">ЗАКРЫТЬ</button>
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
      <div class="spinner-wrap" id="spinner-container">
        <div class="spinner-line"></div>
        <div class="spinner-track" id="spinner-track"></div>
      </div>
      <div id="m-win-info" style="margin-top:10px; font-weight:bold; min-height:40px; color:var(--gold);"></div>
      <button class="btn" id="m-claim-btn" style="width:100%; display:none;" onclick="closeModal('case-modal')">ЗАБРАТЬ</button>
    </div>
  </div>
</div>

<div id="promo-modal" class="modal">
  <div class="modal-box">
    <h3>Введите промокод</h3>
    <input type="text" id="promo-input" placeholder="Код...">
    <button class="btn" style="width:100%" onclick="applyPromo()">АКТИВИРОВАТЬ</button>
    <button class="btn" style="width:100%; background:#222; margin-top:5px;" onclick="closeModal('promo-modal')">ОТМЕНА</button>
  </div>
</div>

<div id="page-cases" class="page active">
  <div class="case-grid">
    <div class="card" onclick="showCaseInfo('My')">тренеровка<br>300 ₽</div>
    <div class="card" onclick="showCaseInfo('Snakebite')">Snakebite Case<br>100 ₽</div>
    <div class="card" onclick="showCaseInfo('Recoil')">Recoil Case<br>250 ₽</div>
    <div class="card" onclick="showCaseInfo('Revolution')">Revolution<br>500 ₽</div>
    <div class="card" onclick="showCaseInfo('Knives')">Knives<br>15 000 ₽</div>
  </div>
</div>

<div id="page-clicker" class="page">
  <div class="click-area">
    <div class="big-button" onclick="clickMoney()">КЛИК</div>
    <div style="background:var(--card); padding:20px; border-radius:10px; border:1px solid #333; width:200px; text-align:center;">
      <div style="font-size:0.8rem; margin-bottom:5px;">Уровень кликера: <span id="click-lvl">1</span></div>
      <div style="font-size:0.8rem; margin-bottom:10px;">Доход: +<span id="click-pwr">0.01</span> ₽</div>
      <button class="btn" style="width:100%; background:var(--gold); color:#000;" onclick="upgradeClicker()">УЛУЧШИТЬ (<span id="click-cost">50</span> ₽)</button>
    </div>
  </div>
</div>

<div id="page-upgrade" class="page">
  <h3>Upgrade</h3>
  <div style="display:flex; justify-content:space-around; align-items:center; margin-bottom:20px;">
    <button class="btn" style="flex:1; margin-right:5px; background:#333;" onclick="openUpgList('my')">ВЫБРАТЬ МОЙ</button>
    <div id="upg-chance" style="font-weight:bold; color:var(--gold); font-size:1.5rem;">0%</div>
    <button class="btn" style="flex:1; margin-left:5px; background:#333;" onclick="openUpgList('target')">ВЫБРАТЬ ЦЕЛЬ</button>
  </div>
  <div style="display:flex; justify-content:center; gap:20px; margin-bottom:20px;">
    <div id="upg-my-disp" class="skin-card" style="width:130px; height:70px; display:flex; align-items:center; justify-content:center;">Мой</div>
    <div id="upg-target-disp" class="skin-card" style="width:130px; height:70px; display:flex; align-items:center; justify-content:center;">Цель</div>
  </div>
  <button class="btn" style="width:100%; height:55px; background:var(--gold); color:black;" onclick="runUpgrade()">УЛУЧШИТЬ</button>
  <div id="upg-selection-list" class="inv-grid" style="margin-top:20px; max-height:250px; overflow-y:auto; display:none; background:#111; padding:10px;"></div>
</div>

<div id="page-contract" class="page">
  <h3>Контракт</h3>
  <div id="contract-selection" class="inv-grid" style="max-height:300px; overflow-y:auto;"></div>
  <button class="btn" style="width:100%; margin-top:10px;" onclick="runContract()">ОБМЕНЯТЬ</button>
</div>

<div id="page-inventory" class="page">
  <div id="admin-label">ADMIN</div>
  <div style="display:flex; justify-content:space-between; margin-bottom:15px;">
    <button class="btn" style="background:var(--red);" onclick="openPromoModal()">ПРОМОКОД</button>
    <button class="btn" style="background:red;" onclick="sellAll()">ПРОДАТЬ ВСЁ</button>
  </div>
  <div id="inventory-grid" class="inv-grid"></div>
</div>

<div class="bottom-nav">
  <div class="nav-item active" onclick="showPage('cases', this)">МАГАЗИН</div>
  <div class="nav-item" onclick="showPage('clicker', this)">КЛИКЕР</div>
  <div class="nav-item red-btn" onclick="showPage('upgrade', this)">UPGRADE</div>
  <div class="nav-item" onclick="showPage('contract', this)">КОНТРАКТ</div>
  <div class="nav-item" onclick="showPage('inventory', this)">ПРОФИЛЬ</div>
</div>

<script>
  const caseData = {
    'Snakebite': { price: 100, skins: [{ name: "SG 553 | Heavy Metal", price: 85, rarity: "Mil-Spec", chance: 90 }, { name: "AWP | Red Kilind", price: 120, rarity: "Restricted", chance: 6 },  { name: "M4A4 | Riger", price: 230, rarity: "Classified", chance: 3 }, { name: "USP-S | The Traitor", price: 400, rarity: "Covert", chance: 1 }] },
    'Recoil': { price: 250, skins: [{ name: "AK-47 | Ice Coaled", price: 150, rarity: "Mil-Spec", chance: 99.9 }, { name: "AWP | Chromatic Aberration", price: 2200, rarity: "Covert", chance: 0.1 }] },
    'Revolution': { price: 500, skins: [{ name: "AK-47 | Slate", price: 350, rarity: "Classified", chance: 98.9 }, { name: "M4A4 | Temukau", price: 900, rarity: "Covert", chance: 1 }, { name: "M4A1-S | Printstream", price: 1500, rarity: "Covert", chance: 0.1 }] },
    'Knives': { price: 15000, skins: [{ name: "★ Karambit | Doppler", price: 12000, rarity: "Gold", chance: 99.9 }, { name: "★ Butterfly | Lore", price: 25000, rarity: "Gold", chance: 0.1 }] },
    'My': { price: 300, skins: [{ name: "Sn1", price: 100, rarity: "Mil-Spec", chance: 80 },{ name: "Sk2", price: 500, rarity: "Covert", chance: 20 }] }
  };
  let allSkinsPool = [];
  for(let c in caseData) allSkinsPool = allSkinsPool.concat(caseData[c].skins);

  let user = null;
  let usersDB = {};
  let contractSelected = [];
  let upgMy = null, upgTarget = null;

  function switchAuth(mode) {
    document.querySelectorAll('.auth-tab').forEach(t => t.classList.remove('active'));
    document.getElementById('tab-' + mode).classList.add('active');
    document.getElementById('auth-btn').onclick = () => handleAuth(mode);
  }
  switchAuth('login');

  function handleAuth(mode) {
    const n = document.getElementById('user-nick').value.trim();
    const p = document.getElementById('user-pass').value.trim();
    if(!n || !p) return;
    if(mode === 'login') {
      if(usersDB[n] && usersDB[n].password === p) { user = usersDB[n]; loginSuccess(); }
      else alert("Ошибка входа");
    } else {
      if(usersDB[n]) return alert("Ник занят");
      usersDB[n] = { nick: n, password: p, balance: 0, inventory: [], usedPromos: [], clickPower: 0.01, clickLvl: 1, clickCost: 50 };
      user = usersDB[n]; save(); loginSuccess();
    }
  }

  function loginSuccess() {
    document.getElementById('auth-screen').style.display = 'none';
    document.getElementById('display-name').innerText = user.nick;
    
    if(user.clickPower === undefined) user.clickPower = 0.01;
    if(user.clickLvl === undefined) user.clickLvl = 1;
    if(user.clickCost === undefined) user.clickCost = 50;

    if(user.nick === 'T3DO' && user.password === '1235') document.getElementById('admin-label').style.display='block';
    updateUI();
  }

  function save() { if(user) { usersDB[user.nick] = user; localStorage.setItem('cs_db_v37', JSON.stringify(usersDB)); } }
  
  function updateUI() { 
    document.getElementById('balance-val').innerText = user.balance.toFixed(2); 
    document.getElementById('click-lvl').innerText = user.clickLvl;
    document.getElementById('click-pwr').innerText = user.clickPower.toFixed(2);
    document.getElementById('click-cost').innerText = user.clickCost;
    save(); 
  }

  function showPage(id, el) {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById('page-' + id).classList.add('active');
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    el.classList.add('active');
    if(id === 'inventory') renderInv();
    if(id === 'contract') renderContractSelection();
  }

  function clickMoney() { user.balance += user.clickPower; updateUI(); }

  function upgradeClicker() {
    if(user.balance >= user.clickCost) {
      user.balance -= user.clickCost;
      user.clickLvl++;
      user.clickPower += 0.01;
      user.clickCost = Math.floor(user.clickCost * 1.5);
      updateUI();
    } else alert("Мало денег!");
  }

  function showCaseInfo(name) {
    const c = caseData[name];
    document.getElementById('m-title').innerText = name;
    document.getElementById('m-contents').innerHTML = c.skins.map(s => `<div class="skin-card ${s.rarity}">${s.name}<br><b style="color:white; font-size:10px;">Шанс: ${s.chance}%</b></div>`).join('');
    document.getElementById('m-open-btn').onclick = () => startOpen(name);
    document.getElementById('m-view').style.display = 'block';
    document.getElementById('m-anim').style.display = 'none';
    document.getElementById('case-modal').style.display = 'flex';
  }

  function startOpen(name) {
    const c = caseData[name];
    if(user.balance < c.price) return alert("Недостаточно средств");
    user.balance -= c.price; updateUI();
    document.getElementById('m-view').style.display = 'none';
    document.getElementById('m-anim').style.display = 'block';
    document.getElementById('m-claim-btn').style.display = 'none';
    document.getElementById('m-win-info').innerText = "";
    
    // Выбор предмета по весам
    const rand = Math.random() * 100;
    let cumulative = 0;
    let winItem = c.skins[0];
    for (let s of c.skins) {
      cumulative += s.chance;
      if (rand <= cumulative) {
        winItem = s;
        break;
      }
    }

    const track = document.getElementById('spinner-track');
    const container = document.getElementById('spinner-container');
    
    let items = [];
    for(let i=0; i<60; i++) {
      const r = Math.random() * 100;
      let cum = 0;
      let itm = c.skins[0];
      for(let s of c.skins) { cum += s.chance; if(r <= cum) { itm = s; break; } }
      items.push(itm);
    }
    
    const winIndex = 50;
    items[winIndex] = winItem;
    track.style.transition = 'none';
    track.style.transform = 'translateX(0px)';
    track.innerHTML = items.map(s => `<div class="spinner-item ${s.rarity}">${s.name}</div>`).join('');
    const itemFullWidth = 110;
    const containerWidth = container.offsetWidth;
    const finalOffset = (winIndex * itemFullWidth) - (containerWidth / 2) + (itemFullWidth / 2);
    
    setTimeout(() => {
      track.style.transition = 'transform 5s cubic-bezier(0.1, 0, 0.1, 1)';
      track.style.transform = `translateX(-${finalOffset}px)`; 
    }, 50);
    
    setTimeout(() => {
      user.inventory.push({...winItem, id: Date.now() + Math.random()});
      document.getElementById('m-win-info').innerText = "ПОЛУЧЕНО: " + winItem.name;
      document.getElementById('m-claim-btn').style.display = 'block';
      updateUI();
    }, 5300);
  }

  function openUpgList(type) {
    const list = document.getElementById('upg-selection-list');
    list.style.display = 'grid';
    if(type === 'my') list.innerHTML = user.inventory.map(s => `<div class="skin-card ${s.rarity}" onclick="setUpg('my','${s.id}')">${s.name} (${s.price}₽)</div>`).join('');
    else list.innerHTML = allSkinsPool.map((s, idx) => `<div class="skin-card ${s.rarity}" onclick="setUpg('target',${idx})">${s.name} (${s.price}₽)</div>`).join('');
  }

  function setUpg(type, val) {
    if(type === 'my') upgMy = user.inventory.find(i => i.id == val);
    else upgTarget = allSkinsPool[val];
    document.getElementById('upg-my-disp').innerText = upgMy ? upgMy.name : "Мой";
    document.getElementById('upg-target-disp').innerText = upgTarget ? upgTarget.name : "Цель";
    document.getElementById('upg-selection-list').style.display = 'none';
    if(upgMy && upgTarget) {
      let chance = (upgMy.price / upgTarget.price) * 100;
      document.getElementById('upg-chance').innerText = Math.min(chance, 95).toFixed(1) + "%";
    }
  }

  function runUpgrade() {
    if(!upgMy || !upgTarget) return alert("Выберите предметы");
    let chance = (upgMy.price / upgTarget.price) * 100;
    user.inventory = user.inventory.filter(i => i.id != upgMy.id);
    if(Math.random() * 100 <= chance) { user.inventory.push({...upgTarget, id: Date.now()}); alert("УСПЕХ!"); }
    else alert("НЕУДАЧА");
    upgMy = null; setUpg('my',null); updateUI();
  }

  function openAdmin() {
    const pSel = document.getElementById('adm-player-select'
