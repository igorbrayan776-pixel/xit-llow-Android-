<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PAINEL LLOW ANDROID V1</title>
    <style>
        :root {
            --primary: #007bff; /* Azul Vibrante */
            --primary-dark: #004a99;
            --bg: #0a0a0a;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: sans-serif; user-select: none; -webkit-user-select: none; }

        body {
            background-color: #000;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }

        /* TELA DE CARREGAMENTO */
        #loader-screen {
            display: none; flex-direction: column; align-items: center; gap: 15px; z-index: 2000;
        }
        .loader-bar { width: 180px; height: 5px; background: #1a1a1a; border-radius: 10px; overflow: hidden; }
        .loader-progress { width: 0%; height: 100%; background: var(--primary); box-shadow: 0 0 10px var(--primary); transition: width 0.1s; }

        /* PAINEL COMPACTO PADRÃO */
        .panel { 
            width: 320px; 
            background: var(--bg); 
            border-radius: 8px; 
            overflow: hidden; 
            border: 1px solid #1a1a1a; 
            display: none; 
            z-index: 999; 
            position: absolute; 
            cursor: move;
            touch-action: none;
        }
        
        /* ESTADO MINIMIZADO */
        .panel.minimized { height: 26px !important; width: 220px; }
        .panel.minimized .main-body { display: none; }

        .header { 
            background: var(--primary); 
            height: 26px; 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            padding: 0 10px; 
            color: white; 
            font-size: 11px; 
            font-weight: bold; 
        }

        .main-body { display: flex; padding: 12px; gap: 12px; min-height: 190px; }

        /* SIDEBAR */
        .sidebar { display: flex; flex-direction: column; gap: 6px; }
        .side-btn { 
            width: 55px; height: 38px; background: var(--primary-dark); 
            border-radius: 10px; display: flex; justify-content: center; 
            align-items: center; cursor: pointer; font-size: 16px; color: white; transition: 0.3s; 
        }
        .side-btn.active { background: var(--primary); box-shadow: 0 0 10px var(--primary); }

        /* CONTEÚDO */
        .content-area { flex: 1; display: none; flex-direction: column; gap: 10px; }
        .content-area.active { display: flex; }
        .top-divider { height: 1px; background: linear-gradient(90deg, var(--primary), transparent); margin-bottom: 5px; }

        /* OPÇÕES */
        .row { display: flex; align-items: center; gap: 15px; }
        .option { display: flex; align-items: center; gap: 8px; color: white; font-size: 11px; cursor: pointer; }
        .box { width: 22px; height: 22px; background: #1a1a1a; border-radius: 4px; border: 1px solid #333; flex-shrink: 0; }
        .option.active .box { background: var(--primary); border-color: #fff; box-shadow: 0 0 8px var(--primary); }

        .fov-bar { background: #181818; border-radius: 4px; height: 26px; width: 110px; display: flex; align-items: center; position: relative; }
        input[type=range] { width: 100%; appearance: none; background: transparent; z-index: 2; cursor: pointer; }
        input[type=range]::-webkit-slider-thumb { appearance: none; width: 20px; height: 20px; background: var(--primary); border-radius: 4px; }
        
        .section-label { color: white; font-size: 11px; margin-top: 2px; }
        .warning-label { color: #ffff00; font-size: 11px; font-weight: bold; margin-bottom: 2px; }

        .split { display: flex; gap: 10px; }
        .col { display: flex; flex-direction: column; gap: 8px; flex: 1; }

        /* LOGIN */
        .key-container {
            width: 320px; background: var(--bg); border: 1px solid #1a1a1a; border-radius: 8px; padding: 20px; text-align: center;
        }
        .key-input { width: 100%; padding: 12px; background: #141414; border: 1px solid #333; border-radius: 6px; color: white; margin-bottom: 12px; outline: none; text-align: center; }
        .key-btn { width: 100%; padding: 10px; background: var(--primary); border: none; border-radius: 6px; color: white; font-weight: bold; cursor: pointer; }

        /* CÍRCULO DO FOV INDEPENDENTE */
        #fov-circle { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); border: 2px solid var(--primary); border-radius: 50%; pointer-events: none; display: none; z-index: 1; }
    </style>
</head>
<body>

<div id="fov-circle"></div>

<div class="key-container" id="key-screen">
    <div style="color:var(--primary); font-weight:bold; margin-bottom:15px;">PAINEL LLOW ANDROID V1</div>
    <input type="text" class="key-input" id="key-field" placeholder="DIGITE A KEY">
    <button class="key-btn" onclick="startLogin()">ENTRAR</button>
</div>

<div id="loader-screen">
    <p style="color: var(--primary); font-weight: bold; font-size: 12px;">CARREGANDO...</p>
    <div class="loader-bar"><div class="loader-progress" id="progress"></div></div>
</div>

<div class="panel" id="main-panel">
    <div class="header" id="panel-header">
        <span onclick="toggleMin()" style="cursor:pointer; padding: 0 5px;">▼</span>
        <span>PAINEL LLOW ANDROID V1</span>
        <span onclick="location.reload()" style="cursor:pointer; padding: 0 5px;">✕</span>
    </div>

    <div class="main-body" id="panel-body">
        <div class="sidebar">
            <div class="side-btn active" onclick="showTab(0, this)">🎯</div>
            <div class="side-btn" onclick="showTab(1, this)">👁️</div>
            <div class="side-btn" onclick="showTab(2, this)">⚙️</div>
            <div class="side-btn" onclick="showTab(3, this)">📇</div>
        </div>

        <!-- ABA 1 -->
        <div class="content-area active" id="tab-0">
            <div class="top-divider"></div>
            <div class="row">
                <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Aimbot Auxilio</div>
                <div class="option" id="aimfov-toggle" onclick="toggleAimfov()"><div class="box"></div> Aimfov</div>
            </div>

            <div class="row" style="margin: 6px 0;">
                <div class="fov-bar">
                    <input type="range" min="50" max="400" value="0" oninput="updateFov(this.value)">
                </div>
                <span style="color:white; font-size:11px;" id="fov-num">0.0</span>
                <span style="color:white; font-size:11px;">Regular FOV</span>
            </div>

            <div class="section-label">Painel Trick</div>
            
            <div class="warning-label">⚠️ Option</div>
            <div class="row">
                <div class="option" id="opt-trick" onclick="exclusiveSelect('opt-trick')"><div class="box" style="border-radius:50%"></div> Aimtrick</div>
                <div class="option" id="opt-lock" onclick="exclusiveSelect('opt-lock')"><div class="box" style="border-radius:50%"></div> Aimlock</div>
            </div>
        </div>

        <!-- ABA 2 -->
        <div class="content-area" id="tab-1">
            <div class="top-divider"></div>
            <div class="split">
                <div class="col">
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Holograma Roxo</div>
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Holograma Amarelo</div>
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Holograma Vermelho</div>
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Holograma Verde</div>
                </div>
                <div class="col">
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Melhorar FPS</div>
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> 120 FPS</div>
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Reduzir Ping</div>
                    <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Norecoil auxílio</div>
                </div>
            </div>
        </div>

        <!-- ABA 3 -->
        <div class="content-area" id="tab-2">
            <div class="top-divider"></div>
            <div class="option" onclick="this.classList.toggle('active')"><div class="box"></div> Ativar bypass</div>
            <div style="color:white; font-size:11px; margin-top:5px; font-weight:bold;">
                tiktok do criador: <span style="color:var(--primary)">huzx77</span><br>
                Instagram do desenvolvedor: <span style="color:var(--primary)">ig.wz77</span>
            </div>
        </div>

        <!-- ABA 4 -->
        <div class="content-area" id="tab-3">
            <div class="top-divider"></div>
            <div style="color:white; font-size:11px; font-weight:bold;">
                CRIADOR: <span style="color:var(--primary)">@ig.wx7</span><br>
                VERSÃO: <span style="color:var(--primary)">1.1</span>
            </div>
        </div>
    </div>
</div>

<script>
    const panel = document.getElementById('main-panel');
    const circle = document.getElementById('fov-circle');
    const loader = document.getElementById('loader-screen');
    let aimfovActive = false;

    function startLogin() {
        const key = document.getElementById('key-field').value;
        if(key === "keyhs") {
            document.getElementById('key-screen').style.display = "none";
            loader.style.display = "flex";
            let p = 0;
            let i = setInterval(() => {
                p += 5;
                document.getElementById('progress').style.width = p + "%";
                if(p >= 100) { 
                    clearInterval(i); 
                    loader.style.display = "none"; 
                    panel.style.display = "block"; 
                    makeDraggable(panel);
                }
            }, 50);
        }
    }

    // MINIMIZAR NÃO ALTERA MAIS O CÍRCULO
    function toggleMin() { 
        panel.classList.toggle('minimized'); 
        
        if (panel.classList.contains('minimized')) {
            document.querySelector('#panel-header span:first-child').innerText = '▲';
        } else {
            document.querySelector('#panel-header span:first-child').innerText = '▼';
        }
    }

    function showTab(idx, btn) {
        document.querySelectorAll('.content-area').forEach(t => t.classList.remove('active'));
        document.querySelectorAll('.sidebar .side-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('tab-' + idx).classList.add('active');
        btn.classList.add('active');
    }

    function exclusiveSelect(id) {
        document.getElementById('opt-trick').classList.remove('active');
        document.getElementById('opt-lock').classList.remove('active');
        document.getElementById(id).classList.add('active');
    }

    function toggleAimfov() {
        const btn = document.getElementById('aimfov-toggle');
        btn.classList.toggle('active');
        aimfovActive = btn.classList.contains('active');
        circle.style.display = aimfovActive ? 'block' : 'none';
    }

    function updateFov(v) {
        document.getElementById('fov-num').innerText = (v/10).toFixed(1);
        circle.style.width = v + "px"; 
        circle.style.height = v + "px";
    }

    function makeDraggable(elmnt) {
        let pos1 = 0, pos2 = 0, pos3 = 0, pos4 = 0;
        
        elmnt.onmousedown = dragMouseDown;
        elmnt.ontouchstart = dragMouseDown;

        function dragMouseDown(e) {
            e = e || window.event;
            
            if (e.target.tagName === 'SPAN' || 
                e.target.classList.contains('side-btn') || 
                e.target.classList.contains('box') || 
                e.target.tagName === 'INPUT') {
                return; 
            }
            
            if (e.type === 'touchstart') {
                pos3 = e.touches[0].clientX;
                pos4 = e.touches[0].clientY;
                document.ontouchend = closeDragElement;
                document.ontouchmove = elementDrag;
            } else {
                e.preventDefault();
                pos3 = e.clientX;
                pos4 = e.clientY;
                document.onmouseup = closeDragElement;
                document.onmousemove = elementDrag;
            }
        }

        function elementDrag(e) {
            e = e || window.event;
            let clientX, clientY;
            
            if (e.type === 'touchmove') {
                clientX = e.touches[0].clientX;
                clientY = e.touches[0].clientY;
            } else {
                clientX = e.clientX;
                clientY = e.clientY;
            }
            
            pos1 = pos3 - clientX;
            pos2 = pos4 - clientY;
            pos3 = clientX;
            pos4 = clientY;
            
            elmnt.style.top = (elmnt.offsetTop - pos2) + "px";
            elmnt.style.left = (elmnt.offsetLeft - pos1) + "px";
        }

        function closeDragElement() {
            document.onmouseup = null;
            document.onmousemove = null;
            document.ontouchend = null;
            document.ontouchmove = null;
        }
    }
</script>

</body>
</html>
