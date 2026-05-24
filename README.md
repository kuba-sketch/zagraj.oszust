# Jak oszukiwać w zagraj.my/speedrun
Stworzene z pomocą Gemini!!!!
otwieramy konsole  (F12) wklejamy kod poniżej ze zmienionym czasem i nickiem, dopiero potem startujemy gre i rozwizaujemy jedno pytanie.
```js
(async () => {
    // --- KONFIGURACJA WYNIKU ---
    const twójNick = "NICK"; // <-- Tutaj wpisz swój nick
    const czasWsekundach = 28.50;   // <-- Tutaj wpisz swój czas (np. 28.50 sekundy)
    // ---------------------------

    console.log("Wysyłam bezpośredni wynik do tabeli liderów...");

    try {
        const res = await fetch("/api/speedrun/results", {
            method: "POST",
            headers: { 
                "Content-Type": "application/json" 
            },
            body: JSON.stringify({
                username: twójNick.trim(),
                time_seconds: czasWsekundach,
                wrong_answers: 0
            })
        });

        if (res.ok) {
            console.log("🎉 SUKCES! Wynik został bezpośrednio zapisany.");
            alert(`Gotowe! Nick "${twójNick}" został dodany do rankingu z czasem ${czasWsekundach}s.`);
        } else {
            const err = await res.json().catch(() => ({}));
            console.error("❌ Serwer odrzucił żądanie:", err.detail || "Nieznany błąd walidacji.");
        }
    } catch (err) {
        console.error("❌ Błąd sieciowy podczas wysyłania wyniku:", err);
    }
})();```
