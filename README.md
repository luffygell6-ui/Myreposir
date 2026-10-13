# Myreposir
WoW
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WhoIsMyIP</title>
    <!-- Fonte Google -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600&display=swap" rel="stylesheet">
    <!-- Ícones -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: rgba(30, 41, 59, 0.7);
            --text-primary: #f8fafc;
            --text-secondary: #94a3b8;
            --accent: #38bdf8;
            --border: rgba(255, 255, 255, 0.1);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg-color);
            color: var(--text-primary);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background-image: radial-gradient(circle at top right, #1e293b 0%, transparent 40%),
                              radial-gradient(circle at bottom left, #1e293b 0%, transparent 40%);
        }

        .container {
            width: 90%;
            max-width: 500px;
            background: var(--card-bg);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid var(--border);
            border-radius: 20px;
            padding: 2rem;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
            animation: fadeIn 0.8s ease-out;
        }

        header { text-align: center; margin-bottom: 2rem; }
        
        h1 { font-size: 1.5rem; font-weight: 600; letter-spacing: -0.5px; }
        
        .main-ip {
            text-align: center;
            margin: 1.5rem 0;
            padding: 1rem;
            background: rgba(56, 189, 248, 0.1);
            border-radius: 12px;
            border: 1px solid rgba(56, 189, 248, 0.2);
        }

        .ip-label { font-size: 0.85rem; color: var(--text-secondary); text-transform: uppercase; letter-spacing: 1px; }
        
        .ip-value { 
            font-size: 2.5rem; 
            font-weight: 700; 
            color: var(--accent);
            word-break: break-all;
        }

        .details-grid {
            display: grid;
            gap: 1rem;
            margin-top: 1.5rem;
        }

        .detail-item {
            display: flex;
            align-items: center;
            padding: 0.8rem;
            background: rgba(255, 255, 255, 0.03);
            border-radius: 10px;
            transition: transform 0.2s;
        }

        .detail-item:hover { transform: translateX(5px); background: rgba(255, 255, 255, 0.06); }

        .icon-box {
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(56, 189, 248, 0.1);
            color: var(--accent);
            border-radius: 8px;
            margin-right: 1rem;
        }

        .info-text h3 { font-size: 0.9rem; color: var(--text-secondary); font-weight: 400; }
        .info-text p { font-size: 1.1rem; font-weight: 600; margin-top: 2px; }

        .btn-refresh {
            width: 100%;
            margin-top: 2rem;
            padding: 1rem;
            background: linear-gradient(135deg, #38bdf8, #2563eb);
            border: none;
            border-radius: 10px;
            color: white;
            font-weight: 600;
            cursor: pointer;
            transition: opacity 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        .btn-refresh:hover { opacity: 0.9; }
        .btn-refresh:disabled { opacity: 0.5; cursor: not-allowed; }

        .loader {
            border: 3px solid rgba(255,255,255,0.1);
            border-top: 3px solid var(--accent);
            border-radius: 50%;
            width: 20px;
            height: 20px;
            animation: spin 1s linear infinite;
            display: none;
        }

        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* Mobile adjustments */
        @media (max-width: 480px) {
            .ip-value { font-size: 1.8rem; }
            .container { padding: 1.5rem; }
        }
    </style>
</head>
<body>

    <div class="container">
        <header>
            <h1><i class="fas fa-globe-americas"></i> Info IP</h1>
        </header>

        <div class="main-ip">
            <div class="ip-label">Seu Endereço IP Público</div>
            <div class="ip-value" id="ipDisplay">...</div>
        </div>

        <div class="details-grid">
            <div class="detail-item">
                <div class="icon-box"><i class="fas fa-map-marker-alt"></i></div>
                <div class="info-text">
                    <h3>Localização</h3>
                    <p id="locationDisplay">Carregando...</p>
                </div>
            </div>

            <div class="detail-item">
                <div class="icon-box"><i class="fas fa-building"></i></div>
                <div class="info-text">
                    <h3>Provedor (ISP)</h3>
                    <p id="ispDisplay">Carregando...</p>
                </div>
            </div>

            <div class="detail-item">
                <div class="icon-box"><i class="fas fa-clock"></i></div>
                <div class="info-text">
                    <h3>Fuso Horário</h3>
                    <p id="timezoneDisplay">Carregando...</p>
                </div>
            </div>
        </div>

        <button class="btn-refresh" id="refreshBtn" onclick="fetchIPData()">
            <i class="fas fa-sync-alt"></i> Atualizar Dados
        </button>
    </div>

    <script>
        async function fetchIPData() {
            const btn = document.getElementById('refreshBtn');
            const ipEl = document.getElementById('ipDisplay');
            const locEl = document.getElementById('locationDisplay');
            const ispEl = document.getElementById('ispDisplay');
            const tzEl = document.getElementById('timezoneDisplay');

            // Estado de Loading
            btn.disabled = true;
            btn.innerHTML = '<div class="loader" style="display:block"></div> Buscando...';
            
            try {
                // Usando ipapi.co (gratuito e não requer chave para baixo volume)
                const response = await fetch('https://ipapi.co/json/');
                
                if (!response.ok) throw new Error('Falha na conexão');
                
                const data = await response.json();

                // Atualiza DOM
                ipEl.innerText = data.ip;
                locEl.innerText = `${data.city}, ${data.country_name}`;
                ispEl.innerText = data.org || data.asn;
                tzEl.innerText = data.timezone;

            } catch (error) {
                console.error(error);
                ipEl.innerText = "Erro";
                locEl.innerText = "Não foi possível obter dados.";
                ispEl.innerText = "-";
                tzEl.innerText = "-";
            } finally {
                btn.disabled = false;
                btn.innerHTML = '<i class="fas fa-sync-alt"></i> Atualizar Dados';
            }
        }

        // Inicializa ao carregar
        window.onload = fetchIPData;
    </script>
</body>
</html>
