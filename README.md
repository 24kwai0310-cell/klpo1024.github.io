<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>포켓몬 도감 퀴즈</title>
    <!-- 아이콘 폰트 추가 -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@latest/tabler-icons.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700;900&display=swap');
        *{margin:0;padding:0;box-sizing:border-box;}
        :root{
          --red:#E3350D;--yellow:#FFCB05;
          --dark:#0d1b3e;--border:rgba(255,255,255,0.11);
          --text:#f0f4ff;--text2:#8899bb;
        }
        body{font-family:'Noto Sans KR',sans-serif;background:var(--dark);color:var(--text);}
        .scene{
          min-height:100vh;
          background:radial-gradient(ellipse at 75% 5%,rgba(59,91,167,0.2) 0%,transparent 50%),
                     radial-gradient(ellipse at 10% 85%,rgba(227,53,13,0.1) 0%,transparent 45%),
                     linear-gradient(160deg,#0d1b3e 0%,#101828 55%,#0a1f14 100%);
          padding:0 14px 48px;position:relative;overflow:hidden;
        }
        .stars{position:absolute;top:0;left:0;width:100%;height:100%;pointer-events:none;}
        .star{position:absolute;border-radius:50%;background:#fff;animation:twinkle var(--d,3s) ease-in-out infinite var(--delay,0s);}
        @keyframes twinkle{0%,100%{opacity:var(--op,.3);}50%{opacity:0.04;}}

        .wrap{max-width:600px;margin:0 auto;position:relative;z-index:1;}

        .hdr{display:flex;align-items:center;justify-content:space-between;padding:16px 0 13px;border-bottom:1px solid rgba(255,203,5,0.12);margin-bottom:18px;}
        .logo{display:flex;align-items:center;gap:8px;font-size:11px;font-weight:700;letter-spacing:1.5px;color:var(--yellow);text-transform:uppercase;}
        .ball{width:18px;height:18px;border-radius:50%;background:linear-gradient(180deg,var(--red) 50%,#eee 50%);border:2px solid rgba(255,255,255,0.2);position:relative;flex-shrink:0;}
        .ball::after{content:'';position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);width:5px;height:5px;background:#fff;border-radius:50%;border:1px solid #ccc;}
        .score-pill{background:rgba(255,203,5,0.13);border:1px solid rgba(255,203,5,0.28);border-radius:20px;padding:5px 15px;font-size:14px;font-weight:700;color:var(--yellow);}

        /* PROGRESS */
        .prog{margin-bottom:14px;}
        .prog-top{display:flex;justify-content:space-between;align-items:center;margin-bottom:6px;}
        .q-num{font-size:13px;color:var(--text2);}
        .hp-wrap{display:flex;align-items:center;gap:8px;}
        .hp-lbl{font-size:12px;color:var(--text2);}
        .hp-track{width:130px;height:7px;background:rgba(255,255,255,0.08);border-radius:4px;overflow:hidden;}
        .hp-bar{height:100%;border-radius:4px;transition:width .5s ease,background .4s;}
        .dots{display:flex;gap:4px;margin-top:7px;}
        .dot{flex:1;height:5px;border-radius:3px;background:rgba(255,255,255,0.09);transition:background .3s;}
        .dot.ok{background:#4ade80;}.dot.no{background:var(--red);}.dot.cur{background:var(--yellow);}

        /* TIMER */
        .timer-row{display:flex;align-items:center;justify-content:center;gap:10px;margin-bottom:14px;}
        .timer-track{flex:1;height:6px;background:rgba(255,255,255,0.08);border-radius:3px;overflow:hidden;}
        .timer-bar{height:100%;border-radius:3px;background:var(--yellow);transition:width .1s linear,background .3s;}
        .timer-num{font-size:22px;font-weight:900;min-width:32px;text-align:right;color:var(--yellow);font-family:monospace;transition:color .3s;}
        .timer-num.warn{color:#f87171;}

        /* ARENA */
        .arena{background:rgba(255,255,255,0.04);border:1px solid var(--border);border-radius:20px;overflow:hidden;margin-bottom:12px;}
        .stage{position:relative;height:200px;display:flex;align-items:center;justify-content:center;overflow:hidden;}
        .stage-sky{position:absolute;inset:0;background:linear-gradient(180deg,rgba(10,20,50,0) 0%,rgba(5,15,35,0.6) 100%);}
        .stage-ground{position:absolute;bottom:0;left:0;right:0;height:48px;background:rgba(50,180,100,0.07);border-top:1px solid rgba(50,200,80,0.15);}
        .gen-tag{position:absolute;top:11px;left:13px;font-size:11px;font-weight:700;background:rgba(255,203,5,0.14);border:1px solid rgba(255,203,5,0.28);color:var(--yellow);border-radius:20px;padding:3px 10px;}
        .dex-num{position:absolute;top:8px;right:12px;font-size:28px;font-weight:900;color:rgba(255,255,255,0.05);font-family:monospace;}
        .poke-wrap{position:relative;width:156px;height:156px;display:flex;align-items:center;justify-content:center;z-index:2;}
        .ring{position:absolute;inset:0;border-radius:50%;border:1.5px solid rgba(255,255,255,0.07);animation:spin 14s linear infinite;}
        .ring2{position:absolute;inset:12px;border-radius:50%;border:1px solid rgba(255,203,5,0.1);animation:spin 9s linear infinite reverse;}
        @keyframes spin{to{transform:rotate(360deg);}}
        .poke-img{width:130px;height:130px;object-fit:contain;position:relative;z-index:3;transition:filter .4s ease,transform .3s;image-rendering:pixelated;}
        .poke-img.sil{filter:brightness(0) drop-shadow(0 6px 16px rgba(0,0,0,.9));}
        .poke-img.vis{filter:drop-shadow(0 4px 20px rgba(255,203,5,.35));}
        .poke-img.shake{animation:shake .35s ease;}
        .poke-img.pop{animation:pop .35s ease;}
        @keyframes shake{0%,100%{transform:translateX(0);}25%{transform:translateX(-8px);}75%{transform:translateX(8px);}50%{transform:translateX(-4px);}}
        @keyframes pop{0%,100%{transform:scale(1);}50%{transform:scale(1.12);}}

        /* HINT CARDS */
        .hint-cards{display:grid;grid-template-columns:repeat(4,1fr);gap:7px;padding:12px 14px;border-top:1px solid rgba(255,255,255,0.05);}
        .hint-card{background:rgba(255,255,255,0.05);border:1px solid rgba(255,255,255,0.08);border-radius:11px;padding:10px 6px;text-align:center;}
        .hint-lbl{font-size:11px;color:var(--text2);margin-bottom:5px;}
        .hint-val{font-size:16px;font-weight:700;color:var(--text);line-height:1.2;}
        .hint-types{display:flex;flex-wrap:wrap;gap:4px;justify-content:center;}
        .chip{font-size:11px;font-weight:700;padding:3px 9px;border-radius:20px;}

        /* INPUT */
        .input-zone{margin-bottom:10px;}
        .input-lbl{font-size:12px;color:var(--text2);margin-bottom:6px;display:block;}
        .input-row{display:flex;gap:8px;}
        .poke-field{flex:1;background:rgba(255,255,255,0.05);border:1px solid rgba(255,255,255,0.13);border-radius:12px;padding:12px 14px;color:var(--text);font-family:'Noto Sans KR',sans-serif;font-size:16px;outline:none;transition:border-color .2s,background .2s;}
        .poke-field::placeholder{color:rgba(255,255,255,0.2);}
        .poke-field:focus{border-color:rgba(255,203,5,.45);background:rgba(255,203,5,.03);}
        .poke-field.ok{border-color:#4ade80;background:rgba(74,222,128,.07);}
        .poke-field.no{border-color:var(--red);background:rgba(227,53,13,.07);}
        .go-btn{background:var(--red);border:none;border-radius:12px;color:#fff;font-family:'Noto Sans KR',sans-serif;font-size:15px;font-weight:700;padding:0 22px;cursor:pointer;transition:transform .15s,opacity .15s;white-space:nowrap;}
        .go-btn:hover:not(:disabled){transform:scale(1.04);}
        .go-btn:disabled{opacity:.35;cursor:not-allowed;}

        /* FEEDBACK */
        .fb{border-radius:11px;padding:11px 14px;display:flex;align-items:center;gap:8px;font-size:14px;min-height:44px;margin-bottom:10px;transition:all .25s;}
        .fb.idle{background:rgba(255,255,255,0.03);border:1px solid rgba(255,255,255,0.06);color:var(--text2);}
        .fb.ok{background:rgba(74,222,128,.1);border:1px solid rgba(74,222,128,.28);color:#5ded91;font-weight:700;}
        .fb.no{background:rgba(227,53,13,.1);border:1px solid rgba(227,53,13,.28);color:#ff7a6a;font-weight:700;}

        /* BUTTONS */
        .btns{display:flex;gap:8px;}
        .ghost-btn{flex:1;background:rgba(255,255,255,0.04);border:1px solid rgba(255,255,255,0.09);border-radius:10px;color:var(--text2);font-family:'Noto Sans KR',sans-serif;font-size:13px;padding:10px 0;cursor:pointer;transition:all .2s;display:flex;align-items:center;justify-content:center;gap:5px;}
        .ghost-btn:hover:not(:disabled){background:rgba(255,255,255,0.08);color:var(--text);}
        .ghost-btn:disabled{opacity:.3;cursor:not-allowed;}

        /* RESULT */
        .result-card{background:rgba(255,255,255,0.04);border:1px solid var(--border);border-radius:22px;padding:32px 18px;text-align:center;margin-top:8px;}
        .r-icon{font-size:56px;margin-bottom:10px;display:block;}
        .r-rank{font-size:11px;font-weight:700;letter-spacing:2px;color:var(--yellow);margin-bottom:5px;text-transform:uppercase;}
        .r-title{font-size:21px;font-weight:900;margin-bottom:4px;}
        .r-sub{font-size:13px;color:var(--text2);margin-bottom:20px;}
        .r-score{display:inline-flex;align-items:baseline;gap:3px;margin-bottom:20px;}
        .r-big{font-size:52px;font-weight:900;color:var(--yellow);line-height:1;}
        .r-den{font-size:19px;color:var(--text2);}
        .r-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin-bottom:18px;}
        .r-box{background:rgba(255,255,255,0.05);border:1px solid rgba(255,255,255,0.07);border-radius:11px;padding:12px 6px;}
        .r-n{font-size:20px;font-weight:700;margin-bottom:2px;}
        .r-l{font-size:11px;color:var(--text2);}
        .restart{background:var(--red);border:none;border-radius:13px;color:#fff;font-family:'Noto Sans KR',sans-serif;font-size:15px;font-weight:700;padding:13px 34px;cursor:pointer;transition:transform .15s;display:inline-flex;align-items:center;gap:7px;}
        .restart:hover{transform:scale(1.04);}
    </style>
</head>
<body>
    <div class="scene">
        <div class="stars" id="stars"></div>
        <div class="wrap">
          <div class="hdr">
            <div class="logo"><div class="ball"></div>포켓몬 도감 퀴즈</div>
            <div class="score-pill" id="scorePill">점수 0</div>
          </div>
          <div id="main"></div>
        </div>
    </div>

    <script>
    const PK=[
      {id:1,name:"이상해씨",en:"Bulbasaur",gen:1,types:["풀","독"],h:"0.7m",w:"6.9kg",c:"초록색"},
      {id:4,name:"파이리",en:"Charmander",gen:1,types:["불꽃"],h:"0.6m",w:"8.5kg",c:"주황색"},
      {id:7,name:"꼬부기",en:"Squirtle",gen:1,types:["물"],h:"0.5m",w:"9.0kg",c:"파란색"},
      {id:25,name:"피카츄",en:"Pikachu",gen:1,types:["전기"],h:"0.4m",w:"6.0kg",c:"노란색"},
      {id:39,name:"푸린",en:"Jigglypuff",gen:1,types:["노말","요정"],h:"0.5m",w:"5.5kg",c:"분홍색"},
      {id:52,name:"나옹",en:"Meowth",gen:1,types:["노말"],h:"0.4m",w:"4.2kg",c:"크림색"},
      {id:54,name:"고라파덕",en:"Psyduck",gen:1,types:["물"],h:"0.8m",w:"19.6kg",c:"노란색"},
      {id:94,name:"팬텀",en:"Gengar",gen:1,types:["고스트","독"],h:"1.5m",w:"40.5kg",c:"보라색"},
      {id:143,name:"잠만보",en:"Snorlax",gen:1,types:["노말"],h:"2.1m",w:"460kg",c:"청록색"},
      {id:150,name:"뮤츠",en:"Mewtwo",gen:1,types:["에스퍼"],h:"2.0m",w:"122kg",c:"보라색"},
      {id:152,name:"치코리타",en:"Chikorita",gen:2,types:["풀"],h:"0.9m",w:"6.4kg",c:"연두색"},
      {id:155,name:"브케인",en:"Cyndaquil",gen:2,types:["불꽃"],h:"0.5m",w:"7.9kg",c:"파랑/빨강"},
      {id:158,name:"리아코",en:"Totodile",gen:2,types:["물"],h:"0.6m",w:"9.5kg",c:"파란색"},
      {id:175,name:"토게피",en:"Togepi",gen:2,types:["요정"],h:"0.3m",w:"1.5kg",c:"흰색"},
      {id:197,name:"블래키",en:"Umbreon",gen:2,types:["악"],h:"1.0m",w:"27.0kg",c:"검정색"},
      {id:249,name:"루기아",en:"Lugia",gen:2,types:["에스퍼","비행"],h:"5.2m",w:"216kg",c:"흰색"},
      {id:252,name:"나무지기",en:"Treecko",gen:3,types:["풀"],h:"0.5m",w:"5.0kg",c:"초록색"},
      {id:255,name:"아차모",en:"Torchic",gen:3,types:["불꽃"],h:"0.4m",w:"2.5kg",c:"주황색"},
      {id:258,name:"물짱이",en:"Mudkip",gen:3,types:["물"],h:"0.4m",w:"7.6kg",c:"파란색"},
      {id:384,name:"레쿠쟈",en:"Rayquaza",gen:3,types:["드래곤","비행"],h:"7.0m",w:"206.5kg",c:"초록색"},
      {id:393,name:"팽도리",en:"Piplup",gen:4,types:["물"],h:"0.4m",w:"5.2kg",c:"파란색"},
      {id:448,name:"루카리오",en:"Lucario",gen:4,types:["격투","강철"],h:"1.2m",w:"54.0kg",c:"파랑/검정"},
      {id:495,name:"주리비얀",en:"Snivy",gen:5,types:["풀"],h:"0.6m",w:"8.1kg",c:"초록색"},
      {id:501,name:"수댕이",en:"Oshawott",gen:5,types:["물"],h:"0.5m",w:"5.9kg",c:"파랑/흰색"},
      {id:571,name:"조로아크",en:"Zoroark",gen:5,types:["악"],h:"1.6m",w:"81.1kg",c:"검정/빨강"},
      {id:656,name:"개구마르",en:"Froakie",gen:6,types:["물"],h:"0.3m",w:"7.0kg",c:"파랑/흰색"},
      {id:700,name:"님피아",en:"Sylveon",gen:6,types:["요정"],h:"1.0m",w:"23.5kg",c:"분홍/흰색"},
      {id:722,name:"나몰빼미",en:"Rowlet",gen:7,types:["풀","비행"],h:"0.3m",w:"1.5kg",c:"초록/흰색"},
      {id:810,name:"흥나숭",en:"Grookey",gen:8,types:["풀"],h:"0.3m",w:"5.0kg",c:"초록색"},
      {id:813,name:"염버니",en:"Scorbunny",gen:8,types:["불꽃"],h:"0.3m",w:"4.5kg",c:"흰색"},
      {id:816,name:"울머기",en:"Sobble",gen:8,types:["물"],h:"0.3m",w:"4.0kg",c:"파란색"},
      {id:888,name:"자시안",en:"Zacian",gen:8,types:["요정"],h:"2.8m",w:"110kg",c:"파란색"},
      {id:906,name:"나오하",en:"Sprigatito",gen:9,types:["풀"],h:"0.4m",w:"4.1kg",c:"초록색"},
      {id:909,name:"뜨아거",en:"Fuecoco",gen:9,types:["불꽃"],h:"0.4m",w:"9.8kg",c:"빨간색"},
      {id:912,name:"꾸왁스",en:"Quaxly",gen:9,types:["물"],h:"0.5m",w:"6.1kg",c:"파랑/흰색"},
    ];

    const TCOLORS={
      "불꽃":{bg:"rgba(240,128,48,.22)",col:"#fca95a",bd:"rgba(240,128,48,.38)"},
      "물":{bg:"rgba(104,144,240,.22)",col:"#7ab0ff",bd:"rgba(104,144,240,.38)"},
      "풀":{bg:"rgba(120,200,80,.22)",col:"#7dd668",bd:"rgba(120,200,80,.38)"},
      "전기":{bg:"rgba(248,208,48,.22)",col:"#fdd747",bd:"rgba(248,208,48,.38)"},
      "에스퍼":{bg:"rgba(248,88,136,.22)",col:"#ff8ab0",bd:"rgba(248,88,136,.38)"},
      "노말":{bg:"rgba(168,168,120,.22)",col:"#c8c880",bd:"rgba(168,168,120,.38)"},
      "비행":{bg:"rgba(168,144,240,.22)",col:"#c0a8ff",bd:"rgba(168,144,240,.38)"},
      "독":{bg:"rgba(160,64,160,.22)",col:"#d080d0",bd:"rgba(160,64,160,.38)"},
      "고스트":{bg:"rgba(112,88,152,.22)",col:"#b09cd0",bd:"rgba(112,88,152,.38)"},
      "드래곤":{bg:"rgba(112,56,248,.22)",col:"#a878ff",bd:"rgba(112,56,248,.38)"},
      "악":{bg:"rgba(112,88,72,.22)",col:"#c09078",bd:"rgba(112,88,72,.38)"},
      "강철":{bg:"rgba(184,184,208,.22)",col:"#c8c8e0",bd:"rgba(184,184,208,.38)"},
      "요정":{bg:"rgba(238,153,172,.22)",col:"#f0a0bc",bd:"rgba(238,153,172,.38)"},
      "격투":{bg:"rgba(192,48,40,.22)",col:"#e87070",bd:"rgba(192,48,40,.38)"},
      "바위":{bg:"rgba(184,160,56,.22)",col:"#d4b840",bd:"rgba(184,160,56,.38)"},
    };

    const GEN={1:"1세대",2:"2세대",3:"3세대",4:"4세대",5:"5세대",6:"6세대",7:"7세대",8:"8세대",9:"9세대"};
    const TIME_LIMIT=20;
    const TOTAL=10;

    let qs,cur,score,wrong,streak,best,answered,revealed,busy,timeLeft,timerInterval;

    function shuffle(a){return[...a].sort(()=>Math.random()-0.5);}

    function init(){
      qs=shuffle(PK).slice(0,TOTAL);
      cur=0;score=0;wrong=0;streak=0;best=0;answered=[];revealed=false;busy=false;
      clearInterval(timerInterval);
      document.getElementById("scorePill").textContent="점수 0";
      renderQ();
    }

    function imgUrl(id){return`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`;}

    function chip(t){
      const s=TCOLORS[t]||{bg:"rgba(128,128,128,.2)",col:"#aaa",bd:"rgba(128,128,128,.3)"};
      return`<span class="chip" style="background:${s.bg};color:${s.col};border:1px solid ${s.bd};">${t}</span>`;
    }

    function hpColor(p){
      if(p>60)return"linear-gradient(90deg,#4ade80,#22c55e)";
      if(p>30)return"linear-gradient(90deg,#fbbf24,#f59e0b)";
      return"linear-gradient(90deg,#f87171,#e3350d)";
    }

    function startTimer(){
      clearInterval(timerInterval);
      timeLeft=TIME_LIMIT;
      updateTimerUI();
      timerInterval=setInterval(()=>{
        timeLeft--;
        updateTimerUI();
        if(timeLeft<=0){clearInterval(timerInterval);timeUp();}
      },1000);
    }

    function updateTimerUI(){
      const bar=document.getElementById("timerBar");
      const num=document.getElementById("timerNum");
      if(!bar||!num)return;
      const pct=Math.round((timeLeft/TIME_LIMIT)*100);
      bar.style.width=pct+"%";
      num.textContent=timeLeft;
      if(timeLeft<=7){
        bar.style.background="linear-gradient(90deg,#f87171,#e3350d)";
        num.className="timer-num warn";
      } else if(timeLeft<=12){
        bar.style.background="linear-gradient(90deg,#fbbf24,#f59e0b)";
        num.className="timer-num";
      } else {
        bar.style.background="#FFCB05";
        num.className="timer-num";
      }
    }

    function timeUp(){
      if(busy)return;
      const q=qs[cur];
      lock();reveal();
      wrong++;streak=0;answered.push(false);
      setFb("no",`<span>⏰</span><span>시간 초과! 정답은 <strong>${q.name}</strong> (${q.en})</span>`);
      const img=document.getElementById("pimg");
      if(img){img.classList.add("shake");setTimeout(()=>img.classList.remove("shake"),400);}
      setTimeout(next,1700);
    }

    function renderQ(){
      const q=qs[cur];
      const hp=Math.round(((TOTAL-wrong)/TOTAL)*100);
      const dots=qs.map((_,i)=>{
        let c="dot";
        if(i<cur)c+=" "+(answered[i]?"ok":"no");
        else if(i===cur)c+=" cur";
        return`<div class="${c}"></div>`;
      }).join("");
      const types=q.types.map(chip).join("");

      document.getElementById("main").innerHTML=`
        <div class="prog">
          <div class="prog-top">
            <span class="q-num">문제 ${cur+1} / ${TOTAL}</span>
            <div class="hp-wrap">
              <span class="hp-lbl">HP ${hp}%</span>
              <div class="hp-track"><div class="hp-bar" id="hpBar" style="width:${hp}%;background:${hpColor(hp)};"></div></div>
            </div>
          </div>
          <div class="dots">${dots}</div>
        </div>

        <div class="timer-row">
          <div class="timer-track"><div class="timer-bar" id="timerBar" style="width:100%"></div></div>
          <span class="timer-num" id="timerNum">${TIME_LIMIT}</span>
        </div>

        <div class="arena">
          <div class="stage">
            <div class="stage-sky"></div>
            <div class="stage-ground"></div>
            <div class="gen-tag">${GEN[q.gen]}</div>
            <div class="dex-num">#${String(q.id).padStart(3,"0")}</div>
            <div class="poke-wrap">
              <div class="ring"></div>
              <div class="ring2"></div>
              <img id="pimg" class="poke-img sil"
                src="${imgUrl(q.id)}"
                width="130" height="130" alt="포켓몬"
                onerror="this.style.display='none'">
            </div>
          </div>

          <div class="hint-cards">
            <div class="hint-card">
              <div class="hint-lbl">타입</div>
              <div class="hint-val hint-types">${types}</div>
            </div>
            <div class="hint-card">
              <div class="hint-lbl">키</div>
              <div class="hint-val">${q.h}</div>
            </div>
            <div class="hint-card">
              <div class="hint-lbl">몸무게</div>
              <div class="hint-val">${q.w}</div>
            </div>
            <div class="hint-card">
              <div class="hint-lbl">색깔</div>
              <div class="hint-val" style="font-size:14px;">${q.c}</div>
            </div>
          </div>
        </div>

        <div class="input-zone">
          <span class="input-lbl">이 포켓몬의 이름은? (한글 또는 영어)</span>
          <div class="input-row">
            <input class="poke-field" id="ans" type="text" placeholder="이름 입력..." autocomplete="off" spellcheck="false">
            <button class="go-btn" id="goBtn" onclick="check()">확인</button>
          </div>
        </div>

        <div class="fb idle" id="fb"><span>💡</span><span>20초 안에 맞춰보세요!</span></div>

        <div class="btns">
          <button class="ghost-btn" id="revBtn" onclick="reveal()">
            <i class="ti ti-eye" style="font-size:14px" aria-hidden="true"></i> 이미지 공개
          </button>
          <button class="ghost-btn" id="skipBtn" onclick="skip()">
            <i class="ti ti-arrow-right" style="font-size:14px" aria-hidden="true"></i> 건너뛰기
          </button>
        </div>
      `;

      const inp=document.getElementById("ans");
      inp.addEventListener("keydown",e=>{if(e.key==="Enter"&&!busy)check();});
      inp.focus();
      startTimer();
    }

    function reveal(){
      const img=document.getElementById("pimg");
      if(img){img.classList.remove("sil");img.classList.add("vis");}
      revealed=true;
      const b=document.getElementById("revBtn");if(b)b.disabled=true;
    }

    function setFb(cls,html){
      const fb=document.getElementById("fb");
      if(fb){fb.className=`fb ${cls}`;fb.innerHTML=html;}
    }

    function lock(){
      busy=true;clearInterval(timerInterval);
      ["goBtn","revBtn","skipBtn"].forEach(id=>{const el=document.getElementById(id);if(el)el.disabled=true;});
      const inp=document.getElementById("ans");if(inp)inp.disabled=true;
    }

    function next(){
      cur++;busy=false;revealed=false;
      if(cur>=TOTAL)renderResult();else renderQ();
    }

    function check(){
      if(busy)return;
      const inp=document.getElementById("ans");
      const val=(inp.value||"").trim().replace(/\s+/g,"");
      if(!val)return;
      const q=qs[cur];
      const ok=val===q.name.replace(/\s+/g,"")||val.toLowerCase()===q.en.toLowerCase().replace(/\s+/g,"");
      lock();reveal();
      inp.classList.add(ok?"ok":"no");
      const img=document.getElementById("pimg");
      if(ok){
        score++;streak++;best=Math.max(best,streak);answered.push(true);
        if(img){img.classList.add("pop");setTimeout(()=>img.classList.remove("pop"),400);}
        setFb("ok",`<span>🎉</span><span>정답! <strong>${q.name}</strong> (${q.en}) — 연속 ${streak}개!</span>`);
        document.getElementById("scorePill").textContent=`점수 ${score}`;
      } else {
        wrong++;streak=0;answered.push(false);
        if(img){img.classList.add("shake");setTimeout(()=>img.classList.remove("shake"),400);}
        setFb("no",`<span>❌</span><span>오답! 정답은 <strong>${q.name}</strong> (${q.en})</span>`);
      }
      setTimeout(next,1700);
    }

    function skip(){
      if(busy)return;
      const q=qs[cur];
      lock();reveal();
      wrong++;streak=0;answered.push(false);
      setFb("no",`<span>➡️</span><span>건너뜀! 정답은 <strong>${q.name}</strong> (${q.en})</span>`);
      setTimeout(next,1600);
    }

    function renderResult(){
      clearInterval(timerInterval);
      const pct=Math.round((score/TOTAL)*100);
      let icon="😢",rank="트레이너 지망생",msg="포켓몬을 더 많이 만나보세요!";
      if(pct>=100){icon="🏆";rank="포켓몬 마스터";msg="완벽한 실력! 전설의 트레이너!";}
      else if(pct>=80){icon="🌟";rank="포켓몬 박사";msg="훌륭한 실력이에요!";}
      else if(pct>=60){icon="😊";rank="숙련 트레이너";msg="좋은 실력이에요!";}
      else if(pct>=40){icon="🙂";rank="견습 트레이너";msg="조금 더 연습해봐요!";}
      const stars="★".repeat(Math.ceil(pct/20))+"☆".repeat(5-Math.ceil(pct/20));

      document.getElementById("main").innerHTML=`
        <div class="result-card">
          <span class="r-icon">${icon}</span>
          <div class="r-rank">${rank}</div>
          <div class="r-title">퀴즈 완료!</div>
          <div class="r-sub">${msg}</div>
          <div class="r-score">
            <span class="r-big">${score}</span>
            <span class="r-den">/ ${TOTAL}</span>
          </div>
          <div class="r-grid">
            <div class="r-box"><div class="r-n" style="color:#4ade80">${score}</div><div class="r-l">정답</div></div>
            <div class="r-box"><div class="r-n" style="color:#f87171">${wrong}</div><div class="r-l">오답</div></div>
            <div class="r-box"><div class="r-n" style="color:#fbbf24">${best}</div><div class="r-l">연속</div></div>
            <div class="r-box"><div class="r-n">${pct}%</div><div class="r-l">정확도</div></div>
          </div>
          <div style="font-size:22px;margin-bottom:20px;letter-spacing:3px;">${stars}</div>
          <button class="restart" onclick="init()">
            <i class="ti ti-refresh" style="font-size:17px" aria-hidden="true"></i>
            다시 도전하기
          </button>
        </div>
      `;
    }

    (function makeStars(){
      const c=document.getElementById("stars");if(!c)return;
      for(let i=0;i<90;i++){
        const s=document.createElement("div");s.className="star";
        const sz=Math.random()*2+0.5;
        s.style.cssText=`width:${sz}px;height:${sz}px;left:${Math.random()*100}%;top:${Math.random()*100}%;--op:${(Math.random()*.5+.1).toFixed(2)};--d:${(Math.random()*3+2).toFixed(1)}s;--delay:-${(Math.random()*4).toFixed(1)}s;`;
        c.appendChild(s);
      }
    })();

    init();
    </script>
</body>
</html>
