# SOSPIRO web (iPhone / Bluefy)

Webapp a file singolo (`index.html`, nessuna dipendenza esterna) che parla con la scheda via **Web Bluetooth** (Nordic UART, dispositivo `SOSPIRO2`) con lo stesso protocollo JSON della GUI desktop.

Funziona con: **Bluefy** (iPhone/iPad), Chrome/Edge su desktop e Android. Non funziona con Safari su iOS (niente Web Bluetooth).

## Requisito: HTTPS

Web Bluetooth funziona solo in un contesto sicuro: `https://...` oppure `http://localhost`. Un semplice `http://100.x.y.z:8765` via Tailscale **non basta**.

## Ospitarla dal PC con Tailscale

1. Nella console admin di Tailscale abilita MagicDNS e HTTPS Certificates (una volta sola).
2. Avvia il server statico, dalla cartella `web/`:

   ```
   python -m http.server 8765 --bind 127.0.0.1
   ```

3. Pubblica in HTTPS sulla tailnet (solo i tuoi dispositivi la vedono):

   ```
   tailscale serve --bg --https=443 http://127.0.0.1:8765
   ```

4. Sull'iPhone (Tailscale attivo) apri in Bluefy l'indirizzo `https://<nome-pc>.<tailnet>.ts.net/` mostrato da `tailscale serve status`.

Per interrompere: `tailscale serve reset`.

Alternativa senza Tailscale: GitHub Pages (HTTPS pubblico, la pagina non contiene dati sensibili).

## Note d'uso

- Il collegamento BLE parte solo da un tocco su "Connetti". Tieni Bluefy in primo piano: se lo schermo si blocca o cambi app la connessione cade (la pagina chiede di tenere lo schermo acceso dove supportato).
- Su iOS lo stream PPG va tenuto basso (10 sps).
- Alla connessione la pagina confronta l'ora del dispositivo con quella del telefono e chiede se correggerla. Invia un heartbeat ogni 2 s.
- La registrazione CSV viene scaricata alla fine ("Ferma e scarica").
