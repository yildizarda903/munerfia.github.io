<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>FIA RACE CONTROL - MUNCOMMAND 2022</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Titillium+Web:ital,wght@0,400;0,700;0,900;1,700&display=swap');
        :root { --f1-red: #e10600; --f1-blue: #0600ef; --f1-silver: #00d2be; --f1-dark: #15151e; }
        body { background: #000; color: white; font-family: 'Titillium Web', sans-serif; margin: 0; height: 100vh; overflow: hidden; text-transform: uppercase; }
        
        /* --- VISUAL INTERFACE (F1 TV) --- */
        .live-tag { position: absolute; top: 20px; right: 40px; background: var(--f1-red); padding: 5px 15px; font-weight: 900; font-style: italic; border-radius: 3px; z-index: 10; }
        
        /* Sol Tower */
        .tower { position: absolute; top: 40px; left: 40px; width: 300px; background: rgba(0,0,0,0.8); border-radius: 5px; border-top: 3px solid var(--f1-red); }
        .tower-item { display: flex; align-items: center; padding: 10px; border-bottom: 1px solid #333; font-weight: 700; font-size: 14px; }
        .active-speaker { background: rgba(225, 6, 0, 0.3); box-shadow: inset 5px 0 0 white; }
        .team-color { width: 5px; height: 20px; margin-right: 10px; }
        
        /* Telemetry Sayaç */
        .telemetry { position: absolute; bottom: 150px; right: 50px; text-align: right; }
        .timer { font-size: 120px; font-weight: 900; font-style: italic; line-height: 1; text-shadow: 0 0 20px var(--f1-red); }
        .session-info { font-size: 20px; color: #aaa; margin-top: 10px; border-right: 5px solid var(--f1-red); padding-right: 15px; }

        /* Kriz Bandı */
        .rc-overlay { position: absolute; bottom: 40px; left: 360px; right: 360px; transform: translateY(200px); transition: 0.5s; z-index: 20; }
        .rc-overlay.active { transform: translateY(0); }
        .rc-content { background: #fff; color: #000; padding: 20px; font-size: 22px; font-weight: 900; border-left: 15px solid var(--f1-red); border-radius: 5px; display: flex; align-items: center; }
        
        /* Oylama Ekranı (Stewards Inquiry) */
        #vote-screen { position: absolute; top: 20%; left: 35%; width: 30%; background: rgba(0,0,0,0.9); border: 2px solid white; padding: 30px; display: none; text-align: center; border-radius: 15px; }
        .vote-count { display: flex; justify-content: space-around; margin-top: 20px; font-size: 30px; }

        /* --- ADMIN PANEL (Yalnızca Yönetici Görür) --- */
        .admin-panel { position: fixed; bottom: 0; left: 0; width: 100%; background: #222; padding: 20px; display: flex; flex-wrap: wrap; gap: 10px; z-index: 100; border-top: 2px solid #444; transform: translateY(85%); transition: 0.3s; opacity: 0.2; }
        .admin-panel:hover { transform: translateY(0); opacity: 1; }
        .admin-panel button { background: var(--f1-red); color: white; border: none; padding: 10px; font-weight: 900; cursor: pointer; border-radius: 3px; }
        .admin-panel input { padding: 10px; border-radius: 3px; border: none; }
        .section { border-right: 1px solid #444; padding: 0 15px; }
    </style>
</head>
<body>

    <div class="live-tag">● LIVE - FIA WORLD FEED</div>

    <div class="tower" id="speaker-list">
        <div style="padding:15px; font-weight:900; background:black;">TIMING TOWER</div>
        </div>

    <div class="telemetry">
        <div class="timer" id="main-timer">00:00</div>
        <div class="session-info" id="session-label">WAITING FOR SESSION...</div>
    </div>

    <div class="rc-overlay" id="rc-banner">
        <div style="background:var(--f1-red); color:white; display:inline-block; padding:5px 15px; font-weight:900;">RACE CONTROL</div>
        <div class="rc-content" id="rc-text">SESSION PREPARING...</div>
    </div>

    <div id="vote-screen">
        <h2 style="color:var(--f1-red)">STEWARDS INQUIRY (VOTING)</h2>
        <p id="vote-topic">SUBMITTED DIRECTIVE 1.0</p>
        <div class="vote-count">
            <div style="color:#00ff00">YES: <span id="v-yes">0</span></div>
            <div style="color:#ff0000">NO: <span id="v-no">0</span></div>
            <div style="color:#ffff00">ABS: <span id="v-abs">0</span></div>
        </div>
    </div>

    <div class="admin-panel">
        <div class="section">
            <strong>⏱️ SAYAÇ</strong><br>
            <input type="number" id="min-input" placeholder="Dk" style="width:50px">
            <button onclick="controlTimer('start')">BAŞLAT</button>
            <button onclick="controlTimer('stop')"
