<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<title>AI Video Player</title>
<style>
body {
  background: #0f172a;
  color: white;
  font-family: Arial, sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  width: 700px;
  text-align: center;
}

canvas {
  background: black;
  margin-top: 15px;
  border-radius: 10px;
}

.subtitle {
  margin-top: 10px;
  font-size: 18px;
  color: #00ffcc;
}
</style>
</head>
<body>

<div class="container">
  <h1>🤖 AI Generated Video</h1>
  <canvas id="canvas" width="640" height="360"></canvas>
  <div class="subtitle" id="subtitle"></div>
</div>

<script>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
const subtitle = document.getElementById("subtitle");

const text =
"Selamat datang. Ini adalah contoh video AI yang dibuat menggunakan HTML, JavaScript, dan suara otomatis.";

let x = canvas.width;
let frame = 0;

// AI VOICE
function speakAI(text){
  const u = new SpeechSynthesisUtterance(text);
  u.lang = "id-ID";
  u.rate = 0.95;
  u.pitch = 1;
  speechSynthesis.speak(u);
}

// VIDEO ANIMATION
function playVideo(){
  subtitle.innerText = text;

  const anim = setInterval(() => {
    ctx.fillStyle = "black";
    ctx.fillRect(0,0,canvas.width,canvas.height);

    ctx.fillStyle = "white";
    ctx.font = "32px Arial";
    ctx.textAlign = "center";
    ctx.fillText("AI VIDEO DEMO", canvas.width/2, 80);

    ctx.font = "24px Arial";
    ctx.fillText(text, x, canvas.height/2);

    x -= 2;
    frame++;

    if(frame > 300){
      clearInterval(anim);
    }
  }, 33);
}

// AUTO PLAY
setTimeout(() => {
  speakAI(text);
  playVideo();
}, 500);
</script>

</body>
</html>
