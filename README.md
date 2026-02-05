<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Junflix - IPTV Premium</title>
    
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>

    <style>
        :root { --red: #E50914; --black: #141414; --gray: #2f2f2f; }
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { font-family: 'Arial', sans-serif; background: var(--black); color: #fff; overflow-x: hidden; }

        header {
            padding: 15px 4%;
            background: linear-gradient(to bottom, rgba(0,0,0,0.9) 0%, transparent 100%);
            display: flex; justify-content: space-between; align-items: center;
            position: fixed; top: 0; width: 100%; z-index: 1000;
        }
        .logo { color: var(--red); font-size: 28px; font-weight: bold; }

        .top-nav { display: flex; gap: 10px; }
        input#url { background: #333; border: 1px solid #444; color: #fff; padding: 8px; border-radius: 4px; width: 150px; font-size: 12px; }
        
        .hero { width: 100%; height: 45vh; background: #000; display: flex; align-items: center; justify-content: center; margin-top: 60px; }
        video { width: 100%; height: 100%; background: #000; }

        .content { padding: 20px 4%; }
        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 15px; }

        .card {
            background: var(--gray); border-radius: 4px; overflow: hidden;
            cursor: pointer; transition: transform 0.3s; position: relative;
            aspect-ratio: 16/9; border: 1px solid #333;
        }
        .card:hover { transform: scale(1.05); }
        .card img { width: 100%; height: 100%; object-fit: contain; background: #222; }
        .card .title {
            position: absolute; bottom: 0; width: 100%; background: rgba(0,0,0,0.8);
            padding: 5px; font-size: 10px; text-align: center; white-space: nowrap; overflow: hidden;
        }
    </style>
</head>
<body>

<header>
    <div class="logo">JUNFLIX</div>
    <div class="top-nav">
        <input type="text" id="url" placeholder="Link da Lista .m3u" value="https://raw.githubusercontent.com/iptv-org/iptv/master/streams/br.m3u">
        <button onclick="loadList()" style="background:var(--red); color:#fff; border:none; padding:8px 15px; cursor:pointer; border-radius:4px; font-weight:bold;">OK</button>
    </div>
</header>

<div class="hero">
    <video id="player" controls autoplay playsinline></video>
</div>

<div class="content">
    <h2 id="cat-name" style="margin-bottom:15px; font-size: 18px;">Canais Disponíveis</h2>
    <div id="grid" class="grid">Carregando canais...</div>
</div>

<script>
    const video = document.getElementById('player');
    const hls = new Hls();
    // Usando um proxy para evitar erro de CORS
    const proxy = "https://api.allorigins.win/raw?url=";

    async function loadList() {
        const grid = document.getElementById('grid');
        grid.innerHTML = "Carregando...";
        const url = document.getElementById('url').value;
        
        try {
            const res = await fetch(proxy + encodeURIComponent(url));
            const data = await res.text();
            parse(data);
        } catch (e) {
            grid.innerHTML = "Erro ao carregar lista. Verifique o link ou tente novamente.";
        }
    }

    function parse(text) {
        const channels = [];
        const lines = text.split('\n');
        
        for (let i = 0; i < lines.length; i++) {
            if (lines[i].includes('#EXTINF')) {
                const name = lines[i].split(',')[1] || "Canal Sem Nome";
                const logoMatch = lines[i].match(/tvg-logo="([^"]+)"/);
                const logo = logoMatch ? logoMatch[1] : '';
                const stream = lines[i+1]?.trim();
                
                if (stream && stream.startsWith('http')) {
                    channels.push({ name, logo, stream });
                }
            }
        }
        render(channels);
    }

    function render(channels) {
        const grid = document.getElementById('grid');
        if (channels.length === 0) {
            grid.innerHTML = "Nenhum canal encontrado nesta lista.";
            return;
        }

        grid.innerHTML = channels.map(c => `
            <div class="card" onclick="play('${c.stream}')">
                <img src="${c.logo}" onerror="this.src='https://via.placeholder.com/300x168?text=TV'">
                <div class="title">${c.name}</div>
            </div>
        `).join('');
    }

    function play(url) {
        if (Hls.isSupported()) {
            hls.loadSource(url);
            hls.attachMedia(video);
            hls.play();
        } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
            video.src = url;
            video.play();
        }
        window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    window.onload = loadList;
</script>
</body>
</html>
