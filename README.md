<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>포켓몬 도감 퀴즈</title>

<link rel="stylesheet"
href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@latest/tabler-icons.min.css">

<style>

*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:'Noto Sans KR',sans-serif;
}

body{
background:#0d1b3e;
color:white;
min-height:100vh;
display:flex;
justify-content:center;
align-items:center;
padding:20px;
}

.container{
width:100%;
max-width:500px;
background:rgba(255,255,255,0.05);
border:1px solid rgba(255,255,255,0.1);
border-radius:20px;
padding:24px;
backdrop-filter:blur(10px);
}

.header{
display:flex;
justify-content:space-between;
align-items:center;
margin-bottom:20px;
}

.title{
font-size:22px;
font-weight:900;
color:#FFCB05;
}

.mail-btn{
background:rgba(255,255,255,0.08);
border:1px solid rgba(255,255,255,0.15);
padding:10px 14px;
border-radius:12px;
color:white;
text-decoration:none;
font-size:13px;
display:flex;
align-items:center;
gap:5px;
}

.mode-select{
display:flex;
flex-direction:column;
gap:14px;
}

.mode-btn{
padding:18px;
border:none;
border-radius:16px;
background:#E3350D;
color:white;
font-size:16px;
font-weight:700;
cursor:pointer;
}

.quiz{
display:none;
}

.player-turn{
margin-bottom:12px;
font-size:15px;
font-weight:700;
color:#4fc3f7;
}

.image-box{
display:flex;
justify-content:center;
margin:20px 0;
}

.image-box img{
width:180px;
image-rendering:pixelated;
filter:brightness(0);
transition:0.3s;
}

.image-box img.show{
filter:none;
}

.hints{
display:grid;
grid-template-columns:1fr 1fr;
gap:10px;
margin-bottom:18px;
}

.hint{
background:rgba(255,255,255,0.06);
padding:12px;
border-radius:12px;
text-align:center;
}

.input-row{
display:flex;
gap:10px;
margin-top:10px;
}

input{
flex:1;
padding:12px;
border:none;
border-radius:12px;
font-size:16px;
}

button{
cursor:pointer;
}

.check-btn{
padding:12px 18px;
border:none;
border-radius:12px;
background:#E3350D;
color:white;
font-weight:700;
}

.bottom-btns{
display:flex;
gap:10px;
margin-top:12px;
}

.bottom-btns button{
flex:1;
padding:12px;
border:none;
border-radius:12px;
background:rgba(255,255,255,0.08);
color:white;
}

.result{
margin-top:16px;
font-size:18px;
font-weight:700;
text-align:center;
}

.score{
margin-top:14px;
text-align:center;
font-size:15px;
color:#FFCB05;
}

.timer{
margin-top:10px;
text-align:center;
font-size:20px;
font-weight:900;
color:#f87171;
}

</style>
</head>

<body>

<div class="container">

<div class="header">
<div class="title">⚡ 포켓몬 도감 퀴즈</div>

<a class="mail-btn"
href="mailto:24kwai0310@sonline20.sen.go.kr">
<i class="ti ti-mail"></i>
문의하기
</a>
</div>

<div id="modeSelect" class="mode-select">

<button class="mode-btn" onclick="startSolo()">
🏆 솔로 플레이
</button>

<button class="mode-btn" onclick="startMulti()">
⚔️ 멀티 플레이
</button>

</div>

<div class="quiz" id="quiz">

<div class="player-turn" id="playerTurn"></div>

<div class="timer">
⏰ <span id="timer">20</span>
</div>

<div class="image-box">
<img id="pokemonImg" src="">
</div>

<div class="hints">

<div class="hint">
<div>세대</div>
<div id="gen"></div>
</div>

<div class="hint">
<div>타입</div>
<div id="type"></div>
</div>

<div class="hint">
<div>색깔</div>
<div id="color"></div>
</div>

<div class="hint">
<div>키</div>
<div id="height"></div>
</div>

</div>

<div class="input-row">

