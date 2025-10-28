
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>.</title>
<style>
*{box-sizing:border-box;margin:0;padding:0;font-family:"Poppins",sans-serif;transition:all 0.3s ease;}
body{background:#030617;color:#fff;overflow:hidden;}
canvas#bg{position:fixed;top:0;left:0;width:100%;height:100%;z-index:-1;}
.container{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;text-align:center;padding:20px;}
h1{font-size:2.8rem;background:linear-gradient(45deg,#00bcd4,#3f51b5);-webkit-background-clip:text;-webkit-text-fill-color:transparent;text-shadow:0 0 20px rgba(0,188,212,0.6);margin-bottom:25px;}
button{background:linear-gradient(135deg,#00bcd4,#3f51b5);border:none;color:white;padding:15px 25px;border-radius:15px;font-size:1.2rem;cursor:pointer;margin:8px;box-shadow:0 0 15px rgba(63,81,181,0.5);transition:0.3s;min-width:130px;font-weight:600;}
button:hover{transform:scale(1.07);box-shadow:0 0 25px #00bcd4,0 0 35px #3f51b5;}
.question-box{background:rgba(255,255,255,0.05);padding:30px;border-radius:20px;width:90%;max-width:750px;box-shadow:0 0 40px rgba(0,188,212,0.3);transition:0.5s;}
.answers{display:grid;grid-template-columns:1fr 1fr;gap:15px;margin-top:20px;}
@media(max-width:600px){.answers{grid-template-columns:1fr;}}
.hidden{display:none;}
.points{position:absolute;top:15px;right:20px;background:rgba(0,0,0,0.5);padding:10px 18px;border-radius:14px;font-weight:700;box-shadow:0 0 15px #00bcd4;font-size:1.1rem;}
footer{position:fixed;bottom:10px;width:100%;text-align:center;font-size:0.9rem;color:#88c9f9;}
</style>
</head>
<body>
<canvas id="bg"></canvas>

<div class="container" id="start-screen">
  <h1>Quiz Bíblico Avançado</h1>
  <button onclick="showLevels()">Começar</button>
</div>

<div class="container hidden" id="level-screen">
  <h1>Escolha o Nível</h1>
  <button onclick="startQuiz('easy')">Fácil</button>
  <button onclick="startQuiz('medium')">Médio</button>
  <button onclick="startQuiz('hard')">Difícil</button>
</div>

<div class="container hidden" id="quiz-screen">
  <div class="points" id="points">PONTOS: 0</div>
  <div class="question-box">
    <h2 id="question-text">Pergunta</h2>
    <div class="answers" id="answers"></div>
  </div>
</div>

<footer>Feito por <strong>Gabyteo</strong></footer>

<script>
/* Fundo tech/celestial */
const canvas=document.getElementById('bg');
const ctx=canvas.getContext('2d');
let w,h,particles=[];
function resize(){w=canvas.width=innerWidth;h=canvas.height=innerHeight;}
window.onresize=resize;resize();
for(let i=0;i<100;i++){
  particles.push({x:Math.random()*w,y:Math.random()*h,r:Math.random()*2+1,dx:(Math.random()-0.5)*0.8,dy:(Math.random()-0.5)*0.8});
}
function animate(){
  ctx.fillStyle='#030617';
  ctx.fillRect(0,0,w,h);
  ctx.strokeStyle='rgba(79,195,247,0.15)';
  particles.forEach(p=>{
    ctx.beginPath();
    ctx.arc(p.x,p.y,p.r,0,Math.PI*2);
    ctx.fillStyle='rgba(79,195,247,0.7)';
    ctx.fill();
    particles.forEach(p2=>{
      const dx=p.x-p2.x, dy=p.y-p2.y;
      const dist=Math.sqrt(dx*dx+dy*dy);
      if(dist<100){
        ctx.beginPath();
        ctx.moveTo(p.x,p.y);
        ctx.lineTo(p2.x,p2.y);
        ctx.stroke();
      }
    });
    p.x+=p.dx; p.y+=p.dy;
    if(p.x<0||p.x>w)p.dx*=-1;
    if(p.y<0||p.y>h)p.dy*=-1;
  });
  requestAnimationFrame(animate);
}
animate();

/* Sistema de telas */
const startScreen=document.getElementById('start-screen');
const levelScreen=document.getElementById('level-screen');
const quizScreen=document.getElementById('quiz-screen');
const questionText=document.getElementById('question-text');
const answersContainer=document.getElementById('answers');
const pointsDisplay=document.getElementById('points');
let currentLevel='easy',points=0,currentQuestion=0,questions=[];

function showLevels(){
  startScreen.classList.add('hidden');
  levelScreen.classList.remove('hidden');
}

/* Perguntas bíblicas difíceis */
const quizData={
easy:[
{q:'Quem construiu a arca?',a:['Noé','Moisés','Abraão','Jonas'],c:'Noé'},
{q:'Onde Jesus nasceu?',a:['Belém','Nazaré','Jerusalém','Egito'],c:'Belém'},
{q:'Quantos discípulos Jesus teve?',a:['10','12','7','40'],c:'12'},
{q:'Quem foi engolido por um grande peixe?',a:['Jonas','Daniel','José','Davi'],c:'Jonas'},
{q:'Quem matou Golias?',a:['Davi','Sansão','Saul','José'],c:'Davi'},
{q:'Quem era a mãe de Jesus?',a:['Maria','Ester','Rute','Débora'],c:'Maria'},
{q:'Qual foi o primeiro livro da Bíblia?',a:['Gênesis','Êxodo','Levítico','Salmos'],c:'Gênesis'},
{q:'Quem liderou os israelitas para fora do Egito?',a:['Moisés','Josué','Abraão','Elias'],c:'Moisés'},
{q:'Qual profeta desafiou os profetas de Baal?',a:['Elias','Isaías','Jeremias','Daniel'],c:'Elias'},
{q:'Quem foi traído por Judas?',a:['Jesus','Pedro','Paulo','Tomé'],c:'Jesus'},
{q:'Quem escreveu os Salmos?',a:['Davi','Moisés','Salomão','Isaías'],c:'Davi'}
],
medium:[
{q:'Quem interpretou os sonhos do faraó no Egito?',a:['José','Daniel','Moisés','Elias'],c:'José'},
{q:'Qual apóstolo duvidou da ressurreição?',a:['Pedro','Tomé','João','Tiago'],c:'Tomé'},
{q:'Quem escreveu a maioria das cartas do Novo Testamento?',a:['Paulo','Lucas','Pedro','Tiago'],c:'Paulo'},
{q:'Qual rei construiu o templo em Jerusalém?',a:['Salomão','Davi','Ezequias','Josias'],c:'Salomão'},
{q:'Quem foi lançado na cova dos leões?',a:['Daniel','Elias','Jonas','Moisés'],c:'Daniel'},
{q:'Qual discípulo negou Jesus três vezes?',a:['Pedro','João','Tiago','André'],c:'Pedro'},
{q:'Qual profeta anunciou o retorno do cativeiro?',a:['Ezequiel','Isaías','Jeremias','Daniel'],c:'Jeremias'},
{q:'Quem escreveu Apocalipse?',a:['João','Pedro','Paulo','Tiago'],c:'João'},
{q:'Qual foi o juiz mais forte de Israel?',a:['Sansão','Gideão','Débora','Elias'],c:'Sansão'},
{q:'Quem recebeu as tábuas da lei?',a:['Moisés','Josué','Abraão','Elias'],c:'Moisés'},
{q:'Quem foi chamado de amigo de Deus?',a:['Abraão','Moisés','Elias','Daniel'],c:'Abraão'},
{q:'Quem cuidou de Daniel na Babilônia?',a:['Sadraque','Mesaque','Abede-Nego','Aspásia'],c:'Sadraque'},
{q:'Quem salvou seu povo da morte no livro de Ester?',a:['Ester','Débora','Rute','Maria'],c:'Ester'},
{q:'Quem teve visão do vale de ossos secos?',a:['Ezequiel','Isaías','Jeremias','Daniel'],c:'Ezequiel'}
],
hard:[
{q:'Qual foi o primeiro milagre de Jesus?',a:['Transformar água em vinho','Curar o cego','Andar sobre as águas','Ressuscitar Lázaro'],c:'Transformar água em vinho'},
{q:'Quem subiu ao céu em um redemoinho?',a:['Elias','Eliseu','Isaías','Jeremias'],c:'Elias'},
{q:'Quem escreveu Hebreus?',a:['Paulo','Barnabé','Apolo','Moisés'],c:'Paulo'},
{q:'Qual apóstolo era médico?',a:['Lucas','Mateus','João','Paulo'],c:'Lucas'},
{q:'Quem traiu Jesus por 30 moedas?',a:['Judas','Pedro','Tomé','Mateus'],c:'Judas'},
{q:'Quem foi jogado na fornalha ardente?',a:['Sadraque','Daniel','Mesaque','Abede-Nego'],c:'Sadraque'},
{q:'Quem alimentou 5000 pessoas?',a:['Jesus','Pedro','Paulo','João'],c:'Jesus'},
{q:'Quem foi ressuscitado por Jesus?',a:['Lázaro','Jairo','Tabita','Eutico'],c:'Lázaro'},
{q:'Qual cidade foi destruída por fogo e enxofre?',a:['Sodoma','Nínive','Jerusalém','Babel'],c:'Sodoma'},
{q:'Quem curou Naamã, o sírio?',a:['Eliseu','Elias','Moisés','Daniel'],c:'Eliseu'},
{q:'Quem escreveu mais cartas do Novo Testamento?',a:['Paulo','Pedro','João','Tiago'],c:'Paulo'},
{q:'Quem duvidou da ressurreição de Jesus?',a:['Tomé','Pedro','João','Tiago'],c:'Tomé'},
{q:'Quem foi conhecido como amigo de Deus?',a:['Abraão','Moisés','Elias','Noé'],c:'Abraão'},
{q:'Qual profeta foi levado ao céu em carro de fogo?',a:['Elias','Eliseu','Isaías','Jeremias'],c:'Elias'},
{q:'Quem foi a mãe de Samuel?',a:['Ana','Sara','Débora','Ester'],c:'Ana'},
{q:'Quem matou o gigante Golias?',a:['Davi','Sansão','Saul','Elias'],c:'Davi'},
{q:'Quem previu o nascimento de Jesus?',a:['Isaías','Jeremias','Ezequiel','Daniel'],c:'Isaías'},
{q:'Quem ficou no ventre de Jonas por 3 dias?',a:['Jonas','Daniel','Elias','Moisés'],c:'Jonas'}
]
};

/* Função para embaralhar */
function shuffle(array){for(let i=array.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[array[i],array[j]]=[array[j],array[i]];}return array;}

function startQuiz(level){
  currentLevel=level;
  questions=[...quizData[level]];
  points=0;currentQuestion=0;
  levelScreen.classList.add('hidden');
  quizScreen.classList.remove('hidden');
  updatePoints();
  showQuestion();
}

function updatePoints(){
  pointsDisplay.textContent='PONTOS: '+points;
}

function showQuestion(){
  if(currentQuestion>=questions.length){
    alert('Fim do quiz! Pontuação final: '+points);
    quizScreen.classList.add('hidden');
    levelScreen.classList.remove('hidden');
    return;
  }
  const q=questions[currentQuestion];
  questionText.textContent=q.q;
  answersContainer.innerHTML='';
  let shuffledAnswers=shuffle([...q.a]);
  shuffledAnswers.forEach(ans=>{
    const btn=document.createElement('button');
    btn.textContent=ans;
    btn.onclick=()=>checkAnswer(btn,ans===q.c);
    answersContainer.appendChild(btn);
  });
}

function checkAnswer(btn,correct){
  const levelPoints={easy:20,medium:100,hard:250};
  if(correct){
    btn.style.background='#16a34a';
    btn.style.boxShadow='0 0 25px #16a34a,0 0 35px #4ade80';
    points+=levelPoints[currentLevel];
  }else{
    btn.style.background='#dc2626';
    btn.style.boxShadow='0 0 25px #dc2626,0 0 35px #f87171';
    points-=Math.floor(levelPoints[currentLevel]/2);
  }
  updatePoints();
  setTimeout(()=>{
    currentQuestion++;
    showQuestion();
  },700);
}
</script>
</body>
</html>
