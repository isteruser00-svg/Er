<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>Özür Dilerim ❤️</title>

<style>
*{
  margin:0;
  padding:0;
  box-sizing:border-box;
}

html,body{
  width:100%;
  height:100%;
  overflow:hidden;
  background:#030305;
  font-family:Arial,sans-serif;
}

canvas{
  position:fixed;
  inset:0;
  width:100%;
  height:100%;
  touch-action:manipulation;
}

#text{
  position:fixed;
  left:50%;
  top:76%;
  transform:translate(-50%,-50%);
  width:90%;
  text-align:center;
  color:white;
  opacity:0;
  transition:opacity 1.8s ease;
  pointer-events:none;
}

#text h1{
  font-size:clamp(32px,9vw,62px);
  text-shadow:
    0 0 8px #ff4268,
    0 0 25px #ff174d,
    0 0 50px rgba(255,23,77,.5);
}

#text p{
  margin-top:14px;
  font-size:clamp(17px,4.5vw,26px);
  color:#eee;
}

#hint{
  position:fixed;
  bottom:7%;
  left:50%;
  transform:translateX(-50%);
  color:rgba(255,255,255,.55);
  font-size:15px;
  transition:opacity 1s;
}
</style>
</head>

<body>

<canvas id="canvas"></canvas>

<div id="text">
  <h1>Özür dilerim ❤️</h1>
  <p>Seni kırmak istememiştim.</p>
</div>

<div id="hint">Kalbe dokun...</div>

<script>

const canvas=document.getElementById("canvas");
const ctx=canvas.getContext("2d");

let W,H;
let started=false;
let amount=0;

function resize(){
  W=canvas.width=innerWidth;
  H=canvas.height=innerHeight;
}

addEventListener("resize",resize);
resize();


/* GERÇEK DOLU KALP */

function heartPath(x,y,s){

  ctx.beginPath();

  ctx.moveTo(x,y+s*0.85);

  ctx.bezierCurveTo(
    x-s*1.35,y-s*0.05,
    x-s*1.25,y-s*1.05,
    x-s*0.55,y-s*1.05
  );

  ctx.bezierCurveTo(
    x-s*0.15,y-s*1.05,
    x,y-s*0.72,
    x,y-s*0.42
  );

  ctx.bezierCurveTo(
    x,y-s*0.72,
    x+s*0.15,y-s*1.05,
    x+s*0.55,y-s*1.05
  );

  ctx.bezierCurveTo(
    x+s*1.25,y-s*1.05,
    x+s*1.35,y-s*0.05,
    x,y+s*0.85
  );

  ctx.closePath();
}


/* PARLAK KALP */

function drawHeart(x,y,s,alpha=1){

  ctx.save();

  ctx.globalAlpha=alpha;

  /* dış parlama */

  ctx.shadowColor="#ff174d";
  ctx.shadowBlur=35;

  const gradient=ctx.createLinearGradient(
    x-s,
    y-s,
    x+s,
    y+s
  );

  gradient.addColorStop(0,"#ff416c");
  gradient.addColorStop(.45,"#ff174d");
  gradient.addColorStop(1,"#b9003d");

  ctx.fillStyle=gradient;

  heartPath(x,y,s);
  ctx.fill();


  /* üstteki parlaklık */

  ctx.shadowBlur=0;

  const shine=ctx.createRadialGradient(
    x-s*.35,
    y-s*.65,
    2,
    x,
    y,
    s*1.5
  );

  shine.addColorStop(
    0,
    "rgba(255,255,255,.45)"
  );

  shine.addColorStop(
    .25,
    "rgba(255,150,170,.18)"
  );

  shine.addColorStop(
    1,
    "rgba(255,255,255,0)"
  );

  ctx.fillStyle=shine;

  heartPath(x,y,s);
  ctx.fill();

  ctx.restore();
}


/* BAŞLANGIÇTA KIRIK KALP */

function drawBroken(){

  const s=Math.min(W,H)*.18;

  const x=W/2;
  const y=H*.43;

  const gap=(1-amount)*35;

  /*
    Sol taraf
  */

  ctx.save();

  ctx.beginPath();

  ctx.rect(
    0,
    0,
    x-gap,
    H
  );

  ctx.clip();

  drawHeart(
    x-gap,
    y,
    s,
    1
  );

  ctx.restore();


  /*
    Sağ taraf
  */

  ctx.save();

  ctx.beginPath();

  ctx.rect(
    x+gap,
    0,
    W-x-gap,
    H
  );

  ctx.clip();

  drawHeart(
    x+gap,
    y,
    s,
    1
  );

  ctx.restore();


  /*
    Kırık hattı
  */

  if(amount<.85){

    ctx.save();

    ctx.strokeStyle=
      "rgba(20,0,8,.9)";

    ctx.lineWidth=5;
    ctx.lineCap="round";

    ctx.beginPath();

    ctx.moveTo(x,y-s*.45);

    ctx.lineTo(
      x-8,
      y-s*.05
    );

    ctx.lineTo(
      x+8,
      y+s*.18
    );

    ctx.lineTo(
      x-6,
      y+s*.45
    );

    ctx.stroke();

    ctx.restore();
  }
}


/* ANİMASYON */

function animate(){

  ctx.fillStyle="#030305";

  ctx.fillRect(
    0,
    0,
    W,
    H
  );

  if(!started){

    drawBroken();

  }else{

    amount+=(1-amount)*.045;

    drawBroken();

    if(amount>.96){

      const pulse=
        1+
        Math.sin(Date.now()*.004)*.025;

      const s=
        Math.min(W,H)*.18;

      drawHeart(
        W/2,
        H*.43,
        s*pulse,
        1
      );
    }
  }

  requestAnimationFrame(animate);
}

animate();


/* DOKUNUNCA BİRLEŞ */

canvas.addEventListener(
  "click",
  ()=>{

    if(started)return;

    started=true;

    document.getElementById(
      "hint"
    ).style.opacity="0";

    setTimeout(()=>{

      document.getElementById(
        "text"
      ).style.opacity="1";

    },2500);

  }
);

</script>

</body>
</html>
