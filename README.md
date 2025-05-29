# saude
mais saúde<!DOCTYPE html>
<html>
<head>
  <title>Vida Saudável - Missão com Hospital e NPCs</title>
  <style>
    body { margin: 0; overflow: hidden; background: #cceeff; }
    canvas { background: #88cc88; display: block; margin: 0 auto; }
  </style>
</head>
<body>
  <canvas id="game" width="800" height="600"></canvas>
  <script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');

    const player = {
      x: 100,
      y: 100,
      size: 32,
      color: 'blue',
      speed: 3,
      saude: 100,
      pontos: 0
    };

    const feira = { x: 600, y: 400, size: 64, color: 'orange' };
    const hospital = { x: 50, y: 500, size: 80, color: 'red' };
    const comidaRuim = { x: 300, y: 250, size: 40, color: 'brown', ativa: true };
    const comidaBoa = { x: 700, y: 100, size: 40, color: 'yellow', ativa: true };
    const npc = { x: 400, y: 100, size: 40, color: 'purple' };

    let keys = {};
    let missãoCompleta = false;
    let showNpcMessage = false;
    let currentPhase = 1;

    document.addEventListener('keydown', e => keys[e.key] = true);
    document.addEventListener('keyup', e => keys[e.key] = false);

    function colisao(a, b) {
      return (
        a.x < b.x + b.size &&
        a.x + a.size > b.x &&
        a.y < b.y + b.size &&
        a.y + a.size > b.y
      );
    }

    function update() {
      if (keys['ArrowUp'] || keys['w']) player.y -= player.speed;
      if (keys['ArrowDown'] || keys['s']) player.y += player.speed;
      if (keys['ArrowLeft'] || keys['a']) player.x -= player.speed;
      if (keys['ArrowRight'] || keys['d']) player.x += player.speed;

      if (!missãoCompleta && colisao(player, feira)) {
        missãoCompleta = true;
        alert("Missão completa! Você comprou frutas saudáveis. 🌽🍎");
        nextPhase();
      }

      if (comidaRuim.ativa && colisao(player, comidaRuim)) {
        player.saude -= 30;
        comidaRuim.ativa = false;
        alert("Você comeu fast-food estragado 😵. Vá ao hospital!");
      }

      if (comidaBoa.ativa && colisao(player, comidaBoa)) {
        player.saude += 20;
        player.pontos += 10;
        comidaBoa.ativa = false;
        alert("Você comeu uma banana! +10 pontos 🍌");
      }

      if (colisao(player, hospital) && player.saude < 100) {
        player.saude = 100;
        alert("Você foi curado no hospital 🏥");
      }

      if (colisao(player, npc)) {
        showNpcMessage = true;
      } else {
        showNpcMessage = false;
      }
    }

    function nextPhase() {
      currentPhase++;
      missãoCompleta = false;
      comidaBoa.ativa = true;
      comidaRuim.ativa = true;
      player.x = 100;
      player.y = 100;
      alert("Nova fase desbloqueada! Coma bem para continuar saudável 🍇🥦");
    }

    function drawBar(x, y, width, height, value, max, color) {
      ctx.fillStyle = 'gray';
      ctx.fillRect(x, y, width, height);
      ctx.fillStyle = color;
      ctx.fillRect(x, y, (value / max) * width, height);
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = 'green';
      ctx.fillRect(0, 0, canvas.width, 200);

      ctx.fillStyle = feira.color;
      ctx.fillRect(feira.x, feira.y, feira.size, feira.size);

      ctx.fillStyle = hospital.color;
      ctx.fillRect(hospital.x, hospital.y, hospital.size, hospital.size);

      if (comidaRuim.ativa) {
        ctx.fillStyle = comidaRuim.color;
        ctx.fillRect(comidaRuim.x, comidaRuim.y, comidaRuim.size, comidaRuim.size);
      }

      if (comidaBoa.ativa) {
        ctx.fillStyle = comidaBoa.color;
        ctx.fillRect(comidaBoa.x, comidaBoa.y, comidaBoa.size, comidaBoa.size);
      }

      ctx.fillStyle = npc.color;
      ctx.fillRect(npc.x, npc.y, npc.size, npc.size);

      ctx.fillStyle = player.color;
      ctx.fillRect(player.x, player.y, player.size, player.size);

      ctx.fillStyle = 'black';
      ctx.font = '18px Arial';
      ctx.fillText('Fase: ' + currentPhase + ' | Missão: Comer bem 🍎', 10, 25);
      ctx.fillText('Pontos: ' + player.pontos, 10, 50);

      drawBar(10, 70, 150, 15, player.saude, 100, 'red');
      ctx.strokeStyle = 'black';
      ctx.strokeRect(10, 70, 150, 15);
      ctx.fillText('Saúde', 170, 82);

      if (showNpcMessage) {
        ctx.fillStyle = 'white';
        ctx.fillRect(300, 400, 200, 80);
        ctx.strokeStyle = 'black';
        ctx.strokeRect(300, 400, 200, 80);
        ctx.fillStyle = 'black';
        ctx.font = '14px Arial';
        ctx.fillText('NPC: Coma frutas e legumes', 310, 420);
        ctx.fillText('todos os dias! 🍊🥕', 310, 440);
      }
    }

    function gameLoop() {
      update();
      draw();
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>

