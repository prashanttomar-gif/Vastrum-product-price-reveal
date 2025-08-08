# Vastrum-product-price-reveal
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Vastrumstore Spin Wheel</title>
  <style>
    body {
      background-color: white;
      text-align: center;
      font-family: Arial, sans-serif;
      color: royalblue;
    }
    canvas {
      margin-top: 20px;
    }
    #result {
      margin-top: 20px;
      font-size: 24px;
      font-weight: bold;
      color: royalblue;
    }
    button {
      margin-top: 20px;
      padding: 10px 20px;
      background-color: royalblue;
      color: white;
      border: none;
      cursor: pointer;
      font-size: 18px;
      border-radius: 5px;
    }
    button:hover {
      background-color: #003c8f;
    }
  </style>
</head>
<body>
  <h1>Vastrumstore</h1>
  <canvas id="wheelCanvas" width="500" height="500"></canvas>
  <br>
  <button onclick="spinWheel()">Spin Now</button>
  <div id="result"></div>

  <script>
    const prices = [499, 549, 599, 649, 699, 749, 799, 849];
    const biasPrices = [499, 549, 599]; // High chance prices
    const colors = ["royalblue", "white"];
    const canvas = document.getElementById("wheelCanvas");
    const ctx = canvas.getContext("2d");
    const arcSize = (2 * Math.PI) / prices.length;
    let startAngle = 0;
    let spinTimeout = null;
    let spinAngleStart = 0;
    let spinTime = 0;
    let spinTimeTotal = 0;

    function drawWheel() {
      for (let i = 0; i < prices.length; i++) {
        const angle = startAngle + i * arcSize;
        ctx.fillStyle = colors[i % 2];
        ctx.beginPath();
        ctx.moveTo(250, 250);
        ctx.arc(250, 250, 250, angle, angle + arcSize, false);
        ctx.lineTo(250, 250);
        ctx.fill();
        ctx.save();
        ctx.translate(250 + Math.cos(angle + arcSize / 2) * 150,
                      250 + Math.sin(angle + arcSize / 2) * 150);
        ctx.rotate(angle + arcSize / 2);
        ctx.fillStyle = i % 2 === 0 ? "white" : "royalblue";
        ctx.font = "bold 20px Arial";
        ctx.fillText("₹" + prices[i], -30, 10);
        ctx.restore();
      }
      ctx.beginPath();
      ctx.moveTo(250 - 10, 0);
      ctx.lineTo(250 + 10, 0);
      ctx.lineTo(250, 40);
      ctx.fillStyle = "red";
      ctx.fill();
    }

    function spinWheel() {
      spinAngleStart = Math.random() * 10 + 10;
      spinTime = 0;
      spinTimeTotal = Math.random() * 3 + 4 * 1000;
      rotateWheel();
    }

    function rotateWheel() {
      spinTime += 30;
      if (spinTime >= spinTimeTotal) {
        stopRotateWheel();
        return;
      }
      const spinAngle = spinAngleStart - easeOut(spinTime, 0, spinAngleStart, spinTimeTotal);
      startAngle += (spinAngle * Math.PI / 180);
      drawWheel();
      spinTimeout = setTimeout(rotateWheel, 30);
    }

    function stopRotateWheel() {
      const degrees = startAngle * 180 / Math.PI + 90;
      const arcd = arcSize * 180 / Math.PI;
      const index = Math.floor((360 - (degrees % 360)) / arcd) % prices.length;
      
      let finalPrice;
      if (Math.random() < 0.7) {
        finalPrice = biasPrices[Math.floor(Math.random() * biasPrices.length)];
      } else {
        finalPrice = prices[index];
      }
      
      document.getElementById("result").innerHTML = "🎉 Congratulations! Your new product price is ₹" + finalPrice;
    }

    function easeOut(t, b, c, d) {
      const ts = (t /= d) * t;
      const tc = ts * t;
      return b + c * (tc + -3 * ts + 3 * t);
    }

    drawWheel();
  </script>
</body>
</html>
