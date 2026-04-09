<!DOCTYPE html><html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <title>Jogo da Esfera Mobile</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body { margin: 0; background: black; overflow: hidden; }
    canvas { display: block; margin: auto; background: #111; touch-action: none; }
  </style>
</head>
<body>
<canvas id="gameCanvas"></canvas><script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

let player = {
  x: 50,
  y: canvas.height / 2,
  radius: 20
};

let bullets = [];
let enemies = [];

// Controle por toque
canvas.addEventListener("touchmove", e => {
  let touch = e.touches[0];
  player.y = touch.clientY;
});

canvas.addEventListener("touchstart", () => {
  bullets.push({ x: player.x + 20, y: player.y });
});

function spawnEnemy() {
  enemies.push({
    x: canvas.width,
    y: Math.random() * canvas.height,
    radius: 20,
    speed: 3
  });
}

setInterval(spawnEnemy, 800);

function update() {
  // Limitar jogador
  if (player.y < 0) player.y = 0;
  if (player.y > canvas.height) player.y = canvas.height;

  // Tiros
  bullets.forEach((b, i) => {
    b.x += 7;
    if (b.x > canvas.width) bullets.splice(i, 1);
  });

  // Inimigos
  enemies.forEach((e, i) => {
    e.x -= e.speed;
    if (e.x < 0) enemies.splice(i, 1);
  });

  // Colisão
  bullets.forEach((b, bi) => {
    enemies.forEach((e, ei) => {
      const dx = b.x - e.x;
      const dy = b.y - e.y;
      const dist = Math.sqrt(dx*dx + dy*dy);
      if (dist < e.radius) {
        bullets.splice(bi, 1);
        enemies.splice(ei, 1);
      }
    });
  });
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Jogador
  ctx.fillStyle = "blue";
  ctx.beginPath();
  ctx.arc(player.x, player.y, player.radius, 0, Math.PI * 2);
  ctx.fill();

  // Tiros
  ctx.fillStyle = "white";
  bullets.forEach(b => {
    ctx.beginPath();
    ctx.arc(b.x, b.y, 5, 0, Math.PI * 2);
    ctx.fill();
  });

  // Inimigos
  ctx.fillStyle = "red";
  enemies.forEach(e => {
    ctx.beginPath();
    ctx.arc(e.x, e.y, e.radius, 0, Math.PI * 2);
    ctx.fill();
  });
}

function gameLoop() {
  update();
  draw();
  requestAnimationFrame(gameLoop);
}

gameLoop();
</script></body>
</html>
