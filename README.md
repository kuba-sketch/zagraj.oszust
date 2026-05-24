# Jak oszukiwać w zagraj.my/speedrun
Stworzene z pomocą Gemini!!!!
otwieramy konsole  (F12) wklejamy kod poniżej ze zmienionym czasem i nickiem, dopiero potem startujemy gre i rozwizaujemy jedno pytanie.
```js
(async () => {
    // --- KONFIGURACJA WYNIKU ---
    const twójNick = "TWOJNICK"; // <-- Tutaj zmień swój nick
    const czasWsekundach = 3;   // <-- Tutaj zmień czas gry
    // ---------------------------

    const prawidlowePoziomy = [
        { level: 1, id: 198 }, { level: 1, id: 114 }, { level: 1, id: 226 }, { level: 1, id: 258 }, 
        { level: 1, id: 186 }, { level: 1, id: 162 }, { level: 1, id: 140 }, { level: 1, id: 78 },
        { level: 2, id: 207 }, { level: 2, id: 118 }, { level: 2, id: 233 }, { level: 2, id: 262 }, 
        { level: 2, id: 188 }, { level: 2, id: 166 }, { level: 2, id: 143 },
        { level: 3, id: 211 }, { level: 3, id: 121 }, { level: 3, id: 244 }, { level: 3, id: 269 }, 
        { level: 3, id: 190 }, { level: 3, id: 181 }, { level: 3, id: 147 }, { level: 3, id: 136 },
        { level: 4, id: 215 }, { level: 4, id: 111 }, { level: 4, id: 249 }, { level: 4, id: 275 }, 
        { level: 4, id: 149 }, { level: 4, id: 180 }, { level: 4, id: 196 }
    ];

    console.log("📡 Sniffer uruchomiony! Oczekuję na kliknięcie START przez Ciebie...");

    // Podmieniamy oryginalny fetch, aby "podsłuchać" ID gry (ve)
    const oryginalnyFetch = window.fetch;
    window.fetch = async function(...args) {
        const url = args[0];
        
        // Wyłapujemy moment, gdy gra pyta o poziom lub wysyła event
        if (typeof url === 'string' && (url.includes('/level-event') || url.includes('/levels'))) {
            try {
                // Jeśli to był POST z body, wyciągamy game_id
                if (args[1] && args[1].body) {
                    const bodyData = JSON.parse(args[1].body);
                    if (bodyData.game_id) {
                        odpalHax(bodyData.game_id);
                    }
                }
            } catch(e) {}
        }
        
        // Zwracamy normalne działanie gry, żeby nic się nie zepsuło
        return oryginalnyFetch.apply(this, args);
    };

    async function odpalHax(gameId) {
        // Przywracamy oryginalny fetch, żeby nie zapętlić skryptu
        window.fetch = oryginalnyFetch;
        console.log("🎯 Przechwycono ID gry: " + gameId);
        console.log("🚀 Automatyczne przechodzenie 30 poziomów...");

        for (const poz of prawidlowePoziomy) {
            try {
                await oryginalnyFetch("/api/speedrun/level-event", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        game_id: gameId,
                        level: poz.level,
                        level_id: poz.id,
                        passed: true
                    })
                });
            } catch (err) {}
        }

        console.log("💾 Wysyłanie końcowego rekordu do rankingu...");
        const res = await oryginalnyFetch("/api/speedrun/results", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({
                username: twójNick.trim(),
                time_seconds: czasWsekundach,
                wrong_answers: 0
            })
        });

        if (res.ok) {
            console.log("🎉 SUKCES! Gra została pomyślnie oszukana automatycznie!");
            alert(`Ukończono! Wynik dla ${twójNick} trafia do bazy.`);
        } else {
            console.error("❌ Serwer odrzucił zapis.");
        }
    }
})();```