<input type="text"
id="answer"
placeholder="포켓몬 이름 입력">

<button class="check-btn"
onclick="checkAnswer()">
확인
</button>

</div>

<div class="bottom-btns">

<button onclick="showPokemon()">
이미지 공개
</button>

<button onclick="nextQuestion()">
건너뛰기
</button>

</div>

<div class="result" id="result"></div>

<div class="score" id="score"></div>

</div>

</div>

<script>

const pokemons = [

{
name:"피카츄",
type:"전기",
gen:"1세대",
color:"노란색",
height:"0.4m",
img:"https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/25.png"
},

{
name:"파이리",
type:"불꽃",
gen:"1세대",
color:"주황색",
height:"0.6m",
img:"https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/4.png"
},

{
name:"꼬부기",
type:"물",
gen:"1세대",
color:"파란색",
height:"0.5m",
img:"https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/7.png"
},

{
name:"이상해씨",
type:"풀 / 독",
gen:"1세대",
color:"초록색",
height:"0.7m",
img:"https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/1.png"
},

{
name:"님피아",
type:"페어리",
gen:"6세대",
color:"분홍색",
height:"1.0m",
img:"https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/700.png"
},

{
name:"블래키",
type:"악",
gen:"2세대",
color:"검정색",
height:"1.0m",
img:"https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/197.png"
}

];

let currentPokemon;
let score1 = 0;
let score2 = 0;
let currentPlayer = 1;
let multiMode = false;
let timer = 20;
let interval;

function randomPokemon(){
return pokemons[Math.floor(Math.random()*pokemons.length)];
}

function startSolo(){

multiMode = false;

document.getElementById("modeSelect").style.display="none";
document.getElementById("quiz").style.display="block";

loadPokemon();

}

function startMulti(){

multiMode = true;

document.getElementById("modeSelect").style.display="none";
document.getElementById("quiz").style.display="block";

loadPokemon();

}

function loadPokemon(){

currentPokemon = randomPokemon();

document.getElementById("pokemonImg").src =
currentPokemon.img;

document.getElementById("pokemonImg").classList.remove("show");

document.getElementById("gen").textContent =
currentPokemon.gen;

document.getElementById("type").textContent =
currentPokemon.type;

document.getElementById("color").textContent =
currentPokemon.color;

document.getElementById("height").textContent =
currentPokemon.height;

document.getElementById("answer").value = "";

document.getElementById("result").textContent = "";

updatePlayerTurn();

startTimer();

}

function updatePlayerTurn(){

if(multiMode){

document.getElementById("playerTurn").textContent =
`플레이어 ${currentPlayer} 차례`;

document.getElementById("score").textContent =
`플레이어1 : ${score1}점   |   플레이어2 : ${score2}점`;

}else{

document.getElementById("playerTurn").textContent =
"솔로 플레이";

document.getElementById("score").textContent =
`점수 : ${score1}`;

}

}

function startTimer(){

clearInterval(interval);

timer = 20;

document.getElementById("timer").textContent = timer;

interval = setInterval(()=>{

timer--;

document.getElementById("timer").textContent = timer;

if(timer <= 0){

clearInterval(interval);

document.getElementById("result").textContent =
"⏰ 시간 초과!";

showPokemon();

}

},1000);

}

function checkAnswer(){

const input =
document.getElementById("answer").value.trim();

if(input === currentPokemon.name){

document.getElementById("result").textContent =
"🎉 정답입니다!";

showPokemon();

clearInterval(interval);

if(multiMode){

if(currentPlayer === 1){

score1++;

}else{

score2++;

}

}else{

score1++;

}

updatePlayerTurn();

}else{

document.getElementById("result").textContent =
"❌ 틀렸습니다!";

}

}

function showPokemon(){

document.getElementById("pokemonImg")
.classList.add("show");

}

function nextQuestion(){

if(multiMode){

currentPlayer = currentPlayer === 1 ? 2 : 1;

}

loadPokemon();

}

</script>

</body>
</html>
