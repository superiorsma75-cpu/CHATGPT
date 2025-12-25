text-to-video-ultimate/
├── index.html
├── style.css
├── script.js
└── ffmpeg.min.js
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Text to Video PRO+</title>
<link rel="stylesheet" href="style.css">
<script src="https://unpkg.com/@ffmpeg/ffmpeg@0.12.6/dist/ffmpeg.min.js"></script>
</head>
<body>

<div class="app">
<h1>🎬 Text to Video PRO+</h1>

<textarea id="textInput" placeholder="Masukkan teks..."></textarea>

<button onclick="generate()">Buat Video MP4 + Subtitle</button>

<canvas id="canvas" width="640" height="360"></canvas>

<p id="status"></p>
<a id="download" download="video.mp4">⬇ Download MP4</a>
</div>

<script src="script.js"></script>
</body>
</html>
const { createFFmpeg, fetchFile } = FFmpeg;
const ffmpeg = createFFmpeg({ log: true });

const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
const status = document.getElementById("status");
const download = document.getElementById("download");

async function generate(){
    const text = textInput.value;
    speak(text);

    status.innerText = "🎥 Merekam video...";
    const stream = canvas.captureStream(30);
    const recorder = new MediaRecorder(stream);
    let chunks = [];

    recorder.ondataavailable = e => chunks.push(e.data);
    recorder.start();

    let x = canvas.width;
    let frames = 0;

    const anim = setInterval(()=>{
        ctx.fillStyle="black";
        ctx.fillRect(0,0,canvas.width,canvas.height);
        ctx.fillStyle="white";
        ctx.font="30px Arial";
        ctx.fillText(text,x,canvas.height/2);
        x -= 2;
        frames++;

        if(frames>300){
            clearInterval(anim);
            recorder.stop();
        }
    },33);

    recorder.onstop = async ()=>{
        status.innerText = "⚙️ Konversi ke MP4...";
        const webmBlob = new Blob(chunks,{type:"video/webm"});

        // Subtitle otomatis
        const srt = generateSRT(text);

        if(!ffmpeg.isLoaded()) await ffmpeg.load();

        ffmpeg.FS('writeFile','video.webm',await fetchFile(webmBlob));
        ffmpeg.FS('writeFile','sub.srt',new TextEncoder().encode(srt));

        await ffmpeg.run(
            '-i','video.webm',
            '-vf','subtitles=sub.srt',
            '-c:v','libx264',
            '-pix_fmt','yuv420p',
            'output.mp4'
        );

        const mp4 = ffmpeg.FS('readFile','output.mp4');
        const mp4Blob = new Blob([mp4.buffer],{type:'video/mp4'});

        download.href = URL.createObjectURL(mp4Blob);
        download.style.display="block";
        status.innerText="✅ Video MP4 siap!";
    };
}

function generateSRT(text){
    return `1
00:00:00,000 --> 00:00:10,000
${text}
`;
}

function speak(text){
    const u = new SpeechSynthesisUtterance(text);
    u.lang = "id-ID";
    speechSynthesis.speak(u);
}
