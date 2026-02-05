<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JunkBloque - Ganhe Pontos</title>
    <style>
        body { 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
            text-align: center; 
            background: #1a1a2e; 
            color: #e94560; 
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        #game-container { 
            background: #16213e;
            border: 3px solid #0f3460; 
            padding: 30px; 
            border-radius: 20px; 
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
            width: 300px;
        }
        h1 { color: #4ecca3; margin-bottom: 10px; }
        .score-box { font-size: 32px; font-weight: bold; margin: 20px 0; color: #fff; }
        .btn { 
            background: #4ecca3; 
            border: none; 
            padding: 15px; 
            width: 100%;
            font-size: 18px; 
            cursor: pointer; 
            border-radius: 10px; 
            color: #1a1a2e; 
            font-weight: bold;
            transition: 0.3s;
            margin-bottom: 10px;
        }
        .btn:hover { background: #45b293; transform: scale(1.05); }
        .btn:disabled { background: #394a51; cursor: not-allowed; transform: none; color: #777; }
        .ad-btn { background: #e94560; color: white; }
        .ad-btn:hover { background: #cf3a54; }
        #status-msg { font-size: 14px; color: #888; }
    </style>
</head>
<body>

    <div id="game-container">
        <h1>JunkBloque</h1>
        <div class="score-box">JB: <span id="points">0</span></div>
        
        <button id="clickBtn" class="btn" onclick="playGame()">JOGAR JUNKBLOQUE</button>
        
        <button id="adBtn" class="btn ad-btn" onclick="showAd()" style="display: none;">
            VER ANÚNCIO (+50 JB)
        </button>

        <p id="status-msg">Clique no botão para minerar pontos!</p>
        <button onclick="resetGame()" style="background:none; border:none; color:#555; cursor:pointer; font-size:10px; margin-top:20px;">Resetar progresso</button>
    </div>

    <script>
        // Carrega os pontos salvos ou começa do zero
        let score = parseInt(localStorage.getItem('junkbloque_pts')) || 0;
        let playCount = 0;

        // Atualiza a tela ao carregar
        document.getElementById('points').innerText = score;

        function updateDisplay() {
            document.getElementById('points').innerText = score;
            localStorage.setItem('junkbloque_pts', score);
        }

        function playGame() {
            score += 1;
            playCount++;
            updateDisplay();

            if (playCount >= 10) {
                document.getElementById('clickBtn').style.display = 'none';
                document.getElementById('adBtn').style.display = 'block';
                document.getElementById('status-msg').innerText = "Energia esgotada! Veja um anúncio.";
            }
        }

        function showAd() {
            document.getElementById('status-msg').innerText = "Carregando anúncio...";
            document.getElementById('adBtn').disabled = true;

            // Simula o tempo do anúncio (3 segundos)
            setTimeout(() => {
                score += 50;
                playCount = 0;
                updateDisplay();
                
                document.getElementById('clickBtn').style.display = 'block';
                document.getElementById('adBtn').style.display = 'none';
                document.getElementById('adBtn').disabled = false;
                document.getElementById('status-msg').innerText = "Pontos creditados! Continue jogando.";
                alert("Anúncio finalizado! Você ganhou 50 pontos JunkBloque.");
            }, 3000);
        }

        function resetGame() {
            if(confirm("Deseja zerar seus pontos?")) {
                score = 0;
                playCount = 0;
                updateDisplay();
                location.reload();
            }
        }
    </script>
</body>
</html>
