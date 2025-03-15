<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Personal Expense Tracker</title>
    <link rel="stylesheet" href="style.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
    <div id="registrationDesk">
        <h1>Personal Expense Tracker</h1>
        <h2>Register</h2>
        <input type="text" id="regUsername" placeholder="Enter Username" required>
        <input type="password" id="regPassword" placeholder="Enter Password" required>
        <button onclick="registerUser()">Register</button>

        <h2>Login</h2>
        <input type="text" id="loginUsername" placeholder="Enter Username" required>
        <input type="password" id="loginPassword" placeholder="Enter Password" required>
        <button onclick="loginUser()">Login</button>
    </div>

    <div id="dashboard" style="display: none;">
        <h1 id="welcomeMessage"></h1>
        <button onclick="logout()">Back to Registration Desk</button>
        
        <h2>Enter Income</h2>
        <input type="number" id="incomeAmount" placeholder="Enter Monthly Income (₹)">
        <button onclick="addIncome()">Add Income</button>

        <h2>Enter Expense</h2>
        <input type="text" id="expenseCategory" placeholder="Category (Food, Rent, etc.)">
        <input type="number" id="expenseAmount" placeholder="Amount (₹)">
        <button onclick="addExpense()">Add Expense</button>

        <h2>Expense List</h2>
        <ul id="expenseList"></ul>

        <h2>Financial Summary</h2>
        <p>Total Income: ₹<span id="totalIncome">0</span></p>
        <p>Total Monthly Expenses: ₹<span id="totalExpenses">0</span></p>
        <p>Remaining Amount: ₹<span id="remainingAmount">0</span></p>

        <h2>Expense Chart</h2>
        <canvas id="expenseChart" width="400" height="400"></canvas>
    </div>

    <script src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    margin: 0;
    padding: 0;
    text-align: center;
}

h1 {
    color: #2c3e50;
}

input {
    padding: 10px;
    margin: 5px;
}

button {
    padding: 10px;
    margin: 5px;
    background-color: #3498db;
    color: white;
    border: none;
    cursor: pointer;
}

button:hover {
    background-color: #2980b9;
}

#dashboard {
    background-color: #ecf0f1;
    padding: 20px;
    margin: 20px;
    border-radius: 10px;
    display: inline-block;
}
let users = {};
let currentUser = null;
let expenseChart = null;

function registerUser() {
    const username = document.getElementById('regUsername').value;
    const password = document.getElementById('regPassword').value;

    if (username && password) {
        users[username] = { password, income: 0, expenses: [] };
        alert("Registration successful! Please login.");
    } else {
        alert("Please enter both username and password.");
    }
}

function loginUser() {
    const username = document.getElementById('loginUsername').value;
    const password = document.getElementById('loginPassword').value;

    if (users[username] && users[username].password === password) {
        currentUser = username;
        document.getElementById('registrationDesk').style.display = 'none';
        document.getElementById('dashboard').style.display = 'block';
        document.getElementById('welcomeMessage').innerText = `Welcome, ${username}!`;
        updateDashboard();
    } else {
        alert("Invalid username or password.");
    }
}

function logout() {
    currentUser = null;
    document.getElementById('registrationDesk').style.display = 'block';
    document.getElementById('dashboard').style.display = 'none';
}

function addIncome() {
    const amount = parseFloat(document.getElementById('incomeAmount').value);
    if (amount > 0) {
        users[currentUser].income = amount;
        updateDashboard();
    }
}

function addExpense() {
    const category = document.getElementById('expenseCategory').value;
    const amount = parseFloat(document.getElementById('expenseAmount').value);

    if (category && amount > 0) {
        users[currentUser].expenses.push({ category, amount });
        updateDashboard();
    }
}

function updateDashboard() {
    const user = users[currentUser];
    const totalExpenses = user.expenses.reduce((sum, exp) => sum + exp.amount, 0);
    const remainingAmount = user.income - totalExpenses;

    document.getElementById('totalIncome').innerText = user.income;
    document.getElementById('totalExpenses').innerText = totalExpenses;
    document.getElementById('remainingAmount').innerText = remainingAmount;

    const expenseList = document.getElementById('expenseList');
    expenseList.innerHTML = '';
    user.expenses.forEach(exp => {
        const li = document.createElement('li');
        li.innerText = `${exp.category}: ₹${exp.amount}`;
        expenseList.appendChild(li);
    });

    renderPieChart();
}

function renderPieChart() {
    const user = users[currentUser];
    const expenseData = user.expenses.map(exp => exp.amount);
    const expenseLabels = user.expenses.map(exp => exp.category);

    if (expenseChart) {
        expenseChart.destroy();
    }

    const ctx = document.getElementById('expenseChart').getContext('2d');
    expenseChart = new Chart(ctx, {
        type: 'pie',
        data: {
            labels: expenseLabels,
            datasets: [{
                data: expenseData,
                backgroundColor: expenseLabels.map(() => getRandomColor())
            }]
        },
        options: {
            responsive: true,
            plugins: {
                legend: {
                    position: 'bottom'
                }
            }
        }
    });
}

function getRandomColor() {
    return `hsl(${Math.floor(Math.random() * 360)}, 70%, 70%)`;
}
