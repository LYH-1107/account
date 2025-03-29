    <title>🐰 乐乐的智能记账本 💖</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@3.7.1"></script>
    <style>
        :root {
            --pink: #ff9ecf;
            --blue: #8ad3ff;
            --yellow: #fff3a3;
            --shadow: 0 10px 30px rgba(255, 182, 193, 0.2);
        }

        body {
            background: linear-gradient(135deg, #ffe8f5, #d4f0fd);
            min-height: 100vh;
            padding: 20px;
            font-family: 'Mali', cursive;
            animation: gradientShift 15s ease infinite;
        }

        @keyframes gradientShift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .container {
            max-width: 1000px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.98);
            padding: 30px;
            border-radius: 30px;
            box-shadow: var(--shadow);
            backdrop-filter: blur(5px);
        }

        h1 {
            color: #ff6b9d;
            text-align: center;
            margin: 20px 0;
            font-size: 2.8em;
            text-shadow: 2px 2px 0px rgba(255, 255, 255, 0.8);
            position: relative;
        }

        h1::after {
            content: "";
            display: block;
            width: 60px;
            height: 4px;
            background: linear-gradient(90deg, var(--pink), var(--blue));
            margin: 10px auto;
            border-radius: 2px;
        }

        .input-section {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 15px;
            background: rgba(255, 250, 252, 0.9);
            padding: 20px;
            border-radius: 20px;
            margin-bottom: 25px;
            box-shadow: inset 0 0 15px rgba(255, 182, 193, 0.1);
        }

        .salary-field {
            display: none;
            animation: fieldAppear 0.3s ease;
        }

        @keyframes fieldAppear {
            from { opacity: 0; transform: translateY(-10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .serial-number {
            font-family: 'Courier New', monospace;
            background: #fff3f8;
            padding: 3px 8px;
            border-radius: 5px;
        }

        input, select {
            padding: 12px 15px;
            border: 2px solid #ffd1e6;
            border-radius: 12px;
            font-size: 16px;
            transition: all 0.3s;
            background: rgba(255, 255, 255, 0.9);
        }

        input:focus, select:focus {
            border-color: var(--pink);
            box-shadow: 0 0 8px rgba(255, 155, 203, 0.2);
            outline: none;
        }

        button {
            padding: 12px 20px;
            border-radius: 12px;
            background: linear-gradient(135deg, var(--pink), var(--blue));
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(255, 155, 203, 0.3);
        }

        .dashboard {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-top: 20px;
        }

        .chart-container {
            background: white;
            border-radius: 20px;
            padding: 20px;
            box-shadow: 0 5px 15px rgba(255, 182, 193, 0.1);
            height: 300px;
            width: 100%;
            position: relative;
        }

        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 15px;
            margin: 20px 0;
        }

        .stat-item {
            background: white;
            padding: 15px;
            border-radius: 15px;
            text-align: center;
            box-shadow: 0 3px 8px rgba(0, 0, 0, 0.05);
        }

        .stat-item span {
            display: block;
            font-size: 1.4em;
            margin-top: 8px;
            font-weight: bold;
        }

        .records-table {
            width: 100%;
            border-collapse: separate;
            border-spacing: 0 8px;
        }

        .records-table th {
            background: #fff5f9;
            padding: 16px;
            font-size: 0.95em;
        }

        .records-table td {
            padding: 14px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 6px rgba(0, 0, 0, 0.03);
            animation: slideIn 0.3s ease-out;
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateX(-20px); }
            to { opacity: 1; transform: translateX(0); }
        }

        .delete-btn {
            background: #ffe6ee;
            padding: 6px 12px;
            border-radius: 6px;
            transition: all 0.2s;
            cursor: pointer;
        }

        .delete-btn:hover {
            background: #ffd1dd;
        }

        .toast {
            position: fixed;
            top: 20px;
            right: 20px;
            background: white;
            padding: 15px 25px;
            border-radius: 15px;
            box-shadow: var(--shadow);
            display: flex;
            align-items: center;
            gap: 10px;
            opacity: 0;
            transition: all 0.3s;
        }

        .toast.show {
            opacity: 1;
            transform: translateY(0);
        }

        @media (max-width: 768px) {
            .container {
                padding: 20px;
            }
            .dashboard {
                grid-template-columns: 1fr;
            }
            h1 {
                font-size: 2.2em;
            }
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Mali:wght@600&display=swap" rel="stylesheet">
</head>
<body>
    <div class="container">
        <h1>🐰 乐乐的智能记账本 💖</h1>
        
        <!-- 预算管理系统 -->
        <div class="stat-item" style="grid-column: 1 / -1;">
            <input type="number" id="monthly-budget" placeholder="💝 设置月度预算">
            <button onclick="saveBudget()">保存预算</button>
            <div id="budget-display" style="margin-top:10px"></div>
        </div>

        <!-- 智能输入区域 -->
        <div class="input-section">
            <select id="type">
                <option value="income">🌸 收入</option>
                <option value="expense">🍰 支出</option>
            </select>
            <select id="category" onchange="toggleSerialNumberField()">
                <option value="工资">💼 工资</option>
                <option value="餐饮">🍔 餐饮</option>
                <option value="购物">🛍️ 购物</option>
                <option value="交通">🚕 交通</option>
                <option value="娱乐">🎮 娱乐</option>
            </select>
            <input type="text" id="serialNumber" 
                   class="salary-field" 
                   placeholder="📇 工资单号"
                   style="background:#fff3f8">
            <input type="number" id="amount" placeholder="💴 金额">
            <input type="date" id="date">
            <button onclick="addRecord()">✨ 添加记录</button>
        </div>

        <!-- 数据过滤系统 -->
        <div class="filter-section" style="display:flex;gap:10px;margin:15px 0">
            <button class="filter-btn active" onclick="filterRecords('all')">全部</button>
            <button class="filter-btn" onclick="filterRecords('income')">收入</button>
            <button class="filter-btn" onclick="filterRecords('expense')">支出</button>
            <input type="month" id="month-filter" onchange="filterByMonth()">
        </div>

        <!-- 实时统计看板 -->
        <div class="stats">
            <div class="stat-item" style="color: #4CAF50;">
                🌸 总收入
                <span id="total-income">0</span>
            </div>
            <div class="stat-item" style="color: #f44336;">
                🍰 总支出
                <span id="total-expense">0</span>
            </div>
            <div class="stat-item" style="color: #2196F3;">
                🤑 当前结余
                <span id="balance">0</span>
            </div>
        </div>

        <!-- 数据可视化中心 -->
        <div class="dashboard">
            <div class="chart-container">
                <canvas id="myChart"></canvas>
            </div>
            <div style="overflow-x: auto;">
                <table class="records-table">
                    <thead>
                        <tr>
                            <th>类型</th>
                            <th>分类</th>
                            <th>金额</th>
                            <th>日期</th>
                            <th>单号</th>
                            <th>操作</th>
                        </tr>
                    </thead>
                    <tbody id="records-body"></tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        // 数据管理系统
        let records = JSON.parse(localStorage.getItem('financeRecords')) || [];
        let chart = null;

        // 图表初始化（增强版）
        function initChart() {
            const ctx = document.getElementById('myChart').getContext('2d');
            if(chart) chart.destroy();
            
            chart = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: ['收入', '支出'],
                    datasets: [{
                        data: [getTotalIncome(), getTotalExpense()],
                        backgroundColor: ['#90EE90', '#FFB6C1'],
                        borderWidth: 0
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { 
                            position: 'bottom',
                            labels: { font: { size: 14 } }
                        },
                        tooltip: { 
                            bodyFont: { size: 16 } 
                        }
                    }
                }
            });
        }

        // 智能表单控制
        function toggleSerialNumberField() {
            const category = document.getElementById('category').value;
            const serialField = document.getElementById('serialNumber');
            serialField.style.display = category === '工资' ? 'block' : 'none';
            serialField.toggleAttribute('required', category === '工资');
        }

        // 数据添加功能（增强验证）
        function addRecord() {
            const type = document.getElementById('type').value;
            const category = document.getElementById('category').value;
            const amount = document.getElementById('amount').value;
            const date = document.getElementById('date').value;
            const serialNumber = document.getElementById('serialNumber').value.trim();

            // 强化表单验证
            const errorMessages = [];
            if (!category) errorMessages.push("请选择分类");
            if (!amount || isNaN(amount)) errorMessages.push("请输入有效金额");
            if (!date) errorMessages.push("请选择日期");
            if (category === '工资' && !serialNumber) errorMessages.push("工资单号不能为空");

            if (errorMessages.length > 0) {
                showToast(errorMessages.join("，"), 'warning');
                return;
            }

            // 添加格式化数据
            records.push({
                type,
                category,
                amount: Number(amount).toFixed(2),
                date,
                serialNumber: category === '工资' ? serialNumber : null
            });

            // 更新界面
            renderRecords();
            clearForm();
            showToast('🎉 记录添加成功！', 'success');
        }

        // 数据渲染引擎
        function renderRecords(filterType = 'all', month = '') {
            const filtered = records.filter(r => {
                const matchType = filterType === 'all' || r.type === filterType;
                const matchMonth = !month || r.date.startsWith(month);
                return matchType && matchMonth;
            });

            const tbody = document.getElementById('records-body');
            tbody.innerHTML = '';

            filtered.forEach((record, index) => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td class="${record.type}">${record.type === 'income' ? '🌸 收入' : '🍰 支出'}</td>
                    <td>${record.category}</td>
                    <td>¥${record.amount}</td>
                    <td>${record.date}</td>
                    <td>${record.category === '工资' 
                        ? `<span class="serial-number">${record.serialNumber}</span>` 
                        : '—'}</td>
                    <td><span class="delete-btn" onclick="deleteRecord(${index})">❌ 删除</span></td>
                `;
                tbody.appendChild(row);
            });

            updateStats();
            updateBudgetDisplay();
            updateChart();
            localStorage.setItem('financeRecords', JSON.stringify(records));
        }

        // 辅助功能模块
        function updateChart() {
            if (chart) {
                chart.data.datasets[0].data = [getTotalIncome(), getTotalExpense()];
                chart.update();
            } else {
                initChart();
            }
        }

        function saveBudget() {
            const budget = document.getElementById('monthly-budget').value;
            if (!budget || isNaN(budget)) {
                showToast("请输入有效预算金额", 'warning');
                return;
            }
            localStorage.setItem('monthlyBudget', budget);
            updateBudgetDisplay();
            showToast('预算设置成功 💰', 'success');
        }

        function updateBudgetDisplay() {
            const budget = localStorage.getItem('monthlyBudget') || 0;
            const remaining = budget - getTotalExpense();
            document.getElementById('budget-display').innerHTML = `
                ${remaining >= 0 ? '💚 剩余' : '💔 超支'} ¥${Math.abs(remaining).toFixed(2)}
            `;
        }

        function deleteRecord(index) {
            if (confirm('确定要删除这条记录吗？')) {
                records.splice(index, 1);
                renderRecords();
                showToast('记录已删除 🗑️', 'warning');
            }
        }

        function clearForm() {
            document.getElementById('category').value = '工资';
            document.getElementById('amount').value = '';
            document.getElementById('date').value = '';
            document.getElementById('serialNumber').value = '';
            toggleSerialNumberField();
        }

        function showToast(message, type = 'success') {
            const toast = document.createElement('div');
            toast.className = `toast ${type}`;
            toast.innerHTML = `
                <div class="toast-icon">${type === 'success' ? '🎉' : '⚠️'}</div>
                <div>${message}</div>
            `;
            document.body.appendChild(toast);
            
            setTimeout(() => {
                toast.classList.add('show');
                setTimeout(() => toast.remove(), 3000);
            }, 100);
        }

        // 统计计算模块
        function getTotalIncome() {
            return records.filter(r => r.type === 'income')
                   .reduce((sum, r) => sum + Number(r.amount), 0);
        }

        function getTotalExpense() {
            return records.filter(r => r.type === 'expense')
                   .reduce((sum, r) => sum + Number(r.amount), 0);
        }

        function updateStats() {
            document.getElementById('total-income').textContent = getTotalIncome().toFixed(2);
            document.getElementById('total-expense').textContent = getTotalExpense().toFixed(2);
            document.getElementById('balance').textContent = (getTotalIncome() - getTotalExpense()).toFixed(2);
        }

        // 初始化系统
        document.addEventListener('DOMContentLoaded', () => {
            renderRecords();
            initChart();
            updateBudgetDisplay();
            toggleSerialNumberField();
        });
    </script>
</body>
</html>
