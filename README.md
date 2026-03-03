<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Budget Tracker</title>

  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Segoe UI', sans-serif;
    }

    body {
      background: linear-gradient(135deg, #667eea, #764ba2);
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
    }

    .app {
      background: white;
      width: 100%;
      max-width: 500px;
      padding: 30px;
      border-radius: 20px;
      box-shadow: 0 20px 50px rgba(0,0,0,0.2);
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
      color: #6c63ff;
    }

    .summary {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin-bottom: 20px;
    }

    .box {
      flex: 1;
      min-width: 140px;
      padding: 15px;
      border-radius: 15px;
      color: white;
      text-align: center;
    }

    .income  { background: linear-gradient(135deg, #00c9a7, #92fe9d); }
    .expense { background: linear-gradient(135deg, #ff758c, #ff7eb3); }
    .balance { background: linear-gradient(135deg, #6c63ff, #48c6ef); }
    .today   { background: linear-gradient(135deg, #ff9966, #ff5e62); }

    input, select, button {
      width: 100%;
      padding: 10px;
      margin: 8px 0;
      border-radius: 10px;
      border: 1px solid #ccc;
      font-size: 14px;
    }

    button {
      border: none;
      background: linear-gradient(90deg, #6c63ff, #ff6584);
      color: white;
      cursor: pointer;
      transition: 0.3s;
    }

    button:hover {
      transform: translateY(-2px);
      box-shadow: 0 5px 15px rgba(0,0,0,0.2);
    }

    ul {
      list-style: none;
      margin-top: 10px;
      max-height: 200px;
      overflow-y: auto;
    }

    li {
      padding: 10px;
      margin: 8px 0;
      background: #f4f4ff;
      border-radius: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 14px;
    }

    .delete {
      background: #ff4d6d;
      border: none;
      color: white;
      padding: 4px 8px;
      border-radius: 6px;
      cursor: pointer;
    }

    small {
      color: gray;
      margin-left: 5px;
    }
  </style>
</head>

<body>

<div class="app">
  <h2>Budget Tracker 💰</h2>

  <div class="summary">
    <div class="box income">
      <h4>Income</h4>
      <p>$<span id="income">0</span></p>
    </div>

    <div class="box expense">
      <h4>Expenses</h4>
      <p>$<span id="expense">0</span></p>
    </div>

    <div class="box balance">
      <h4>Balance</h4>
      <p>$<span id="balance">0</span></p>
    </div>

    <div class="box today">
      <h4>Today's Expense</h4>
      <p>$<span id="todayExpense">0</span></p>
    </div>
  </div>

  <input type="text" id="desc" placeholder="Description">
  <input type="number" id="amount" placeholder="Amount">

  <select id="type">
    <option value="income">Income</option>
    <option value="expense">Expense</option>
  </select>

  <button onclick="addTransaction()">Add Transaction</button>

  <ul id="list"></ul>
</div>

<script>
  let transactions = JSON.parse(localStorage.getItem("transactions")) || [];

  function saveData() {
    localStorage.setItem("transactions", JSON.stringify(transactions));
  }

  function addTransaction() {
    const desc = document.getElementById("desc").value;
    let amount = Number(document.getElementById("amount").value);
    const type = document.getElementById("type").value;

    if (desc === "" || amount === 0) {
      alert("Please enter description and amount.");
      return;
    }

    if (type === "expense") {
      amount = -Math.abs(amount);
    }

    const transaction = {
      id: Date.now(),
      desc: desc,
      amount: amount,
      date: new Date().toLocaleDateString()
    };

    transactions.push(transaction);
    saveData();
    updateUI();

    document.getElementById("desc").value = "";
    document.getElementById("amount").value = "";
  }

  function deleteTransaction(id) {
    transactions = transactions.filter(t => t.id !== id);
    saveData();
    updateUI();
  }

  function updateUI() {
    const list = document.getElementById("list");
    list.innerHTML = "";

    let income = 0;
    let expense = 0;
    let todayExpense = 0;

    const today = new Date().toLocaleDateString();

    transactions.forEach(t => {

      if (t.amount > 0) {
        income += t.amount;
      } else {
        expense += t.amount;

        if (t.date === today) {
          todayExpense += t.amount;
        }
      }

      const li = document.createElement("li");
      li.innerHTML = `
        ${t.desc} ($${t.amount})
        <small>${t.date}</small>
        <button class="delete" onclick="deleteTransaction(${t.id})">X</button>
      `;

      list.appendChild(li);
    });

    document.getElementById("income").innerText = income;
    document.getElementById("expense").innerText = Math.abs(expense);
    document.getElementById("balance").innerText = income + expense;
    document.getElementById("todayExpense").innerText = Math.abs(todayExpense);
  }

  updateUI();
</script>

</body>
</html>
