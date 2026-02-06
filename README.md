<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Свадьба Александра и Анастасии</title>

<style>
body{
  margin:0;
  font-family: system-ui, sans-serif;
  color:#333;
  text-align:center;
  overflow-x:hidden;

  background:
    linear-gradient(rgba(255,255,255,.75), rgba(255,255,255,.9)),
    url("photo.jpg") center/cover fixed no-repeat;
}

canvas{
  position:fixed;
  top:0;
  left:0;
  pointer-events:none;
}

.card{
  max-width:650px;
  margin:40px auto;
  padding:30px;
  background:#ffffffee;
  border-radius:22px;
  box-shadow:0 10px 40px rgba(0,0,0,.12);
}

h1{font-size:34px;margin:10px 0}
.timer{font-size:22px;margin:15px 0}

input{
  width:90%;
  padding:12px;
  margin:8px 0;
  border-radius:12px;
  border:1px solid #ddd;
}

.buttons{
  display:flex;
  justify-content:center;
  gap:12px;
  margin-top:10px;
}

button{
  padding:12px 24px;
  border:none;
  border-radius:12px;
  font-size:15px;
  cursor:pointer;
}

.yes{background:#a8d8ff}
.no{background:#f2c4c4}
.download{margin-top:18px;background:#ddd}

.start{
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
  flex-direction:column;
}

.hidden{display:none}
</style>
</head>

<body>

<canvas id="petals"></canvas>

<div id="start" class="start">
  <h1>Александр & Анастасия</h1>
  <p>приглашают вас на свадьбу</p>
  <button onclick="openInvite()">Открыть приглашение</button>
</div>

<div id="invite" class="hidden">
  <div class="card">

    <h1>23 мая 2026 💍</h1>
    <div id="timer" class="timer"></div>

    <p>Дресс-код: нежно-голубые и белые оттенки</p>

    <h2>Вы придёте?</h2>

    <input id="name" placeholder="Ваше имя"/>
    <input id="count" type="number" value="1" min="1" placeholder="Сколько человек"/>

    <div class="buttons">
      <button class="yes" onclick="saveAnswer('Да')">Да</button>
      <button class="no" onclick="saveAnswer('Нет')">Нет</button>
    </div>

    <!-- только для вас -->
    <button class="download" onclick="downloadCSV()">Скачать ответы (Excel)</button>

  </div>
</div>

<audio id="music" loop>
  <source src="music.mp3" type="audio/mpeg">
</audio>

<script>
function openInvite(){
  start.classList.add("hidden");
  invite.classList.remove("hidden");
  music.play();
}

/* таймер */
const date=new Date("2026-05-23T15:00:00");
setInterval(()=>{
  const diff=date-new Date();
  const d=Math.floor(diff/86400000);
  const h=Math.floor(diff/3600000)%24;
  const m=Math.floor(diff/60000)%60;
  timer.textContent=`До свадьбы: ${d} д ${h} ч ${m} мин`;
},1000);

/* сохранение ответов локально */
function saveAnswer(ans){
  const nameVal=name.value || "Без имени";
  const countVal=count.value || 1;

  const list=JSON.parse(localStorage.getItem("wedding")) || [];

  list.push({
    name:nameVal,
    guests:countVal,
    answer:ans,
    time:new Date().toLocaleString()
  });

  localStorage.setItem("wedding",JSON.stringify(list));

  alert("Спасибо за ответ ❤️");
}

/* выгрузка в Excel */
function downloadCSV(){
  const data=JSON.parse(localStorage.getItem("wedding")) || [];
  let csv="Имя,Количество,Ответ,Время\n";

  data.forEach(r=>{
    csv+=`${r.name},${r.guests},${r.answer},${r.time}\n`;
  });

  const blob=new Blob([csv],{type:"text/csv"});
  const a=document.createElement("a");
  a.href=URL.createObjectURL(blob);
  a.download="answers.csv";
  a.click();
}

/* лепестки */
const canvas=document.getElementById("petals");
const ctx=canvas.getContext("2d");
canvas.width=innerWidth;
canvas.height=innerHeight;

let p=[];
for(let i=0;i<40;i++){
  p.push({x:Math.random()*innerWidth,y:Math.random()*innerHeight,s:Math.random()*1+0.5});
}

function draw(){
  ctx.clearRect(0,0,canvas.width,canvas.height);
  ctx.fillStyle="#ffdbe8";
  p.forEach(e=>{
    ctx.beginPath();
    ctx.arc(e.x,e.y,5,0,6.28);
    ctx.fill();
    e.y+=e.s;
    if(e.y>innerHeight)e.y=0;
  });
  requestAnimationFrame(draw);
}
draw();
</script>

</body>
</html>
