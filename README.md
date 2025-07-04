# Senha-de-tra-os


<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Desbloqueio por padrão</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin-top: 40px;
      user-select: none;
    }
    #grid {
      width: 180px;
      height: 180px;
      margin: 0 auto;
      position: relative;
      display: grid;
      grid-template-columns: repeat(3, 60px);
      grid-template-rows: repeat(3, 60px);
      gap: 15px;
      touch-action: none;
    }
    .dot {
      width: 40px;
      height: 40px;
      border: 3px solid #333;
      border-radius: 50%;
      margin: auto;
      background-color: white;
      position: relative;
      z-index: 2;
      box-sizing: border-box;
      transition: background-color 0.2s, border-color 0.2s;
    }
    .dot.active {
      background-color: #4caf50;
      border-color: #4caf50;
    }
    #patternCode {
      margin-top: 30px;
      font-weight: bold;
      font-size: 18px;
      user-select: text;
    }
    #btnClear, #btnSubmit {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
      user-select: none;
    }
    /* SVG overlay for lines */
    #lines {
      position: absolute;
      top: 0;
      left: 0;
      width: 180px;
      height: 180px;
      pointer-events: none;
      z-index: 1;
    }
  </style>
</head>
<body>

<h1>Desenhe o padrão</h1>
<div id="grid">
  <svg id="lines"></svg>
  <div class="dot" data-id="1"></div>
  <div class="dot" data-id="2"></div>
  <div class="dot" data-id="3"></div>
  <div class="dot" data-id="4"></div>
  <div class="dot" data-id="5"></div>
  <div class="dot" data-id="6"></div>
  <div class="dot" data-id="7"></div>
  <div class="dot" data-id="8"></div>
  <div class="dot" data-id="9"></div>
</div>

<div id="patternCode">Padrão: </div>
<button id="btnClear">Limpar</button>
<button id="btnSubmit">Enviar</button>

<script>
  const dots = Array.from(document.querySelectorAll('.dot'));
  const patternCode = document.getElementById('patternCode');
  const btnClear = document.getElementById('btnClear');
  const btnSubmit = document.getElementById('btnSubmit');
  const svg = document.getElementById('lines');

  let pattern = [];
  let isDrawing = false;

  // Função para obter o centro do ponto relativo ao grid
  function getDotCenter(dot) {
    const rect = dot.getBoundingClientRect();
    const gridRect = dot.parentElement.getBoundingClientRect();
    return {
      x: rect.left + rect.width / 2 - gridRect.left,
      y: rect.top + rect.height / 2 - gridRect.top
    };
  }

  // Desenha linhas SVG entre os pontos ativos
  function drawLines() {
    // Limpa linhas anteriores
    svg.innerHTML = '';

    for (let i = 0; i < pattern.length - 1; i++) {
      const fromDot = dots.find(d => d.dataset.id === pattern[i]);
      const toDot = dots.find(d => d.dataset.id === pattern[i + 1]);
      if (fromDot && toDot) {
        const start = getDotCenter(fromDot);
        const end = getDotCenter(toDot);

        const line = document.createElementNS("http://www.w3.org/2000/svg", "line");
        line.setAttribute('x1', start.x);
        line.setAttribute('y1', start.y);
        line.setAttribute('x2', end.x);
        line.setAttribute('y2', end.y);
        line.setAttribute('stroke', '#4caf50');
        line.setAttribute('stroke-width', '4');
        line.setAttribute('stroke-linecap', 'round');

        svg.appendChild(line);
      }
    }
  }

  function activateDot(dot) {
    if (!pattern.includes(dot.dataset.id)) {
      pattern.push(dot.dataset.id);
      dot.classList.add('active');
      patternCode.textContent = 'Padrão: ' + pattern.join('-');
      drawLines();
    }
  }

  function clearPattern() {
    pattern = [];
    dots.forEach(dot => dot.classList.remove('active'));
    patternCode.textContent = 'Padrão: ';
    svg.innerHTML = '';
  }

  // Eventos mouse
  document.getElementById('grid').addEventListener('mousedown', e => {
    if (e.target.classList.contains('dot')) {
      isDrawing = true;
      activateDot(e.target);
    }
  });
  document.getElementById('grid').addEventListener('mouseover', e => {
    if (isDrawing && e.target.classList.contains('dot')) {
      activateDot(e.target);
    }
  });
  document.addEventListener('mouseup', () => {
    if (isDrawing) isDrawing = false;
  });

  // Eventos touch
  document.getElementById('grid').addEventListener('touchstart', e => {
    e.preventDefault();
    const touch = e.touches[0];
    const el = document.elementFromPoint(touch.clientX, touch.clientY);
    if (el && el.classList.contains('dot')) {
      isDrawing = true;
      activateDot(el);
    }
  }, { passive: false });
  document.getElementById('grid').addEventListener('touchmove', e => {
    e.preventDefault();
    const touch = e.touches[0];
    const el = document.elementFromPoint(touch.clientX, touch.clientY);
    if (isDrawing && el && el.classList.contains('dot')) {
      activateDot(el);
    }
  }, { passive: false });
  document.addEventListener('touchend', () => {
    if (isDrawing) isDrawing = false;
  });

  btnClear.addEventListener('click', clearPattern);

  btnSubmit.addEventListener('click', () => {
    if (pattern.length === 0) {
      alert('Desenhe um padrão antes de enviar!');
      return;
    }
    const patternStr = pattern.join('');
    window.location.href = `display.html?pattern=${patternStr}`;
  });
</script>

</body>
</html>

