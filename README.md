<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Моделирование движения тел в гравитационном поле</title>
    <script src="https://cdn.plot.ly/plotly-2.24.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/numeric/1.2.6/numeric.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
            color: #fff;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            padding: 30px 0;
            margin-bottom: 30px;
            border-bottom: 2px solid rgba(255, 255, 255, 0.1);
        }

        h1 {
            font-size: 2.8rem;
            margin-bottom: 15px;
            background: linear-gradient(90deg, #00c9ff, #92fe9d);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: 0 2px 10px rgba(0, 201, 255, 0.3);
        }

        .subtitle {
            font-size: 1.2rem;
            color: #aaa;
            max-width: 800px;
            margin: 0 auto;
            line-height: 1.6;
        }

        .main-content {
            display: grid;
            grid-template-columns: 400px 1fr;
            gap: 30px;
            margin-bottom: 30px;
        }

        @media (max-width: 1024px) {
            .main-content {
                grid-template-columns: 1fr;
            }
        }

        .control-panel {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }

        .input-group {
            margin-bottom: 25px;
        }

        label {
            display: block;
            margin-bottom: 10px;
            font-weight: 500;
            color: #92fe9d;
            font-size: 1.1rem;
        }

        input[type="number"] {
            width: 100%;
            padding: 15px;
            background: rgba(255, 255, 255, 0.07);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            color: white;
            font-size: 1.1rem;
            transition: all 0.3s ease;
        }

        input[type="number"]:focus {
            outline: none;
            border-color: #00c9ff;
            box-shadow: 0 0 15px rgba(0, 201, 255, 0.3);
        }

        .value-display {
            display: block;
            margin-top: 8px;
            color: #00c9ff;
            font-size: 0.9rem;
        }

        .btn {
            width: 100%;
            padding: 18px;
            background: linear-gradient(90deg, #00c9ff, #92fe9d);
            border: none;
            border-radius: 10px;
            color: #0f2027;
            font-size: 1.2rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-top: 10px;
            margin-bottom: 20px;
        }

        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 10px 25px rgba(0, 201, 255, 0.4);
        }

        .btn:active {
            transform: translateY(0);
        }

        .preset-buttons {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-top: 20px;
        }

        .preset-btn {
            padding: 12px;
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 8px;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .preset-btn:hover {
            background: rgba(0, 201, 255, 0.2);
            border-color: #00c9ff;
        }

        .results-panel {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }

        .results-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 25px;
            margin-top: 25px;
        }

        .result-item {
            background: rgba(255, 255, 255, 0.05);
            padding: 20px;
            border-radius: 10px;
            border-left: 4px solid #00c9ff;
        }

        .result-label {
            font-size: 0.9rem;
            color: #aaa;
            margin-bottom: 5px;
        }

        .result-value {
            font-size: 1.4rem;
            color: #92fe9d;
            font-weight: bold;
        }

        .trajectory-type {
            text-align: center;
            padding: 20px;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 10px;
            margin-top: 20px;
            border: 2px solid #00c9ff;
        }

        .type-label {
            color: #aaa;
            font-size: 1rem;
            margin-bottom: 10px;
        }

        .type-value {
            font-size: 1.6rem;
            font-weight: bold;
            background: linear-gradient(90deg, #00c9ff, #92fe9d);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .graph-container {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 25px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
            margin-bottom: 30px;
        }

        .graph-title {
            text-align: center;
            margin-bottom: 20px;
            color: #92fe9d;
            font-size: 1.3rem;
        }

        .plot {
            width: 100%;
            height: 500px;
            background: rgba(255, 255, 255, 0.02);
            border-radius: 10px;
            overflow: hidden;
        }

        footer {
            text-align: center;
            padding: 20px;
            color: #aaa;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            margin-top: 30px;
            font-size: 0.9rem;
        }

        .info-text {
            color: #92fe9d;
            font-size: 0.95rem;
            margin-top: 5px;
            opacity: 0.8;
        }

        .spinner {
            border: 4px solid rgba(255, 255, 255, 0.1);
            border-top: 4px solid #00c9ff;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 20px auto;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .error-message {
            background: rgba(255, 0, 0, 0.1);
            border: 1px solid rgba(255, 0, 0, 0.3);
            border-radius: 10px;
            padding: 15px;
            color: #ff6b6b;
            margin-top: 20px;
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🌌 Моделирование движения тел в гравитационном поле</h1>
            <p class="subtitle">Интерактивный симулятор орбитального движения. Введите параметры запуска и наблюдайте траекторию движения спутника вокруг Земли.</p>
        </header>

        <div class="main-content">
            <!-- Левая панель: управление -->
            <div class="control-panel">
                <h2 style="margin-bottom: 25px; color: #00c9ff;">⚙️ Параметры запуска</h2>
                
                <div class="input-group">
                    <label for="height">Высота над поверхностью (км)</label>
                    <input type="number" id="height" min="100" max="10000" step="50" value="400">
                    <span class="value-display">Стандартное значение: 400 км (как МКС)</span>
                </div>

                <div class="input-group">
                    <label for="velocity">Начальная скорость (м/с)</label>
                    <input type="number" id="velocity" min="1000" max="20000" step="100" value="7670">
                    <span class="value-display" id="velocityInfo">Первая космическая скорость: ~7670 м/с</span>
                    <div class="info-text" id="speedInfo"></div>
                </div>

                <div class="input-group">
                    <label for="time">Время моделирования (минут)</label>
                    <input type="number" id="time" min="1" max="300" step="10" value="100">
                    <span class="value-display">1 полный оборот ≈ 90 минут</span>
                </div>

                <button class="btn" onclick="runSimulation()">
                    🚀 Запустить моделирование
                </button>

                <div class="error-message" id="errorMessage"></div>

                <div id="loading" style="display: none;">
                    <div class="spinner"></div>
                    <p style="text-align: center; margin-top: 10px;">Рассчитываю траекторию...</p>
                </div>

                <h3 style="margin: 25px 0 15px 0; color: #92fe9d;">⚡ Быстрые пресеты</h3>
                <div class="preset-buttons">
                    <button class="preset-btn" onclick="setPreset('circular')">
                        🟡 Круговая орбита<br>7670 м/с
                    </button>
                    <button class="preset-btn" onclick="setPreset('elliptical')">
                        🔵 Эллиптическая<br>9000 м/с
                    </button>
                    <button class="preset-btn" onclick="setPreset('parabolic')">
                        🟢 Параболическая<br>11200 м/с
                    </button>
                    <button class="preset-btn" onclick="setPreset('hyperbolic')">
                        🔴 Гиперболическая<br>15000 м/с
                    </button>
                </div>
            </div>

            <!-- Правая панель: результаты -->
            <div class="results-panel">
                <h2 style="color: #92fe9d; margin-bottom: 20px;">📊 Результаты моделирования</h2>
                
                <div class="trajectory-type">
                    <div class="type-label">ТИП ТРАЕКТОРИИ</div>
                    <div class="type-value" id="trajectoryType">—</div>
                </div>

                <div class="results-grid">
                    <div class="result-item">
                        <div class="result-label">Начальная высота</div>
                        <div class="result-value" id="initialHeight">— км</div>
                    </div>
                    <div class="result-item">
                        <div class="result-label">Начальная скорость</div>
                        <div class="result-value" id="initialVelocity">— м/с</div>
                    </div>
                    <div class="result-item">
                        <div class="result-label">Минимальная высота</div>
                        <div class="result-value" id="minHeight">— км</div>
                    </div>
                    <div class="result-item">
                        <div class="result-label">Максимальная высота</div>
                        <div class="result-value" id="maxHeight">— км</div>
                    </div>
                </div>

                <div style="margin-top: 30px; padding: 20px; background: rgba(0, 201, 255, 0.1); border-radius: 10px;">
                    <h3 style="color: #00c9ff; margin-bottom: 15px;">💡 Справка по скоростям</h3>
                    <ul style="list-style: none; color: #aaa;">
                        <li>• <strong style="color: #92fe9d;">7670 м/с</strong> — первая космическая (круговая орбита)</li>
                        <li>• <strong style="color: #92fe9d;">11200 м/с</strong> — вторая космическая (параболическая)</li>
                        <li>• <strong style="color: #92fe9d;">>11200 м/с</strong> — гиперболическая траектория</li>
                    </ul>
                </div>
            </div>
        </div>

        <!-- График 1: Траектория -->
        <div class="graph-container">
            <div class="graph-title">🌍 Траектория движения спутника вокруг Земли</div>
            <div id="trajectoryPlot" class="plot"></div>
        </div>

        <!-- График 2: Высота -->
        <div class="graph-container">
            <div class="graph-title">📈 Высота над поверхностью Земли</div>
            <div id="heightPlot" class="plot"></div>
        </div>

        <footer>
            <p>© 2025 Моделирование движения тел в гравитационном поле</p>
            <p>Алгоритм: Метод Верле | Параметры: G = 6.67430×10⁻¹¹ Н·м²/кг², Mₑ = 5.972×10²⁴ кг</p>
        </footer>
    </div>

    <script>
        // Константы
        const G = 6.67430e-11;
        const M_earth = 5.972e24;
        const R_earth = 6371000;
        let currentSimulationData = null;

        // Пресеты
        function setPreset(type) {
            const presets = {
                circular: { height: 400, velocity: 7670 },
                elliptical: { height: 400, velocity: 9000 },
                parabolic: { height: 400, velocity: 11200 },
                hyperbolic: { height: 400, velocity: 15000 }
            };
            
            const preset = presets[type];
            document.getElementById('height').value = preset.height;
            document.getElementById('velocity').value = preset.velocity;
            
            // Обновляем информацию о скорости
            updateSpeedInfo(preset.velocity);
        }

        // Обновление информации о скорости
        function updateSpeedInfo(velocity) {
            const info = document.getElementById('speedInfo');
            const firstCosmic = Math.sqrt(G * M_earth / (R_earth + parseFloat(document.getElementById('height').value) * 1000));
            
            let text = '';
            if (Math.abs(velocity - firstCosmic) < 100) {
                text = '🔵 Скорость близка к первой космической (круговая орбита)';
            } else if (velocity >= 11100 && velocity <= 11300) {
                text = '🟢 Скорость близка ко второй космической (параболическая)';
            } else if (velocity > 11300) {
                text = '🔴 Скорость выше второй космической (гиперболическая)';
            } else if (velocity > firstCosmic) {
                text = '🔵 Скорость выше первой космической (эллиптическая)';
            } else {
                text = '🟡 Скорость ниже первой космической (суборбитальный полет)';
            }
            
            info.textContent = text;
        }

        // Основная функция моделирования
        function runSimulation() {
            // Сброс ошибок и показ загрузки
            document.getElementById('errorMessage').style.display = 'none';
            document.getElementById('loading').style.display = 'block';
            document.getElementById('trajectoryType').textContent = 'РАСЧЁТ...';

            // Получение входных данных
            const height = parseFloat(document.getElementById('height').value) * 1000; // в метры
            const velocity = parseFloat(document.getElementById('velocity').value);
            const simTime = parseFloat(document.getElementById('time').value) * 60; // в секунды

            // Проверка ввода
            if (isNaN(height) || isNaN(velocity) || isNaN(simTime) || height < 100000 || velocity < 1000) {
                showError('Пожалуйста, введите корректные значения. Высота ≥ 100 км, скорость ≥ 1000 м/с');
                document.getElementById('loading').style.display = 'none';
                return;
            }

            // Обновление информации о скорости
            updateSpeedInfo(velocity);

            // Запуск расчёта (асинхронно, чтобы не блокировать интерфейс)
            setTimeout(() => {
                try {
                    const result = calculateTrajectory(height, velocity, simTime);
                    displayResults(result);
                    plotGraphs(result);
                    currentSimulationData = result;
                } catch (error) {
                    showError('Ошибка при расчёте: ' + error.message);
                } finally {
                    document.getElementById('loading').style.display = 'none';
                }
            }, 100);
        }

        // Вычисление траектории
        function calculateTrajectory(height, velocity, simTime) {
            const dt = 1; // шаг 1 секунда
            const steps = Math.floor(simTime / dt);
            
            const x0 = 0;
            const y0 = R_earth + height;
            const vx0 = velocity;
            const vy0 = 0;
            
            // Инициализация массивов
            const x = new Array(steps).fill(0);
            const y = new Array(steps).fill(0);
            const vx = new Array(steps).fill(0);
            const vy = new Array(steps).fill(0);
            const time = new Array(steps).fill(0);
            
            x[0] = x0;
            y[0] = y0;
            vx[0] = vx0;
            vy[0] = vy0;
            
            // Функция ускорения
            function acceleration(x, y) {
                const r = Math.sqrt(x*x + y*y);
                const ax = -G * M_earth * x / (r*r*r);
                const ay = -G * M_earth * y / (r*r*r);
                return { ax, ay };
            }
            
            // Численное интегрирование методом Верле
            for (let i = 0; i < steps - 1; i++) {
                const { ax, ay } = acceleration(x[i], y[i]);
                
                x[i+1] = x[i] + vx[i]*dt + 0.5*ax*dt*dt;
                y[i+1] = y[i] + vy[i]*dt + 0.5*ay*dt*dt;
                
                const { ax: ax_new, ay: ay_new } = acceleration(x[i+1], y[i+1]);
                
                vx[i+1] = vx[i] + 0.5*(ax + ax_new)*dt;
                vy[i+1] = vy[i] + 0.5*(ay + ay_new)*dt;
                time[i+1] = time[i] + dt;
            }
            
            // Вычисление высоты и скорости
            const height_km = x.map((xi, idx) => (Math.sqrt(xi*xi + y[idx]*y[idx]) - R_earth) / 1000);
            const speed = vx.map((vxi, idx) => Math.sqrt(vxi*vxi + vy[idx]*vy[idx]));
            
            // Определение типа траектории
            const minHeight = Math.min(...height_km);
            const maxHeight = Math.max(...height_km);
            const minSpeed = Math.min(...speed);
            
            const r0 = R_earth + height;
            const specificEnergy = (velocity*velocity)/2 - (G * M_earth)/r0;
            
            let trajectoryType;
            if (specificEnergy < -1e6) {
                if (maxHeight - minHeight < 50) {
                    trajectoryType = "КРУГОВАЯ ОРБИТА";
                } else {
                    trajectoryType = "ЭЛЛИПТИЧЕСКАЯ ОРБИТА";
                }
            } else if (Math.abs(specificEnergy) < 1e6) {
                trajectoryType = "ПАРАБОЛИЧЕСКАЯ ТРАЕКТОРИЯ";
            } else {
                trajectoryType = "ГИПЕРБОЛИЧЕСКАЯ ТРАЕКТОРИЯ";
            }
            
            return {
                x, y, time,
                height_km, speed,
                initialHeight: height/1000,
                initialVelocity: velocity,
                minHeight, maxHeight,
                minSpeed: Math.min(...speed),
                maxSpeed: Math.max(...speed),
                trajectoryType,
                specificEnergy
            };
        }

        // Отображение результатов
        function displayResults(result) {
            document.getElementById('initialHeight').textContent = result.initialHeight.toFixed(1) + " км";
            document.getElementById('initialVelocity').textContent = result.initialVelocity.toFixed(0) + " м/с";
            document.getElementById('minHeight').textContent = result.minHeight.toFixed(1) + " км";
            document.getElementById('maxHeight').textContent = result.maxHeight.toFixed(1) + " км";
            document.getElementById('trajectoryType').textContent = result.trajectoryType;
            
            // Цвет траектории в зависимости от типа
            const typeElement = document.getElementById('trajectoryType');
            if (result.trajectoryType.includes("КРУГОВАЯ")) {
                typeElement.style.background = "linear-gradient(90deg, #00c9ff, #0077ff)";
            } else if (result.trajectoryType.includes("ЭЛЛИПТИЧЕСКАЯ")) {
                typeElement.style.background = "linear-gradient(90deg, #00c9ff, #00ffaa)";
            } else if (result.trajectoryType.includes("ПАРАБОЛИЧЕСКАЯ")) {
                typeElement.style.background = "linear-gradient(90deg, #ffaa00, #ff5500)";
            } else {
                typeElement.style.background = "linear-gradient(90deg, #ff0066, #ff0000)";
            }
        }

        // Построение графиков
        function plotGraphs(result) {
            // График 1: Траектория
            const earthTrace = {
                type: 'scatter',
                x: [0],
                y: [0],
                mode: 'markers',
                marker: {
                    size: [R_earth/50000],
                    color: ['#1e90ff'],
                    opacity: 0.7
                },
                name: 'Земля',
                hoverinfo: 'text',
                text: ['Земля']
            };
            
            const trajectoryTrace = {
                type: 'scatter',
                x: result.x,
                y: result.y,
                mode: 'lines',
                line: {
                    color: '#00ffaa',
                    width: 2
                },
                name: 'Траектория'
            };
            
            const startTrace = {
                type: 'scatter',
                x: [result.x[0]],
                y: [result.y[0]],
                mode: 'markers',
                marker: {
                    size: 10,
                    color: '#00ff00'
                },
                name: 'Старт'
            };
            
            Plotly.newPlot('trajectoryPlot', [earthTrace, trajectoryTrace, startTrace], {
                title: 'Траектория движения (X-Y плоскость)',
                xaxis: {
                    title: 'X координата (м)',
                    gridcolor: 'rgba(255,255,255,0.1)',
                    zerolinecolor: 'rgba(255,255,255,0.3)'
                },
                yaxis: {
                    title: 'Y координата (м)',
                    gridcolor: 'rgba(255,255,255,0.1)',
                    zerolinecolor: 'rgba(255,255,255,0.3)',
                    scaleanchor: 'x',
                    scaleratio: 1
                },
                plot_bgcolor: 'rgba(0,0,0,0)',
                paper_bgcolor: 'rgba(0,0,0,0)',
                font: { color: '#fff' },
                showlegend: true,
                legend: {
                    x: 0.02,
                    y: 0.98,
                    bgcolor: 'rgba(0,0,0,0.5)',
                    bordercolor: 'rgba(255,255,255,0.2)'
                }
            });
            
            // График 2: Высота
            const heightTrace = {
                x: result.time.map(t => t/60), // в минуты
                y: result.height_km,
                type: 'scatter',
                mode: 'lines',
                line: {
                    color: '#00c9ff',
                    width: 3
                },
                name: 'Высота'
            };
            
            Plotly.newPlot('heightPlot', [heightTrace], {
                title: 'Высота над поверхностью Земли',
                xaxis: {
                    title: 'Время (минуты)',
                    gridcolor: 'rgba(255,255,255,0.1)',
                    zerolinecolor: 'rgba(255,255,255,0.3)'
                },
                yaxis: {
                    title: 'Высота (км)',
                    gridcolor: 'rgba(255,255,255,0.1)',
                    zerolinecolor: 'rgba(255,255,255,0.3)'
                },
                plot_bgcolor: 'rgba(0,0,0,0)',
                paper_bgcolor: 'rgba(0,0,0,0)',
                font: { color: '#fff' },
                showlegend: false
            });
        }

        // Показать ошибку
        function showError(message) {
            const errorDiv = document.getElementById('errorMessage');
            errorDiv.textContent = message;
            errorDiv.style.display = 'block';
        }

        // Инициализация при загрузке
        window.onload = function() {
            // Автоматически обновляем информацию при изменении скорости
            document.getElementById('velocity').addEventListener('input', function() {
                updateSpeedInfo(parseFloat(this.value) || 7670);
            });
            
            // Запускаем демонстрационный расчёт
            setTimeout(() => {
                setPreset('circular');
                runSimulation();
            }, 500);
        };
    </script>
</body>
</html>
