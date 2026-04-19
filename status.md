<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>StatusEkipy.pl</title>
    <style>
        :root {
            --bg: #11111b; --card: #1e1e2e; --text: #cdd6f4;
            --blue: #89b4fa; --green: #a6e3a1; --red: #f38ba8; --yellow: #f9e2af;
        }
        body { font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); display: flex; justify-content: center; padding: 20px; }
        .app { background: var(--card); padding: 25px; border-radius: 15px; width: 100%; max-width: 400px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        h1 { text-align: center; color: var(--blue); margin-bottom: 30px; }
        .user-row { display: flex; justify-content: space-between; align-items: center; padding: 12px; margin-bottom: 8px; background: rgba(255,255,255,0.05); border-radius: 10px; }
        .status-dot { width: 12px; height: 12px; border-radius: 50%; display: inline-block; margin-right: 10px; }
        
        /* Kolory dla statusów */
        .status-w-grze { background: var(--green); box-shadow: 0 0 8px var(--green); }
        .status-zajety { background: var(--red); }
        .status-praca { background: var(--blue); }
        .status-przerwa { background: var(--yellow); }
        .status-spanie { background: #94e2d5; }
        .status-jedzenie { background: #fab387; }

        .update-box { margin-top: 30px; border-top: 1px solid #45475a; padding-top: 20px; }
        select, button { width: 100%; padding: 12px; margin-top: 10px; border-radius: 8px; border: none; font-size: 16px; cursor: pointer; }
        select { background: #313244; color: white; }
        button { background: var(--blue); color: #11111b; font-weight: bold; transition: 0.3s; }
        button:hover { opacity: 0.8; }
    </style>
</head>
<body>

<div class="app">
    <h1>statusekipy.pl 🎮</h1>
    <div id="list">Wczytywanie statusów...</div>

    <div class="update-box">
        <h3>Zmień swój status:</h3>
        <select id="user-select">
            <option value="Grzegorz Floryda">Grzegorz Floryda</option>
            <option value="Mental">Mental</option>
            <option value="Ozempeak">Ozempeak</option>
            <option value="Jarek">Jarek</option>
            <option value="Mamed">Mamed</option>
            <option value="Satelita">Satelita</option>
        </select>

        <select id="status-select">
            <option value="w-grze">🟢 W grze</option>
            <option value="zajety">🔴 Zajęty</option>
            <option value="praca">🔵 Praca</option>
            <option value="przerwa">🟡 Przerwa</option>
            <option value="jedzenie">🍕 Jedzenie</option>
            <option value="spanie">😴 Spanie / AFK</option>
        </select>
        <button onclick="updateStatus()">Aktualizuj status</button>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.17.1/firebase-app.js";
    import { getDatabase, ref, set, onValue } from "https://www.gstatic.com/firebasejs/9.17.1/firebase-database.js";

    // Skonfigurowane na podstawie Twoich danych
    const firebaseConfig = {
        apiKey: "AIzaSyCuqxR8adEUhqeRtt7CJCTWHYux1MoRLaU",
        authDomain: "statusekipy.firebaseapp.com",
        // Dodałem poniższą linijkę, żeby baza danych wiedziała, gdzie się łączyć
        databaseURL: "https://statusekipy-default-rtdb.europe-west1.firebasedatabase.app",
        projectId: "statusekipy",
        storageBucket: "statusekipy.firebasestorage.app",
        messagingSenderId: "410345038362",
        appId: "1:410345038362:web:92786231b762fa82aca7a3",
        measurementId: "G-YQFYRGQTSN"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    // Funkcja aktualizacji w bazie
    window.updateStatus = () => {
        const name = document.getElementById('user-select').value;
        const status = document.getElementById('status-select').value;
        set(ref(db, 'users/' + name), { status: status });
    };

    // Słuchanie zmian w bazie na żywo
    onValue(ref(db, 'users'), (snapshot) => {
        const data = snapshot.val();
        const listDiv = document.getElementById('list');
        listDiv.innerHTML = '';
        
        const names = ["Grzegorz Floryda", "Mental", "Ozempeak", "Jarek", "Mamed", "Satelita"];
        
        names.forEach(name => {
            const userStatus = data && data[name] ? data[name].status : 'spanie'; // Domyślnie AFK
            const statusLabel = userStatus.replace('-', ' ');
            
            listDiv.innerHTML += `
                <div class="user-row">
                    <div><span class="status-dot status-${userStatus}"></span><strong>${name}</strong></div>
                    <small style="text-transform: capitalize;">${statusLabel}</small>
                </div>
            `;
        });
    });
</script>

</body>
</html>
