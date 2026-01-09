# consersum-

<!DOCTYPE html>
<html lang="uk">
<head>
<meta charset="UTF-8">
<title>ConsensusSim</title>

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
/* ==== DESIGN ==== */
:root{
  --bg:#0b0f1a;
  --card:rgba(255,255,255,.08);
  --glass:rgba(255,255,255,.12);
  --accent:#4ec9b0;
  --accent2:#9cdcfe;
  --warn:#ffd866;
  --danger:#f44747;
}
*{box-sizing:border-box}
body{
  margin:0;
  font-family:Inter,system-ui;
  background:
    radial-gradient(circle at 20% 20%, #1b2a3a, transparent 40%),
    radial-gradient(circle at 80% 80%, #0e3a2f, transparent 40%),
    var(--bg);
  color:#e6e6e6;
}
header{padding:22px 30px}
header h1{margin:0;font-weight:700}

.dashboard{
  display:grid;
  grid-template-columns:360px 1fr;
  gap:24px;
  padding:24px;
}
.panel,.chart-card,.log{
  background:var(--card);
  backdrop-filter:blur(18px);
  border-radius:18px;
  padding:18px;
}
.panel h3{
  margin:14px 0 8px;
  font-size:13px;
  text-transform:uppercase;
  color:var(--accent2);
}

input,button,select{
  width:100%;
  padding:10px;
  border-radius:10px;
  border:none;
  font-family:inherit;
}
input,select{background:#111827;color:#fff}
button{margin-top:6px;font-weight:600;cursor:pointer}

.btn-control{background:linear-gradient(135deg,var(--accent),var(--accent2));color:#000}
.btn-majority{background:#4fc3f7}
.btn-compromise{background:#ce93d8}
.btn-random{background:#ffb74d}
.btn-compare{background:#81c784}

.option{
  background:#111827;
  border-radius:12px;
  padding:10px;
  margin-bottom:8px;
  display:flex;
  justify-content:space-between;
  align-items:center;
}
.option input{width:60px}

.main{
  display:grid;
  grid-template-rows:auto auto 1fr auto auto;
  gap:20px;
}
.status{
  display:grid;
  grid-template-columns:repeat(6,1fr);
  gap:14px;
}
.status-card{
  background:var(--glass);
  border-radius:16px;
  padding:12px;
  text-align:center;
}
.status-card b{display:block;font-size:16px}

.lamp{width:20px;height:20px;border-radius:50%;margin:6px auto 0}
.red{background:#f44747}
.green{background:#4ec9b0;animation:pulse 1s infinite alternate}
.yellow{background:#ffd866}
@keyframes pulse{from{opacity:.5}to{opacity:1}}

.result{
  background:var(--glass);
  border-radius:18px;
  padding:18px;
  text-align:center;
  font-size:18px;
}

.charts{display:grid;grid-template-columns:1fr 1fr;gap:20px}
canvas{background:#0b0f1a;border-radius:12px}

.log h3{margin:0 0 8px;font-size:13px;color:var(--accent2)}
#eventLog,#historyLog{
  max-height:180px;
  overflow-y:auto;
  font-size:12px;
  text-align:left;
}

.leader{outline:2px solid gold}
.leader-change{animation:flash 1s}
@keyframes flash{
  0%{box-shadow:0 0 0 gold}
  50%{box-shadow:0 0 20px gold}
  100%{box-shadow:0 0 0 gold}
}
</style>
</head>

<body>

<header><h1>ConsensusSim</h1></header>

<div class="dashboard">

<div class="panel">
  <h3>Альтернативи</h3>
  <input id="newOpt" placeholder="Назва альтернативи">
  <button class="btn-control" onclick="addOption()">Додати</button>
  <div id="options"></div>

  <h3>Варіант рішення</h3>
  <button class="btn-majority" onclick="setMethod('majority')">⚖️ Мажоритарний</button>
  <button class="btn-compromise" onclick="setMethod('compromise')">📐 Компромісний</button>
  <button class="btn-random" onclick="setMethod('random')">🎲 Стохастичний</button>
  <button class="btn-compare" onclick="setMethod('compare')">🔬 Порівняти</button>
  <div id="methodLabel" style="margin-top:8px;color:#ffd866">Поточний алгоритм: Мажоритарний</div>

  <h3>Швидкість</h3>
  <select id="speedSelect">
    <option value="demo">🐢 Demo</option>
    <option value="normal" selected>⚙️ Normal</option>
    <option value="research">🔬 Research</option>
  </select>

  <h3>Симуляція</h3>
  <button id="startBtn" class="btn-control" onclick="toggleSimulation()">▶️ START</button>

  <h3>Результати</h3>
  <button class="btn-control" onclick="showHistory()">📂 Історія</button>
  <button class="btn-control" onclick="exportResults()">💾 Експорт JSON</button>
</div>

<div class="main">

<div class="status">
  <div class="status-card"><span>Стан</span><div id="lamp" class="lamp red"></div></div>
  <div class="status-card"><span>Ітерації</span><b id="iter">0</b></div>
  <div class="status-card"><span>Час</span><b id="time">0.0 c</b></div>
  <div class="status-card"><span>Лідер</span><b id="leader">—</b></div>
  <div class="status-card"><span>Довіра</span><b id="conf">0%</b></div>
</div>

<div class="result" id="result">Система готова</div>

<div class="charts">
  <div class="chart-card"><canvas id="bar"></canvas></div>
  <div class="chart-card"><canvas id="pie"></canvas></div>
</div>
<div class="chart-card"><canvas id="line"></canvas></div>

<div class="log">
  <h3>Журнал симуляції</h3>
  <div id="eventLog"></div>
  <h3 style="margin-top:12px">Збережені експерименти</h3>
  <div id="historyLog"></div>
</div>

</div>
</div>

<script>
let options=[],votes=[],timer=null,timeTimer=null;
let iteration=0,lastWinners=[],STAB=6,step=0;
let bar,pie,line,startTime=0,lastLeader=null;
let currentMethod="majority",INTERVAL=1200;

/* ---------- CORE ---------- */
function addOption(){
 let v=newOpt.value.trim();
 if(!v||options.includes(v))return;
 options.push(v); renderOptions(); initCharts(); newOpt.value="";
}
function renderOptions(){
 optionsDiv.innerHTML="";
 options.forEach(o=>{
  let d=document.createElement("div");
  d.className="option";
  d.innerHTML=`<span>${o}</span>
  <input type="number" min="1" max="5" value="1" id="w-${o}">`;
  optionsDiv.appendChild(d);
 });
}
const optionsDiv=document.getElementById("options");

function setMethod(m){
 currentMethod=m;
 const names={majority:"Мажоритарний",compromise:"Компромісний",random:"Стохастичний",compare:"Порівняння"};
 methodLabel.textContent="Поточний алгоритм: "+names[m];
}

function applySpeed(){
 let s=speedSelect.value;
 if(s==="demo"){INTERVAL=2000;STAB=4}
 if(s==="normal"){INTERVAL=1200;STAB=6}
 if(s==="research"){INTERVAL=3000;STAB=10}
}

function toggleSimulation(){
 if(timer){stop();return;}
 applySpeed();
 votes=[];iteration=0;lastWinners=[];lastLeader=null;step=0;
 startTime=Date.now();
 lamp.className="lamp green";
 startBtn.textContent="⏹ STOP";
 timeTimer=setInterval(()=>time.textContent=((Date.now()-startTime)/1000).toFixed(1)+" c",100);
 timer=setInterval(stepSim,INTERVAL);
}

function stop(){
 clearInterval(timer); clearInterval(timeTimer);
 timer=null; lamp.className="lamp red";
 startBtn.textContent="▶️ START";
}

function stepSim(){
 let o=options[Math.floor(Math.random()*options.length)];
 addVote(o); iteration++; iter.textContent=iteration;
 let w=resolveDecision();
 leader.textContent=w||"—";
 conf.textContent=confidence()+"%";

 if(w && w!==lastLeader){
  lastLeader=w;
  log(`Новий лідер → ${w}`);
  animateLeader(w);
 }

 if(checkEq()){
  stop(); lamp.className="lamp yellow";
  saveResult(w);
  result.innerHTML=`✅ Рівновага<br><b>${w}</b>`;
 }
}

/* ---------- DECISION ---------- */
function addVote(o){
 let w=+document.getElementById("w-"+o).value||1;
 votes.push({o,w}); updateCharts();
}
function getCounts(){
 let c={}; votes.forEach(v=>c[v.o]=(c[v.o]||0)+v.w); return c;
}
function resolveDecision(){
 let c=getCounts(),k=Object.keys(c);
 if(!k.length)return null;
 if(currentMethod==="random")return k[Math.floor(Math.random()*k.length)];
 if(currentMethod==="compromise"){
  let a=[];votes.forEach(v=>a.push(...Array(v.w).fill(v.o)));
  a.sort();return a[Math.floor(a.length/2)];
 }
 return k.reduce((a,b)=>c[a]>=c[b]?a:b);
}
function confidence(){
 let c=getCounts(),t=Object.values(c).reduce((a,b)=>a+b,0);
 return t?((Math.max(...Object.values(c))/t)*100).toFixed(1):0;
}
function checkEq(){
 let w=resolveDecision(); if(!w)return false;
 lastWinners.push(w); if(lastWinners.length>STAB)lastWinners.shift();
 return lastWinners.every(x=>x===w);
}

/* ---------- SAVE ---------- */
function saveResult(w){
 let data=JSON.parse(localStorage.getItem("consensusResults")||"[]");
 data.push({
  date:new Date().toLocaleString(),
  algorithm:currentMethod,
  speed:speedSelect.value,
  iterations:iteration,
  time:time.textContent,
  winner:w,
  confidence:confidence(),
  distribution:getCounts()
 });
 localStorage.setItem("consensusResults",JSON.stringify(data));
}
function showHistory(){
 let data=JSON.parse(localStorage.getItem("consensusResults")||"[]");
 historyLog.innerHTML=data.map((e,i)=>`
 <div>
 <b>#${i+1}</b> | ${e.date}<br>
 Алгоритм: ${e.algorithm}, Режим: ${e.speed}<br>
 Ітерації: ${e.iterations}, Час: ${e.time}<br>
 Переможець: <b>${e.winner}</b>, Довіра: ${e.confidence}%
 </div><hr>`).join("");
}
function exportResults(){
 let d=localStorage.getItem("consensusResults");
 if(!d)return alert("Немає даних");
 let a=document.createElement("a");
 a.href=URL.createObjectURL(new Blob([d],{type:"application/json"}));
 a.download="consensus_results.json";
 a.click();
}

/* ---------- VISUAL ---------- */
function animateLeader(w){
 document.querySelectorAll(".option").forEach(o=>{
  if(o.innerText.includes(w)){
    o.classList.add("leader","leader-change");
    setTimeout(()=>o.classList.remove("leader-change"),1000);
  }
 });
}
function log(t){eventLog.innerHTML=`<div>${t}</div>`+eventLog.innerHTML}

/* ---------- CHARTS ---------- */
function initCharts(){
 if(bar)bar.destroy(); if(pie)pie.destroy(); if(line)line.destroy();
 bar=new Chart(barC(),{type:"bar",data:{labels:options,datasets:[{data:options.map(()=>0)}]}});
 pie=new Chart(pieC(),{type:"pie",data:{labels:options,datasets:[{data:options.map(()=>0)}]}});
 line=new Chart(lineC(),{type:"line",data:{labels:[0],datasets:[]}});
}
function updateCharts(){
 let c=getCounts();
 bar.data.datasets[0].data=options.map(o=>c[o]||0); bar.update();
 pie.data.datasets[0].data=options.map(o=>c[o]||0); pie.update();
 step++; line.data.labels.push(step);
 line.data.datasets=options.map(o=>({
  label:o,
  data:(line.data.datasets.find(d=>d.label===o)?.data||[]).concat(c[o]||0)
 }));
 line.update();
}
function barC(){return document.getElementById("bar").getContext("2d")}
function pieC(){return document.getElementById("pie").getContext("2d")}
function lineC(){return document.getElementById("line").getContext("2d")}
</script>

</body>
</html>
