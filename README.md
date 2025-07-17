<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Фермерская игра — MVP</title>
<style>
  body {
    margin: 0; padding: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #a0d8a0, #5a9e55);
    color: #2f4f2f;
    display: flex;
    flex-direction: column;
    height: 100vh;
  }
  header {
    padding: 10px 20px;
    font-weight: 700;
    font-size: 1.2rem;
    background: #497a3e;
    color: white;
    text-align: center;
    position: relative;
  }
  header > div {
    position: absolute;
    top: 10px;
  }
  #ecoCoinsDisplay {
    left: 20px;
  }
  #seedCountDisplay {
    right: 20px;
  }
  main {
    flex-grow: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
  }
  #plant {
    width: 100px;
    height: 100px;
    background: url('https://cdn-icons-png.flaticon.com/512/427/427735.png') no-repeat center center;
    background-size: contain;
    cursor: pointer;
    filter: drop-shadow(0 2px 2px rgba(0,0,0,0.3));
    transition: transform 0.1s ease;
  }
  #plant:active {
    transform: scale(0.9);
  }
  nav {
    display: flex;
    justify-content: space-around;
    background: #497a3e;
    padding: 10px 0;
  }
  nav button {
    background: transparent;
    border: none;
    color: white;
    font-size: 1rem;
    padding: 8px 20px;
    cursor: pointer;
    font-weight: 700;
    transition: background-color 0.3s ease;
    border-radius: 6px;
  }
  nav button:hover {
    background: #6baa52;
  }
  nav button.active {
    background: #8bc34a;
    box-shadow: 0 0 8px #aeea00;
  }
  section.tab {
    display: none;
    padding: 20px;
    text-align: center;
  }
  section.tab.active {
    display: block;
  }
  /* Прокачка кнопки */
  .upgrade-btn {
    background: #7bbf6a;
    border: none;
    color: white;
    font-weight: 700;
    padding: 10px 15px;
    margin: 10px;
    cursor: pointer;
    border-radius: 6px;
    transition: background-color 0.3s ease;
  }
  .upgrade-btn:hover:not(:disabled) {
    background: #6baa52;
  }
  .upgrade-btn:disabled {
    background: #a0a0a0;
    cursor: not-allowed;
  }
  /* Коллекция растений */
  #collectionList {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
  }
  .plant-item {
    background: #7bbf6a;
    border-radius: 8px;
    padding: 10px;
    width: 120px;
    color: white;
    font-weight: 700;
  }
</style>
</head>
<body>

<header>
  Фермерская Игра MVP
  <div id="ecoCoinsDisplay">Eco Coin: <span id="ecoCoins">0</span></div>
  <div id="seedCountDisplay">Семена: <span id="seedCountHeader">0</span></div>
</header>

<main>
  <div id="plant" title="Кликни, чтобы собрать семена"></div>
</main>

<nav>
  <button id="btnMain" class="active">Главный экран</button>
  <button id="btnUpgrade">Прокачка</button>
  <button id="btnCollection">Коллекция</button>
</nav>

<section id="mainScreen" class="tab active">
  <p>Кликайте на росток, чтобы собирать семена!</p>
</section>

<section id="upgradeScreen" class="tab">
  <h2>Прокачка</h2>
  <div>
    <button id="upgradeWorker" class="upgrade-btn">Купить Рабочего (<span id="workerCost">10</span> Eco)</button>
    <p>Рабочих: <span id="workerCount">0</span></p>
  </div>
  <div>
    <button id="upgradeLand" class="upgrade-btn">Купить Землю (<span id="landCost">50</span> Eco)</button>
    <p>Лимит энергии: <span id="energyLimit">100</span></p>
  </div>
  <div>
    <button id="upgradeTech" class="upgrade-btn">Купить Технику (<span id="techCost">100</span> Eco)</button>
    <p>Ускорение роста: <span id="techLevel">1</span>x</p>
  </div>
</section>

<section id="collectionScreen" class="tab">
  <h2>Коллекция растений</h2>
  <div id="collectionList">
    <p>Пока что нет растений</p>
  </div>
</section>

<script>
  const plant = document.getElementById('plant');
  const seedCounter = document.getElementById('seedCountHeader');
  const ecoCoinsDisplay = document.getElementById('ecoCoins');
  
  // Прокачка элементы
  const upgradeWorkerBtn = document.getElementById('upgradeWorker');
  const upgradeLandBtn = document.getElementById('upgradeLand');
  const upgradeTechBtn = document.getElementById('upgradeTech');

  const workerCountDisplay = document.getElementById('workerCount');
  const energyLimitDisplay = document.getElementById('energyLimit');
  const techLevelDisplay = document.getElementById('techLevel');

  const workerCostDisplay = document.getElementById('workerCost');
  const landCostDisplay = document.getElementById('landCost');
  const techCostDisplay = document.getElementById('techCost');

  // Коллекция
  const collectionList = document.getElementById('collectionList');

  // Переменные состояния
  let seeds = 0;
  let ecoCoins = 0;

  let workerCount = 0;
  let landCount = 0;
  let techLevel = 1;

  let energyLimit = 100;

  // Цены
  let workerCost = 10;
  let landCost = 50;
  let techCost = 100;

  // Коллекция растений (для MVP пусть будет простой массив с названиями)
  let collection = [];

  // Функция обновления UI
  function updateUI() {
    seedCounter.textContent = seeds;
    ecoCoinsDisplay.textContent = ecoCoins;

    workerCountDisplay.textContent = workerCount;
    energyLimitDisplay.textContent = energyLimit;
    techLevelDisplay.textContent = techLevel;

    workerCostDisplay.textContent = workerCost;
    landCostDisplay.textContent = landCost;
    techCostDisplay.textContent = techCost;

    // Проверяем, можем ли купить улучшения (для кнопок)
    upgradeWorkerBtn.disabled = ecoCoins < workerCost;
    upgradeLandBtn.disabled = ecoCoins < landCost;
    upgradeTechBtn.disabled = ecoCoins < techCost;

    // Обновление коллекции
    renderCollection();
  }

  // Рендер коллекции растений
  function renderCollection() {
    collectionList.innerHTML = '';
    if (collection.length === 0) {
      collectionList.innerHTML = '<p>Пока что нет растений</p>';
      return;
    }
    collection.forEach((plantName) => {
      const item = document.createElement('div');
      item.className = 'plant-item';
      item.textContent = plantName;
      collectionList.appendChild(item);
    });
  }

  // Логика клика на росток — получаем семена
  plant.addEventListener('click', () => {
    seeds++;
    ecoCoins++; // Для примера, сразу даём и ecoCoins за клик (можно переделать)
    updateUI();
    saveProgress();
  });

  // Логика прокачки: покупка рабочих
  upgradeWorkerBtn.addEventListener('click', () => {
    if (ecoCoins >= workerCost) {
      ecoCoins -= workerCost;
      workerCount++;
      workerCost = Math.floor(workerCost * 1.5);
      updateUI();
      saveProgress();
    }
  });

  // Покупка земли
  upgradeLandBtn.addEventListener('click', () => {
    if (ecoCoins >= landCost) {
      ecoCoins -= landCost;
      landCount++;
      energyLimit += 50;
      landCost = Math.floor(landCost * 2);
      updateUI();
      saveProgress();
    }
  });

  // Покупка техники
  upgradeTechBtn.addEventListener('click', ()
