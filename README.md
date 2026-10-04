<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Magical Particle Heart</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }
    body {
      background-color: #000;
      overflow: hidden;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    canvas {
      display: block;
    }
  </style>
</head>
<body>

<canvas id="canvas"></canvas>

<script>
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");

  let width = (canvas.width = window.innerWidth);
  let height = (canvas.height = window.innerHeight);

  window.addEventListener("resize", () => {
    width = canvas.width = window.innerWidth;
    height = canvas.height = window.innerHeight;
  });

  // Görselde tanımlanan mavi renk tonları
  const BLUE_SHADES = [
    "#3b82f6", "#60a5fa", // blue-400
    "#93c5fd", "#2563eb", // blue-600
    "#bfdbfe", "#1d4ed8"  // blue-700
  ];

  class Particle {
    constructor(x, y, type) {
      this.type = type;
      this.color = BLUE_SHADES[Math.floor(Math.random() * BLUE_SHADES.length)];
      this.maxLife = type === "float" ? 200 + Math.random() * 100 : 80 + Math.random() * 40;
      this.life = this.maxLife;

      if (type === "float") {
        this.x = x;
        this.y = y;
        this.vx = (Math.random() - 0.5) * 0.5;
        this.vy = -Math.random() * 0.8 - 0.2;
        this.size = Math.random() * 2 + 1;
      } else if (type === "burst") {
        // Kalp formülü (Heart parametric equation)
        const t = Math.random() * Math.PI * 2;
        const heartX = 16 * Math.pow(Math.sin(t), 3);
        const heartY = -(13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t));
        
        const speed = Math.random() * 0.8 + 0.2;
        this.x = x;
        this.y = y;
        this.vx = heartX * 0.4 * speed + (Math.random() - 0.5) * 0.5;
        this.vy = heartY * 0.4 * speed + (Math.random() - 0.5) * 0.5;
        this.size = Math.random() * 3 + 1.5;
      }
    }

    update() {
      this.x += this.vx;
      this.y += this.vy;
      this.life--;

      if (this.type === "float" && this.life <= 0) {
        this.x = Math.random() * width;
        this.y = height + 10;
        this.life = this.maxLife;
      }
    }

    draw() {
      const alpha = Math.max(0, this.life / this.maxLife);
      ctx.save();
      ctx.globalAlpha = alpha;
      ctx.shadowBlur = 10;
      ctx.shadowColor = this.color;
      ctx.fillStyle = this.color;
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.fill();
      ctx.restore();
    }
  }

  // Görseldeki değişkenler ve döngüler
  const floatParticles = [];
  const burstParticles = [];
  const FLOAT_COUNT = 140;

  for (let i = 0; i < FLOAT_COUNT; i++) {
    const p = new Particle(
      Math.random() * width, Math.random() * height, "float"
    );
    p.life = Math.random() * p.maxLife; // stagger starting life
    floatParticles.push(p);
  }

  function spawnHeartBurst(x, y) {
    const count = 140;
    for (let i = 0; i < count; i++) {
      burstParticles.push(new Particle(x, y, "burst"));
    }
  }

  // Otomatik patlama/kalp oluşumu
  setInterval(() => {
    spawnHeartBurst(width / 2, height / 2 + 50);
  }, 1200);

  // Ekrana tıklandığında da kalp oluşturur
  window.addEventListener("click", (e) => {
    spawnHeartBurst(e.clientX, e.clientY);
  });

  function animate() {
    ctx.fillStyle = "rgba(0, 0, 0, 0.2)";
    ctx.fillRect(0, 0, width, height);

    floatParticles.forEach((p) => {
      p.update();
      p.draw();
    });

    for (let i = burstParticles.length - 1; i >= 0; i--) {
      const p = burstParticles[i];
      p.update();
      p.draw();
      if (p.life <= 0) {
        burstParticles.splice(i, 1);
      }
    }

    requestAnimationFrame(animate);
  }

  animate();
</script>

</body>
</html>

