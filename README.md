# Fnaf---mobile-by-gemini
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>One Night at Mobile FNAF</title>
    <style>
        * { box-sizing: border-box; touch-action: manipulation; }
        body {
            background-color: #000;
            color: #fff;
            font-family: monospace;
            text-align: center;
            margin: 0;
            padding: 10px;
            overflow: hidden;
        }
        #game-screen {
            width: 100%;
            max-width: 400px;
            height: 500px;
            border: 2px solid #555;
            margin: 0 auto;
            position: relative;
            background-color: #111;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }
        .hud {
            padding: 10px;
            background: rgba(0,0,0,0.8);
            display: flex;
            justify-content: space-between;
            font-size: 14px;
        }
        #display-area {
            flex-grow: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            font-weight: bold;
            background-size: cover;
            position: relative;
        }
        .btn {
            background: #333;
            color: #fff;
            border: 1px solid #777;
            padding: 12px;
            margin: 5px;
            font-size: 14px;
            border-radius: 5px;
            cursor: pointer;
        }
        .btn:active { background: #555; }
        .danger { color: #ff4444; font-weight: bold; }
        .safe { color: #44ff44; }
    </style>
</head>
<body>

    <h2>Night 1 - Mobile Security</h2>

    <div id="game-screen">
        <div class="hud">
            <div>Batteria: <span id="battery">100</span>%</div>
            <div>Ora: <span id="time">12</span> AM</div>
        </div>

        <div id="display-area">
            <div id="status-text">Ufficio Sicuro</div>
        </div>

        <div style="padding: 10px;">
            <button class="btn" onclick="toggleDoor()">🚪 Porta: <span id="door-status">Aperta</span></button>
            <button class="btn" onclick="toggleCamera()">📹 <span id="cam-btn-text">Monitor CAMS</span></button>
        </div>
    </div>

    <script>
        // Stato del Gioco
        let battery = 100;
        let hour = 0; // 0 = 12 AM, 6 = 6 AM
        let doorClosed = false;
        let inCamera = false;
        let currentCam = 1;
        
        // Animatronico (0: Stage, 1: Corridoio, 2: Fuori dalla Porta, 3: Ufficio)
        let animatronicPos = 0; 
        let gameOver = false;

        const display = document.getElementById("display-area");
        const doorStatusText = document.getElementById("door-status");
        const batteryText = document.getElementById("battery");
        const timeText = document.getElementById("time");

        // Loop principale del tempo e della batteria
        setInterval(() => {
            if (gameOver) return;

            // Consumo batteria
            let drain = 1;
            if (doorClosed) drain += 1;
            if (inCamera) drain += 1;
            
            battery -= drain;
            if (battery < 0) battery = 0;
            batteryText.innerText = battery;

            if (battery === 0) {
                triggerJumpscare("⚡ Batteria Esaurita!");
            }
        }, 3000);

        // Avanzamento delle ore (12 AM -> 6 AM)
        setInterval(() => {
            if (gameOver) return;
            hour++;
            timeText.innerText = hour;
            if (hour === 6) {
                alert("🎉 COMPLIMENTI! Hai superato la notte (6 AM)!");
                location.reload();
            }
        }, 15000); // 15 secondi per ogni ora di gioco

        // IA dell'Animatronico
        setInterval(() => {
            if (gameOver) return;

            let moveChance = Math.random();
            if (moveChance > 0.4) {
                if (animatronicPos < 2) {
                    animatronicPos++;
                } else if (animatronicPos === 2) {
                    if (doorClosed) {
                        animatronicPos = 0; // Torna allo stage se la porta è chiusa
                    } else {
                        animatronicPos = 3; // Entra nell'ufficio!
                        triggerJumpscare("😱 JUMPSCARE! L'animatronico ti ha preso!");
                    }
                }
            }
            updateView();
        }, 5000);

        function toggleDoor() {
            if (gameOver) return;
            doorClosed = !doorClosed;
            doorStatusText.innerText = doorClosed ? "Chiusa" : "Aperta";
            doorStatusText.className = doorClosed ? "safe" : "danger";
            updateView();
        }

        function toggleCamera() {
            if (gameOver) return;
            inCamera = !inCamera;
            document.getElementById("cam-btn-text").innerText = inCamera ? "Chiudi Monitor" : "Monitor CAMS";
            updateView();
        }

        function updateView() {
            if (gameOver) return;

            if (inCamera) {
                let camText = "📹 CAM 01: ";
                if (animatronicPos === 0) camText += "Animatronico sullo Stage 🐻";
                else if (animatronicPos === 1) camText += "Corridoio vuoto...";
                else camText += "Nessun segnale...";
                display.innerText = camText;
                display.style.backgroundColor = "#223322";
            } else {
                display.style.backgroundColor = "#111";
                if (animatronicPos === 2 && !doorClosed) {
                    display.innerText = "👀 Qualcosa è fuori dalla porta!";
                } else {
                    display.innerText = "Ufficio di Sicurezza";
                }
            }
        }

        function triggerJumpscare(msg) {
            gameOver = true;
            display.style.backgroundColor = "#ff0000";
            display.innerHTML = `<h1 class="danger">${msg}</h1>`;
        }
    </script>
</body>
</html>
