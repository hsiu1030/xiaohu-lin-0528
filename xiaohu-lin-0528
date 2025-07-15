<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>轉帳手續費試算工具</title>
  <style>
    body { font-family: sans-serif; padding: 20px; max-width: 600px; margin: auto; }
    input, button { padding: 8px; font-size: 1em; }
    .result { margin-top: 20px; padding: 10px; background: #f3f3f3; border-radius: 8px; }
  </style>
</head>
<body>
  <h1>轉帳金額與手續費試算</h1>
  <label>我希望對方實收金額（X 元）：<br>
    <input type="number" id="xAmount" />
  </label>
  <br><br>
  <button onclick="calculate()">試算</button>
  <div class="result" id="output"></div>

  <script>
    const thresholds = [
      { min: 100000000, rate: 0.01 },
      { min: 10000000, rate: 0.015 },
      { min: 1000000, rate: 0.0175 },
      { min: 100000, rate: 0.02 },
      { min: 10000, rate: 0.025 },
      { min: 1000, rate: 0.03 },
      { min: 100, fixed: 5 }
    ];

    function getY(x) {
      if (x < 100) return null;
      if (x < 1000) return Math.round(x + 5);

      for (let i = 0; i < thresholds.length; i++) {
        let t = thresholds[i];
        if (x >= t.min && t.rate) {
          let y = Math.round(x / (1 - t.rate));
          if (y >= t.min) return y;
        }
      }
      return null;
    }

    function getRateTier(y) {
      if (y < 100) return "❌ 拒絕交易";
      if (y < 1000) return "固定手續費 5 元";
      for (let i = 0; i < thresholds.length; i++) {
        const t = thresholds[i];
        if (y >= t.min && t.rate) return `${(t.rate * 100).toFixed(2)}%`;
      }
      return "無級距";
    }

    function bestSplit(x) {
      if (x < 100) return "❌ 不允許轉帳";
      const originalY = getY(x);
      const originalFee = originalY - x;
      let best = { parts: 1, totalY: originalY, totalFee: originalFee, perY: originalY };

      for (let i = 2; i <= 10; i++) {
        let partX = Math.round(x / i);
        let y = getY(partX);
        if (!y) continue;
        let totalY = y * i;
        let totalFee = totalY - x;
        if (totalFee < best.totalFee) {
          best = { parts: i, totalY, totalFee, perY: y };
        }
      }

      if (best.parts === 1) return "不需要拆分，手續費已為最少。";
      return `建議拆成 ${best.parts} 筆，每筆轉 ${best.perY} 元，總手續費 ${best.totalFee} 元`;
    }

    function calculate() {
      const x = parseFloat(document.getElementById("xAmount").value);
      const output = document.getElementById("output");
      if (isNaN(x)) return output.innerHTML = "請輸入有效的金額。";

      const y = getY(x);
      if (y === null) return output.innerHTML = "❌ 金額過低，無法轉帳。";
      const fee = y - x;
      const tier = getRateTier(y);
      const split = bestSplit(x);

      output.innerHTML = `
        <strong>對方實收：</strong> ${x} 元<br>
        <strong>你需要轉帳：</strong> ${y} 元<br>
        <strong>手續費：</strong> ${fee} 元<br>
        <strong>適用級距：</strong> ${tier}<br>
        <strong>拆分建議：</strong> ${split}
      `;
    }
  </script>
</body>
</html>
