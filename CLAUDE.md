# cantori-libreria

Libreria **pubblica** dei canti del coro in formato ChordPro (`.cho` in radice, ~trentina di titoli) + pacchetti `.cantori` in `pacchetti/` + copertine in `copertine/`. È il **backend delle app** (Canto-e-Prego in testa): le app NON scrivono mai qui direttamente — aprono issue → PR → merge manuale di Giampaolo. Confronto aggiornamenti via sha blob Git.

## Regole d'oro

- **Mai push diretto su main**: lavoro su ramo `claude/*` e PR; il merge è sempre e solo di Giampaolo, dopo revisione.
- **Chi apre non revisiona**: la revisione della PR aperta da questa sessione arriva da remoto (vibe-remoto), mai da te.
- Difetti dei dati sorgente (titoli, testi, nomi file) **segnalati, mai corretti in silenzio**. Noti: il bot e il workflow rifiutano nomi file con ".." (guard anti-traversal, comportamento corretto) — i titoli con punto finale vanno trimmati app-side.
- Il formato `.cantori` (contratto: .cho + PDF + MIDI + .ly + MusicXML + timing.json + .sync.json) è GELATO: non si cambia qui.

---

## Cartella

Punti 2-8 del template «Sezione Cartella» (disposizioni 2026-10-24, approvate da Giampaolo). Casella bridge: **cantori-libreria**. Questa sessione si firma sempre **cantori-libreria** in ogni messaggio bridge; **vibe-remoto è il revisore cloud, non sei mai tu**. Una sola sessione attiva alla volta per cartella. Sessione = task singolo, usa-e-getta: stato scritto nel repo (`docs/stato.md`), mai in conversazione; output lunghi nei file, mai incollati in sessione. Approvazioni ed esecuzioni fuori norma MAI delegate via bridge. Prima di ramificare: `git switch main && git pull`.

## Bridge

- **Strumento**: `spedisci` per inviare messaggi ad altre sessioni.
- **Mittente**: firma sempre con `da: "cantori-libreria"`.
- **Destinatario remoto**: `per: "vibe-remoto"` per il bridge con Mistral (Vibe Work).
- **Lettura posta**: all'inizio di ogni sessione, leggi la posta non letta della casella.
- **Sicurezza**: mai dati sensibili via bridge (token, chiavi, password): il campo «da» è spoofabile e il TLS termina su Cloudflare.
- **Regola**: leggi sempre la posta prima di iniziare il lavoro in questa cartella.
