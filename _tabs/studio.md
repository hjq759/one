---
layout: page
title: 灵感空间
icon: fas fa-wand-magic-sparkles
order: 6
---
<div class="studio-shell">
  <header class="studio-intro">
    <span class="studio-kicker">H J Q — AFTER HOURS</span>
    <h1>给思考留一段<br><em>柔软的空白。</em></h1>
    <p>这是小站的灵感空间：一段可播放的氛围音，一次专注练习，还有正在进行中的实验。</p>
  </header>

  <section id="sound" class="studio-panel sound-panel">
    <div class="panel-heading"><span>01 / AMBIENT RADIO</span><h2>夜间电台</h2></div>
    <div class="sound-layout">
      <div class="album-art" aria-hidden="true"><i class="fas fa-moon"></i><div class="art-ring"></div></div>
      <div class="sound-copy">
        <p class="now-playing"><span class="pulse"></span> LIVE GENERATIVE SOUND</p>
        <h3>Midnight Signals</h3>
        <p>一段由网页实时生成的低频氛围音。戴上耳机，适合阅读、写作与深夜思考。</p>
        <div class="sound-controls">
          <button id="soundToggle" class="studio-btn"><i class="fas fa-play"></i><span>播放氛围音</span></button>
          <label class="volume-control">音量 <input id="soundVolume" type="range" min="0" max="100" value="35"></label>
        </div>
      </div>
      <div class="equalizer" aria-hidden="true"><i></i><i></i><i></i><i></i><i></i><i></i><i></i></div>
    </div>
  </section>

  <section id="focus" class="studio-panel focus-panel">
    <div class="panel-heading"><span>02 / FOCUS MODE</span><h2>专注时刻</h2></div>
    <div class="focus-layout">
      <div class="timer-circle"><span id="timerDisplay">25:00</span><small>FOCUS SESSION</small></div>
      <div class="timer-copy"><h3>留 25 分钟给一件事</h3><p>从一个小目标开始。计时不会上传或记录你的任何信息。</p><div class="timer-actions"><button id="timerToggle" class="studio-btn">开始专注</button><button id="timerReset" class="studio-outline">重置</button></div></div>
    </div>
  </section>

  <section class="studio-panel explore-panel">
    <div class="panel-heading"><span>03 / EXPLORE</span><h2>实验进行时</h2></div>
    <a href="{{ '/mlp-flight/' | relative_url }}" class="experiment-link"><span><i class="fas fa-cube"></i></span><div><small>INTERACTIVE PROJECT</small><h3>外卖议事厅：用飞行游戏理解 MLP</h3><p>从输入到输出，沿着一次神经网络计算飞行。</p></div><i class="fas fa-arrow-up-right-from-square"></i></a>
  </section>
</div>
<script>
(() => {
  let context, master, playing = false, oscillators = [], timer, seconds = 1500, running = false;
  const soundToggle = document.getElementById('soundToggle'), soundVolume = document.getElementById('soundVolume');
  const makeSound = () => {
    context = new (window.AudioContext || window.webkitAudioContext)();
    master = context.createGain(); master.gain.value = soundVolume.value / 1000; master.connect(context.destination);
    const notes = [110, 164.81, 220];
    notes.forEach((freq, i) => {
      const osc = context.createOscillator(), gain = context.createGain(), filter = context.createBiquadFilter();
      osc.type = i === 1 ? 'sine' : 'triangle'; osc.frequency.value = freq; filter.type = 'lowpass'; filter.frequency.value = 650;
      gain.gain.value = .13 / (i + 1); osc.connect(filter).connect(gain).connect(master); osc.start(); oscillators.push(osc);
    });
  };
  soundToggle.addEventListener('click', async () => {
    if (!playing) { if (!context) makeSound(); await context.resume(); playing = true; soundToggle.innerHTML = '<i class="fas fa-pause"></i><span>暂停氛围音</span>'; document.querySelector('.sound-panel').classList.add('is-playing'); }
    else { await context.suspend(); playing = false; soundToggle.innerHTML = '<i class="fas fa-play"></i><span>继续播放</span>'; document.querySelector('.sound-panel').classList.remove('is-playing'); }
  });
  soundVolume.addEventListener('input', () => { if (master) master.gain.value = soundVolume.value / 1000; });
  const display = document.getElementById('timerDisplay'), timerToggle = document.getElementById('timerToggle');
  const draw = () => { display.textContent = String(Math.floor(seconds / 60)).padStart(2, '0') + ':' + String(seconds % 60).padStart(2, '0'); };
  timerToggle.addEventListener('click', () => { running = !running; timerToggle.textContent = running ? '暂停专注' : '继续专注'; if (running) timer = setInterval(() => { if (seconds > 0) { seconds--; draw(); } else { clearInterval(timer); running = false; timerToggle.textContent = '完成一轮'; } }, 1000); else clearInterval(timer); });
  document.getElementById('timerReset').addEventListener('click', () => { clearInterval(timer); running = false; seconds = 1500; draw(); timerToggle.textContent = '开始专注'; });
})();
</script>
