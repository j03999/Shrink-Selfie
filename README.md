# Shrink-Selfie
Shrink Selfie Ball Game
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Ball Resize Game</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: #6495ED;
      font-family: sans-serif;
    }
    canvas {
      display: block;
    }
    #upload {
      position: absolute;
      top: 10px;
      left: 10px;
      z-index: 10;
    }
  </style>
</head>
<body>
  <input type="file" id="upload" accept="image/*" />
  <canvas id="gameCanvas"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    let ball = {
      x: canvas.width / 2,
      y: canvas.height / 2,
      radius: 80,
      scale: 1.0,
      img: null
    };

    let isDragging = false;
    let lastY = 0;

    // Handle mouse/touch input for resizing
    function onPointerDown(e) {
      isDragging = true;
      lastY = e.touches ? e.touches[0].clientY : e.clientY;
    }

    function onPointerMove(e) {
      if (!isDragging) return;
      const currentY = e.touches ? e.touches[0].clientY : e.clientY;
      const deltaY = currentY - lastY;
      ball.scale += deltaY * 0.005;
      ball.scale = Math.max(0.2, Math.min(2.0, ball.scale));
      lastY = currentY;
    }

    function onPointerUp() {
      isDragging = false;
    }

    canvas.addEventListener("mousedown", onPointerDown);
    canvas.addEventListener("mousemove", onPointerMove);
    canvas.addEventListener("mouseup", onPointerUp);
    canvas.addEventListener("touchstart", onPointerDown);
    canvas.addEventListener("touchmove", onPointerMove);
    canvas.addEventListener("touchend", onPointerUp);

    // Load selfie image
    document.getElementById("upload").addEventListener("change", function (e) {
      const file = e.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function (event) {
        const img = new Image();
        img.onload = function () {
          ball.img = img;
        };
        img.src = event.target.result;
      };
      reader.readAsDataURL(file);
    });

    // Game loop
    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      if (ball.img) {
        const size = ball.radius * ball.scale;
        ctx.drawImage(
          ball.img,
          ball.x - size,
          ball.y - size,
          size * 2,
          size * 2
        );
      } else {
        ctx.beginPath();
        ctx.arc(ball.x, ball.y, ball.radius * ball.scale, 0, Math.PI * 2);
        ctx.fillStyle = "#fff";
        ctx.fill();
        ctx.strokeStyle = "#333";
        ctx.stroke();
      }

      requestAnimationFrame(draw);
    }

    draw();
  </script>
</body>
</html>
