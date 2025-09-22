<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Modern Calculator</title>
  <style>
    body {
      background: #f5f5f5;
      font-family: 'Segoe UI', Arial, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    .calculator {
      background: #fff;
      border-radius: 12px;
      box-shadow: 0 4px 24px rgba(0,0,0,0.12);
      width: 320px;
      padding: 24px;
    }
    .display {
      width: 100%;
      font-size: 2.2em;
      text-align: right;
      margin-bottom: 18px;
      padding: 12px;
      border: none;
      background: #f0f0f0;
      border-radius: 6px;
      box-sizing: border-box;
    }
    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }
    .buttons button {
      font-size: 1.3em;
      padding: 18px 0;
      border: none;
      border-radius: 6px;
      background: #fff;
      box-shadow: 0 2px 8px rgba(0,0,0,0.04);
      cursor: pointer;
      transition: background 0.2s;
    }
    .buttons button:active {
      background: #e0e0e0;
    }
    .buttons .operator {
      background: #e3f2fd;
      color: #1976d2;
    }
    .buttons .equals {
      background: #1976d2;
      color: #fff;
    }
    .buttons .clear {
      background: #ffcdd2;
      color: #c62828;
    }
  </style>
</head>
<body>
  <div class="calculator">
    <input type="text" class="display" id="display" readonly>
    <div class="buttons">
      <button>7</button>
      <button>8</button>
      <button>9</button>
      <button class="operator">/</button>
      <button>4</button>
      <button>5</button>
      <button>6</button>
      <button class="operator">*</button>
      <button>1</button>
      <button>2</button>
      <button>3</button>
      <button class="operator">-</button>
      <button class="clear">C</button>
      <button>0</button>
      <button class="equals">=</button>
      <button class="operator">+</button>
    </div>
  </div>
  <script>
    const display = document.getElementById('display');
    let currentCalculation = '';

    document.querySelectorAll('.buttons button').forEach(button => {
      button.addEventListener('click', () => {
        const value = button.textContent;
        if (value === '=') {
          try {
            currentCalculation = eval(currentCalculation).toString();
            display.value = currentCalculation;
          } catch {
            display.value = 'Error';
            currentCalculation = '';
          }
        } else if (value === 'C') {
          currentCalculation = '';
          display.value = '';
        } else {
          currentCalculation += value;
          display.value = currentCalculation;
        }
      });
    });
  </script>
</body>
</html>
