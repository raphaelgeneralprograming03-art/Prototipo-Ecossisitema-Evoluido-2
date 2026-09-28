
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Simulação Kardashev Nível 2 - Civilização Estelar</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background-color: #020408;
      font-family: 'Courier New', Courier, monospace;
    }
    canvas {
      display: block;
    }
    #hud {
      position: absolute;
      top: 20px;
      left: 20px;
      color: #00ffcc;
      background: rgba(2, 8, 20, 0.85);
      padding: 18px 22px;
      border: 1px solid #00ffcc;
      border-radius: 6px;
      box-shadow: 0 0 25px rgba(0, 255, 204, 0.25);
      pointer-events: none;
      backdrop-filter: blur(5px);
    }
    h1 {
      margin: 0 0 8px 0;
      font-size: 16px;
      text-transform: uppercase;
      letter-spacing: 2px;
      color: #ffffff;
      text-shadow: 0 0 10px #00ffcc;
    }
    .metric {
      margin: 5px 0;
      font-size: 11px;
      color: #88ffcc;
    }
    .val {
      color: #ffffff;
      font-weight: bold;
    }
  </style>
</head>
<body>

  <div id="hud">
    <h1>Kardashev Nível II: Estelar</h1>
    <div class="metric">⚡ Potência Estelar Coletada: <span class="val" id="power">3.828 × 10²⁶ W</span></div>
    <div class="metric">🛰️ Coletores Dyson Ativos: <span class="val">14,200 Nodos</span></div>
    <div class="metric">🪐 Megasestruturas Habitats: <span class="val">Cilindros O'Neill / Anéis</span></div>
    <div class="metric">🚀 Frotas Interestelares: <span class="val">Hiperpropulsão Ativa</span></div>
  </div>

  <canvas id="spaceCanvas"></canvas>

  <script>
    const canvas = document.getElementById('spaceCanvas');
    const ctx = canvas.getContext('2d');

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }
    window.addEventListener('resize', resize);
    resize();

    // Centro do sistema (Estrela)
    let cx = canvas.width / 2;
    let cy = canvas.height / 2;

    window.addEventListener('resize', () => {
      cx = canvas.width / 2;
      cy = canvas.height / 2;
    });

    // Fundo Estelar Profundo (Paralaxe de Estrelas)
    const backgroundStars = Array.from({ length: 300 }, () => ({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      radius: Math.random() * 1.5,
      alpha: Math.random()
    }));

    // Coletores do Enxame de Dyson (Órbitas ao redor da estrela)
    const dysonNodes = Array.from({ length: 60 }, (_, i) => ({
      angle: (i / 60) * Math.PI * 2,
      distance: 180 + Math.random() * 80,
      speed: 0.003 + (Math.random() * 0.002),
      size: 4 + Math.random() * 3
    }));

    // Habitats Orbitais / Cilindros de O'Neill
    const habitats = Array.from({ length: 8 }, (_, i) => ({
      angle: (i / 8) * Math.PI * 2,
      distance: 360,
      speed: 0.0015,
      width: 18,
      height: 8
    }));

    // Frotas de Transporte Interestelar e Naves de Reconhecimento
    const ships = Array.from({ length: 12 }, () => ({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      targetX: Math.random() * canvas.width,
      targetY: Math.random() * canvas.height,
      speed: 1.5 + Math.random() * 2,
      trail: []
    }));

    let starPulse = 0;

    function animate() {
      // Limpeza de quadro com efeito de rastro suave
      ctx.fillStyle = 'rgba(2, 4, 8, 0.3)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // 1. Desenhar Fundo Estelar
      backgroundStars.forEach(star => {
        ctx.fillStyle = `rgba(255, 255, 255, ${star.alpha})`;
        ctx.beginPath();
        ctx.arc(star.x, star.y, star.radius, 0, Math.PI * 2);
        ctx.fill();
      });

      // 2. A Estrela Central (Fonte de Energia de Nível 2)
      starPulse += 0.05;
      const currentStarRadius = 90 + Math.sin(starPulse) * 3;

      // Gradiente Corona da Estrela
      const sunGradient = ctx.createRadialGradient(cx, cy, 10, cx, cy, currentStarRadius * 1.8);
      sunGradient.addColorStop(0, '#ffffff');
      sunGradient.addColorStop(0.3, '#ffaa00');
      sunGradient.addColorStop(0.7, '#ff3300');
      sunGradient.addColorStop(1, 'rgba(255, 50, 0, 0)');

      ctx.fillStyle = sunGradient;
      ctx.beginPath();
      ctx.arc(cx, cy, currentStarRadius * 1.8, 0, Math.PI * 2);
      ctx.fill();

      // Núcleo da Estrela
      ctx.fillStyle = '#ffff55';
      ctx.beginPath();
      ctx.arc(cx, cy, currentStarRadius, 0, Math.PI * 2);
      ctx.fill();

      // 3. Enxame de Dyson (Coletores e Feixes de Energia)
      ctx.strokeStyle = 'rgba(0, 255, 204, 0.15)';
      ctx.lineWidth = 1;
      
      dysonNodes.forEach(node => {
        node.angle += node.speed;
        const nx = cx + Math.cos(node.angle) * node.distance;
        const ny = cy + Math.sin(node.angle) * node.distance;

        // Feixe de transferência de energia para o centro
        ctx.beginPath();
        ctx.moveTo(nx, ny);
        ctx.lineTo(cx, cy);
        ctx.stroke();

        // Coletor Individual
        ctx.fillStyle = '#00ffcc';
        ctx.shadowColor = '#00ffcc';
        ctx.shadowBlur = 12;
        ctx.beginPath();
        ctx.arc(nx, ny, node.size, 0, Math.PI * 2);
        ctx.fill();
        ctx.shadowBlur = 0; // Reset
      });

      // 4. Órbitas e Habitats de O'Neill (Cidades Espaciais)
      ctx.strokeStyle = 'rgba(0, 150, 255, 0.25)';
      ctx.beginPath();
      ctx.arc(cx, cy, 360, 0, Math.PI * 2);
      ctx.stroke();

      habitats.forEach(hab => {
        hab.angle += hab.speed;
        const hx = cx + Math.cos(hab.angle) * hab.distance;
        const hy = cy + Math.sin(hab.angle) * hab.distance;

        ctx.save();
        ctx.translate(hx, hy);
        ctx.rotate(hab.angle + Math.PI / 2);
        
        ctx.fillStyle = '#ffffff';
        ctx.shadowColor = '#00aaff';
        ctx.shadowBlur = 10;
        ctx.fillRect(-hab.width / 2, -hab.height / 2, hab.width, hab.height);
        ctx.restore();
      });

      // 5. Frotas Interestelares com Linhas de Salto
      ships.forEach(ship => {
        const dx = ship.targetX - ship.x;
        const dy = ship.targetY - ship.y;
        const dist = Math.hypot(dx, dy);

        if (dist < 5) {
          ship.targetX = Math.random() * canvas.width;
          ship.targetY = Math.random() * canvas.height;
        } else {
          ship.x += (dx / dist) * ship.speed;
          ship.y += (dy / dist) * ship.speed;
        }

        // Armazenar rastro
        ship.trail.push({ x: ship.x, y: ship.y });
        if (ship.trail.length > 15) ship.trail.shift();

        // Desenhar rastro de plasma da nave
        ctx.strokeStyle = 'rgba(255, 0, 255, 0.4)';
        ctx.lineWidth = 1.5;
        ctx.beginPath();
        ship.trail.forEach((t, idx) => {
          if (idx === 0) ctx.moveTo(t.x, t.y);
          else ctx.lineTo(t.x, t.y);
        });
        ctx.stroke();

        // Desenhar Nave
        ctx.fillStyle = '#ff00ff';
        ctx.shadowColor = '#ff00ff';
        ctx.shadowBlur = 8;
        ctx.beginPath();
        ctx.arc(ship.x, ship.y, 2.5, 0, Math.PI * 2);
        ctx.fill();
        ctx.shadowBlur = 0;
      });

      requestAnimationFrame(animate);
    }

    animate();
  </script>
</body>
</html>
