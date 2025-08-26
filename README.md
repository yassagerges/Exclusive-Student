<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Security Console</title>
  <style>
    :root { --bg:#0b0f0c; --fg:#d3fbd8; --accent:#ff4d4d; --muted:#78c49b; }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;background:radial-gradient(1200px 600px at 30% 10%, #0f1612 10%, #0b0f0c 60%);
      color:var(--fg);font:15px/1.6 ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
      display:flex;align-items:center;justify-content:center;overflow:hidden;
    }
    .wrap{width:min(950px,95vw);height:min(560px,90vh);border:1px solid #173622;border-radius:14px;
      box-shadow:0 0 40px rgba(0,0,0,.6), inset 0 0 50px rgba(120,196,155,.08);
      position:relative;background:rgba(5,10,8,.6);backdrop-filter: blur(2px);}
    .topbar{display:flex;gap:10px;align-items:center;padding:12px 14px;border-bottom:1px solid #143a23}
    .led{width:10px;height:10px;border-radius:50%;}
    .r{background:#ff5f56}.y{background:#ffbd2e}.g{background:#27c93f}
    .title{margin-left:8px;color:#9fe0b3;letter-spacing:.08em;font-weight:700}
    .screen{padding:14px; height:calc(100% - 52px); overflow:auto}
    .line{white-space:pre-wrap; word-break:break-word}
    .muted{color:#77c49a99}
    .warn{color:var(--accent);font-weight:700}
    .ok{color:#88ffb3}
    .blink{animation:blink 1s steps(1,end) infinite}
    @keyframes blink{50%{opacity:.2}}
    canvas#rain{position:absolute;inset:0;opacity:.08;pointer-events:none}
    .reveal{
      position:absolute;inset:auto 14px 14px 14px;background:#08110c; border:1px solid #1b5c3a;
      padding:12px;border-radius:10px;color:#b8ffd1;display:none
    }
    .reveal strong{color:#fff}
    .btn{
      display:inline-block;margin-top:10px;padding:8px 14px;border:1px solid #2c7a51;border-radius:8px;
      color:#caffd9;text-decoration:none;cursor:pointer;background:#0e1a14
    }
    .btn:hover{filter:brightness(1.2)}

    /* Progress bar */
    .progress {
      width: 100%; 
      height: 14px; 
      background:#122018; 
      border:1px solid #1b5c3a; 
      border-radius:6px; 
      margin:6px 0;
      overflow:hidden;
    }
    .progress .bar {
      height:100%;
      width:0%;
      background:linear-gradient(90deg,#3aff91,#9fe0b3);
      transition:width 0.3s;
    }
  </style>
</head>
<body>
  <canvas id="rain"></canvas>
  <div class="wrap">
    <div class="topbar">
      <span class="led r"></span><span class="led y"></span><span class="led g"></span>
      <span class="title">/sec/ops › live-console</span>
    </div>
    <div class="screen" id="screen" aria-live="polite"></div>

    <div class="reveal" id="reveal">
      <div><strong>Relax 😄 — This is a safe demo/prank.</strong> No data was collected; everything above is simulated UI to show off cybersecurity vibes.</div>
      <a class="btn" href="#" onclick="this.parentElement.style.display='none';return false;">Hide</a>
    </div>
  </div>

  <script>
    // Matrix rain background
    (function(){
      const c = document.getElementById('rain'), ctx = c.getContext('2d');
      function resize(){ c.width = window.innerWidth; c.height = window.innerHeight }
      resize(); addEventListener('resize', resize);
      const letters = '01ABCDEFGHJKLMNPQRSTUVWXYZ#$%&*';
      const fontSize = 16;
      let columns = Math.floor(c.width / fontSize);
      const drops = Array(columns).fill(1);
      function draw(){
        ctx.fillStyle = 'rgba(0,0,0,0.07)'; ctx.fillRect(0,0,c.width,c.height);
        ctx.fillStyle = '#9FE0B3';
        ctx.font = fontSize+'px monospace';
        for(let i=0;i<drops.length;i++){
          const text = letters.charAt(Math.floor(Math.random()*letters.length));
          ctx.fillText(text, i*fontSize, drops[i]*fontSize);
          if(drops[i]*fontSize > c.height && Math.random() > 0.975) drops[i]=0;
          drops[i]++;
        }
        requestAnimationFrame(draw);
      }
      draw();
    })();

    // Console output simulation
    const screen = document.getElementById('screen');

    const lines = [
      ['▶ Initializing secure session…', 'muted', 600],
      ['OK  ', 'ok', 250],
      ['▶ Scanning interfaces (eth0, wlan0)…', 'muted', 500],
      ['eth0 10.0.0.12/24  ▶ link up', 'ok', 120],
      ['wlan0 192.168.1.7/24 ▶ link up', 'ok', 120],
      ['▶ Loading threat intel feeds…', 'muted', 500],
      ['feeds: 12 sources, 48,213 indicators', 'ok', 220],

      // تحميل Package
      ['▶ Downloading incident-response.pkg (24MB)…', 'muted', 500],
      ['__PROGRESS__', 'progress', 0],
      ['download complete — verifying signature', 'muted', 500],
      ['signature: valid (SHA256)', 'ok', 300],

      // سحب البيانات
      ['▶ Fetching live telemetry data…', 'muted', 500],
      ['__PROGRESS__', 'progress', 0],
      ['stream: complete — 12,408 records ingested', 'ok', 400],

      ['▶ Correlating events…', 'muted', 450],
      ['ALERT  T1059.003 suspicious command execution', 'warn', 250],
      ['ALERT  T1021 lateral movement attempt', 'warn', 250],
      ['▶ Launching containment routine…', 'muted', 400],
      ['isolation: host-7  [DONE]', 'ok', 200],
      ['blocking IOC: 45.88.**.** via edge-fw', 'ok', 200],
      ['▶ Verifying integrity…', 'muted', 300],
      ['checksum: /usr/bin/ssh — clean', 'ok', 200],
      ['checksum: /etc/hosts — modified', 'warn', 300],
      ['▶ Restoring baseline snapshot…', 'muted', 500],
      ['restore: volume0 ✓   services: restarted ✓', 'ok', 260],
      ['⚠️ INCIDENT HANDLED — user action required', 'warn', 700],
      ['Prompt: press ANY key to acknowledge _', 'muted blink', 0]
    ];

    function append(text, cls='muted'){
      // Progress bar special case
      if (cls === 'progress' && text === '__PROGRESS__'){
        const wrap = document.createElement('div');
        wrap.className = 'progress';
        const bar = document.createElement('div');
        bar.className = 'bar';
        wrap.appendChild(bar);
        screen.appendChild(wrap);
        screen.scrollTop = screen.scrollHeight;

        // animate bar
        let p = 0;
        const timer = setInterval(()=>{
          p += Math.floor(Math.random()*15)+5; 
          if(p >= 100){ p=100; clearInterval(timer); }
          bar.style.width = p+"%";
        }, 400);
        return;
      }

      // Normal line
      const div = document.createElement('div');
      div.className = 'line '+cls;
      div.textContent = text;
      screen.appendChild(div);
      screen.scrollTop = screen.scrollHeight;
    }

    (async function play(){
      for (const [txt, cls, wait] of lines){
        append(txt, cls);
        await new Promise(r=>setTimeout(r, wait));
      }
      setTimeout(()=>document.getElementById('reveal').style.display='block', 1200);
    })();

    addEventListener('keydown', ()=> {
      const acks = document.querySelectorAll('.blink');
      acks.forEach(e => e.classList.remove('blink'));
      append('ACK received — logging out…', 'ok');
    });
  </script>
</body>
</html>

