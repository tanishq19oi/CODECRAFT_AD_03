<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Precision Stopwatch</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap');
        * {
            font-family: 'Inter', sans-serif;
        }
        body {
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            min-height: 100vh;
            transition: background 0.5s ease;
        }
        body.light-mode {
            background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e1 100%);
        }
        .stopwatch-container {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
            transition: all 0.5s ease;
        }
        .light-mode .stopwatch-container {
            background: rgba(255, 255, 255, 0.9);
            border: 1px solid rgba(0, 0, 0, 0.1);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.15);
        }
        .time-display {
            font-family: 'JetBrains Mono', monospace;
            text-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            letter-spacing: 2px;
            transition: color 0.5s ease;
        }
        .dark .time-display {
            color: #60a5fa;
            text-shadow: 0 0 20px rgba(96, 165, 250, 0.3);
        }
        .light-mode .time-display {
            color: #1e40af;
            text-shadow: 0 5px 15px rgba(30, 64, 175, 0.1);
        }
        .time-unit {
            color: #94a3b8;
            transition: color 0.5s ease;
        }
        .light-mode .time-unit {
            color: #475569;
        }
        .control-btn {
            background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%);
            color: white;
            border: none;
            padding: 16px 32px;
            border-radius: 12px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(59, 130, 246, 0.3);
        }
        .control-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(59, 130, 246, 0.4);
        }
        .control-btn:active {
            transform: translateY(0);
        }
        .light-mode .control-btn {
            background: linear-gradient(135deg, #2563eb 0%, #1e40af 100%);
            box-shadow: 0 4px 15px rgba(37, 99, 235, 0.2);
        }
        .control-btn.pause {
            background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);
            box-shadow: 0 4px 15px rgba(245, 158, 11, 0.3);
        }
        .light-mode .control-btn.pause {
            background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
        }
        .control-btn.reset {
            background: linear-gradient(135deg, #ef4444 0%, #dc2626 100%);
            box-shadow: 0 4px 15px rgba(239, 68, 68, 0.3);
        }
        .light-mode .control-btn.reset {
            background: linear-gradient(135deg, #dc2626 0%, #b91c1c 100%);
        }
        .pulse-animation {
            animation: pulse 1.5s infinite;
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }
        .theme-toggle {
            position: fixed;
            top: 20px;
            right: 20px;
            width: 50px;
            height: 50px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
            z-index: 1000;
        }
        .theme-toggle:hover {
            transform: rotate(30deg);
            background: rgba(255, 255, 255, 0.2);
        }
        .light-mode .theme-toggle {
            background: rgba(0, 0, 0, 0.1);
            border: 1px solid rgba(0, 0, 0, 0.2);
        }
        .light-mode .theme-toggle:hover {
            background: rgba(0, 0, 0, 0.2);
        }
        .theme-toggle i {
            color: #ffffff;
            font-size: 1.5rem;
            transition: all 0.3s ease;
        }
        .light-mode .theme-toggle i {
            color: #1e293b;
        }
        .miliseconds {
            font-size: 3.5rem;
            opacity: 0.9;
        }
        .lap-list {
            max-height: 200px;
            overflow-y: auto;
        }
        .lap-item {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 8px;
            padding: 10px 15px;
            margin-bottom: 5px;
            border-left: 3px solid #3b82f6;
            transition: all 0.3s ease;
        }
        .light-mode .lap-item {
            background: rgba(0, 0, 0, 0.05);
            border-left: 3px solid #2563eb;
        }
        .lap-item:hover {
            transform: translateX(5px);
        }
        .lap-number {
            background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%);
            color: white;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 600;
        }
        .light-mode .lap-number {
            background: linear-gradient(135deg, #2563eb 0%, #1e40af 100%);
        }
        .lap-time {
            font-family: 'JetBrains Mono', monospace;
        }
        .dark .lap-time {
            color: #cbd5e1;
        }
        .light-mode .lap-time {
            color: #475569;
        }
        .current-status {
            position: absolute;
            top: 20px;
            left: 20px;
            background: rgba(255, 255, 255, 0.1);
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 0.875rem;
            color: #94a3b8;
            backdrop-filter: blur(10px);
        }
        .light-mode .current-status {
            background: rgba(0, 0, 0, 0.1);
            color: #64748b;
        }
        .status-running {
            background: rgba(34, 197, 94, 0.2);
            color: #22c55e;
        }
        .light-mode .status-running {
            background: rgba(34, 197, 94, 0.2);
        }
        .status-paused {
            background: rgba(245, 158, 11, 0.2);
            color: #f59e0b;
        }
        .light-mode .status-paused {
            background: rgba(245, 158, 11, 0.2);
        }
        .status-stopped {
            background: rgba(239, 68, 68, 0.2);
            color: #ef4444;
        }
        .light-mode .status-stopped {
            background: rgba(239, 68, 68, 0.2);
        }
        .divider {
            height: 1px;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
            margin: 20px 0;
        }
        .light-mode .divider {
            background: linear-gradient(90deg, transparent, rgba(0, 0, 0, 0.2), transparent);
        }
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 10px;
        }
        .light-mode ::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.05);
        }
        ::-webkit-scrollbar-thumb {
            background: linear-gradient(180deg, #3b82f6, #1d4ed8);
            border-radius: 10px;
        }
        .light-mode ::-webkit-scrollbar-thumb {
            background: linear-gradient(180deg, #2563eb, #1e40af);
        }
        .flash {
            animation: flash 0.3s ease;
        }
        @keyframes flash {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }
        @media (max-width: 640px) {
            .time-display {
                font-size: 4rem !important;
            }
            .miliseconds {
                font-size: 2.5rem !important;
            }
            .control-btn {
                padding: 14px 24px;
            }
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen p-4 dark">
    
    <div class="theme-toggle" id="themeToggle">
        <i class="fas fa-moon" id="themeIcon"></i>
    </div>
    
    <div class="stopwatch-container rounded-3xl p-8 w-full max-w-2xl">
        
        <div class="current-status status-stopped" id="currentStatus">
            <i class="fas fa-stop-circle mr-2"></i> Stopped
        </div>
        
        <div class="text-center mb-8">
            <h1 class="text-3xl font-bold mb-2 text-white transition-colors duration-500 light-mode:text-gray-900">Precision Stopwatch</h1>
            <p class="text-gray-400 transition-colors duration-500 light-mode:text-gray-600">
                Track time with millisecond accuracy
            </p>
        </div>
        
        <div class="time-display text-center mb-10">
            <div class="flex items-baseline justify-center gap-1">
                <div class="text-8xl" id="minutes">00</div>
                <div class="text-4xl time-unit">min</div>
                <div class="text-8xl" id="seconds">00</div>
                <div class="text-4xl time-unit">sec</div>
            </div>
            <div class="flex items-baseline justify-center gap-1 mt-2">
                <div class="text-4xl time-unit">.</div>
                <div class="text-6xl miliseconds" id="milliseconds">000</div>
                <div class="text-4xl time-unit">ms</div>
            </div>
        </div>
        
        <div class="flex flex-wrap justify-center gap-4 mb-8">
            <button class="control-btn flex items-center" id="startBtn">
                <i class="fas fa-play mr-2"></i> Start
            </button>
            <button class="control-btn pause flex items-center" id="pauseBtn" disabled>
                <i class="fas fa-pause mr-2"></i> Pause
            </button>
            <button class="control-btn reset flex items-center" id="resetBtn" disabled>
                <i class="fas fa-redo mr-2"></i> Reset
            </button>
            <button class="control-btn flex items-center" id="lapBtn" disabled>
                <i class="fas fa-flag mr-2"></i> Lap
            </button>
        </div>
        
        <div class="divider"></div>
        
        <div class="mt-8">
            <h3 class="text-xl font-semibold mb-4 text-white transition-colors duration-500 light-mode:text-gray-900 flex items-center">
                <i class="fas fa-flag-checkered mr-2"></i> Lap Times
                <span class="ml-2 text-sm bg-blue-500 text-white px-2 py-1 rounded-full" id="lapCount">0</span>
            </h3>
            
            <div class="lap-list" id="lapList">
                <p class="text-center text-gray-400 transition-colors duration-500 light-mode:text-gray-600 py-4">
                    No lap times recorded yet
                </p>
            </div>
        </div>
        
        <div class="text-center mt-8 text-sm text-gray-400 transition-colors duration-500 light-mode:text-gray-600">
            <p><i class="fas fa-info-circle mr-2"></i> Press Spacebar to start/pause, 'R' to reset, 'L' for lap</p>
        </div>
        
    </div>

    <script>
        let startTime = 0;
        let elapsedTime = 0;
        let timerInterval = null;
        let isRunning = false;
        let laps = [];
        let lapCounter = 1;
        
        const minutesDisplay = document.getElementById('minutes');
        const secondsDisplay = document.getElementById('seconds');
        const millisecondsDisplay = document.getElementById('milliseconds');
        const startBtn = document.getElementById('startBtn');
        const pauseBtn = document.getElementById('pauseBtn');
        const resetBtn = document.getElementById('resetBtn');
        const lapBtn = document.getElementById('lapBtn');
        const lapList = document.getElementById('lapList');
        const lapCount = document.getElementById('lapCount');
        const currentStatus = document.getElementById('currentStatus');
        const themeToggle = document.getElementById('themeToggle');
        const themeIcon = document.getElementById('themeIcon');
        const body = document.body;
        
        function formatTime(time) {
            const minutes = Math.floor(time / 60000);
            const seconds = Math.floor((time % 60000) / 1000);
            const milliseconds = Math.floor((time % 1000));
            
            return {
                minutes: minutes.toString().padStart(2, '0'),
                seconds: seconds.toString().padStart(2, '0'),
                milliseconds: milliseconds.toString().padStart(3, '0')
            };
        }
        
        function updateDisplay() {
            const time = formatTime(elapsedTime);
            minutesDisplay.textContent = time.minutes;
            secondsDisplay.textContent = time.seconds;
            millisecondsDisplay.textContent = time.milliseconds;
            
            if (isRunning) {
                millisecondsDisplay.classList.add('pulse-animation');
            } else {
                millisecondsDisplay.classList.remove('pulse-animation');
            }
        }
        
        function startTimer() {
            if (!isRunning) {
                startTime = Date.now() - elapsedTime;
                timerInterval = setInterval(() => {
                    elapsedTime = Date.now() - startTime;
                    updateDisplay();
                }, 10);
                
                isRunning = true;
                updateControls();
                updateStatus('running');
                document.getElementById('startBtn').disabled = true;
                document.getElementById('pauseBtn').disabled = false;
                lapBtn.disabled = false;
            }
        }
        
        function pauseTimer() {
            if (isRunning) {
                clearInterval(timerInterval);
                isRunning = false;
                updateControls();
                updateStatus('paused');
                document.getElementById('startBtn').disabled = false;
                document.getElementById('pauseBtn').disabled = true;
            }
        }
        
        function resetTimer() {
            clearInterval(timerInterval);
            isRunning = false;
            elapsedTime = 0;
            laps = [];
            lapCounter = 1;
            updateDisplay();
            updateControls();
            updateStatus('stopped');
            updateLapList();
            document.getElementById('startBtn').disabled = false;
            document.getElementById('pauseBtn').disabled = true;
            lapBtn.disabled = true;
            resetBtn.disabled = true;
        }
        
        function recordLap() {
            if (isRunning) {
                const lapTime = elapsedTime;
                const previousLapTime = laps.length > 0 ? laps[laps.length - 1].time : 0;
                const lapDifference = lapTime - previousLapTime;
                
                laps.push({
                    number: lapCounter,
                    time: lapTime,
                    difference: lapDifference
                });
                
                lapCounter++;
                updateLapList();
                
                lapList.classList.add('flash');
                setTimeout(() => lapList.classList.remove('flash'), 300);
            }
        }
        
        function updateControls() {
            if (isRunning) {
                startBtn.style.display = 'none';
                pauseBtn.style.display = 'flex';
            } else {
                startBtn.style.display = 'flex';
                pauseBtn.style.display = 'flex';
            }
            
            if (elapsedTime > 0 || laps.length > 0) {
                resetBtn.disabled = false;
            }
        }
        
        function updateStatus(status) {
            currentStatus.className = 'current-status';
            
            switch(status) {
                case 'running':
                    currentStatus.classList.add('status-running');
                    currentStatus.innerHTML = '<i class="fas fa-play-circle mr-2"></i> Running';
                    break;
                case 'paused':
                    currentStatus.classList.add('status-paused');
                    currentStatus.innerHTML = '<i class="fas fa-pause-circle mr-2"></i> Paused';
                    break;
                case 'stopped':
                    currentStatus.classList.add('status-stopped');
                    currentStatus.innerHTML = '<i class="fas fa-stop-circle mr-2"></i> Stopped';
                    break;
            }
        }
        
        function updateLapList() {
            lapCount.textContent = laps.length;
            
            if (laps.length === 0) {
                lapList.innerHTML = '<p class="text-center text-gray-400 transition-colors duration-500 light-mode:text-gray-600 py-4">No lap times recorded yet</p>';
                return;
            }
            
            lapList.innerHTML = '';
            
            laps.slice().reverse().forEach(lap => {
                const lapTime = formatTime(lap.time);
                const diffTime = formatTime(lap.difference);
                
                const lapItem = document.createElement('div');
                lapItem.className = 'lap-item flex items-center justify-between';
                
                lapItem.innerHTML = `
                    <div class="flex items-center">
                        <div class="lap-number mr-3">${lap.number}</div>
                        <div>
                            <div class="lap-time text-lg">${lapTime.minutes}:${lapTime.seconds}.<span class="text-sm">${lapTime.milliseconds}</span></div>
                            <div class="text-xs text-gray-400 light-mode:text-gray-600">+${diffTime.minutes}:${diffTime.seconds}.${diffTime.milliseconds}</div>
                        </div>
                    </div>
                    <div class="text-sm text-gray-400 light-mode:text-gray-600">
                        ${lap.number === 1 ? 'First Lap' : 'Lap ' + lap.number}
                    </div>
                `;
                
                lapList.appendChild(lapItem);
            });
        }
        
        function formatLapTime(time) {
            const formatted = formatTime(time);
            return `${formatted.minutes}:${formatted.seconds}.${formatted.milliseconds}`;
        }
        
        function toggleTheme() {
            body.classList.toggle('light-mode');
            body.classList.toggle('dark');
            
            if (body.classList.contains('light-mode')) {
                themeIcon.className = 'fas fa-sun';
                themeIcon.style.color = '#f59e0b';
            } else {
                themeIcon.className = 'fas fa-moon';
                themeIcon.style.color = '#ffffff';
            }
            
            localStorage.setItem('stopwatchTheme', body.classList.contains('light-mode') ? 'light' : 'dark');
        }
        
        function loadTheme() {
            const savedTheme = localStorage.getItem('stopwatchTheme') || 'dark';
            
            if (savedTheme === 'light') {
                body.classList.add('light-mode');
                body.classList.remove('dark');
                themeIcon.className = 'fas fa-sun';
                themeIcon.style.color = '#f59e0b';
            } else {
                body.classList.remove('light-mode');
                body.classList.add('dark');
                themeIcon.className = 'fas fa-moon';
                themeIcon.style.color = '#ffffff';
            }
        }
        
        document.addEventListener('keydown', (e) => {
            if (e.code === 'Space') {
                e.preventDefault();
                if (isRunning) {
                    pauseTimer();
                } else {
                    startTimer();
                }
            } else if (e.code === 'KeyR' && (elapsedTime > 0 || laps.length > 0)) {
                resetTimer();
            } else if (e.code === 'KeyL' && isRunning) {
                recordLap();
            }
        });
        
        startBtn.addEventListener('click', startTimer);
        pauseBtn.addEventListener('click', pauseTimer);
        resetBtn.addEventListener('click', resetTimer);
        lapBtn.addEventListener('click', recordLap);
        themeToggle.addEventListener('click', toggleTheme);
        
        document.addEventListener('DOMContentLoaded', () => {
            loadTheme();
            updateDisplay();
            updateControls();
            updateStatus('stopped');
        });
        
        updateDisplay();
    </script>
</body>
</html>
