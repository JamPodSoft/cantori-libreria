# Rapporto di verifica strutturale — file ChordPro (.cho)

**Repo:** JamPodSoft/cantori-libreria (radice) · **Commit base:** b86b62d · **Data:** 2026-10-27
**Esecutore:** vibe-remoto (pilota eseguito da remoto via github_app)
**Natura del pilota:** verifica SOLO strutturale, read-only. Nessun file sorgente modificato. I difetti sono SEGNALATI, mai corretti (regola ecosistema: le correzioni toccano a chi possiede il brano, con revisione separata).

## 1. Scopo e metodo

Verifica sintattica dei file ChordPro in radice tramite checker a regole (regex, eseguito in sessione remota):

- direttive sconosciute o malformate (righe che non rispettano la forma `{nome: valore}`);
- alias deprecati dello standard ChordPro v6 ({soc}, {eoc}, {c}, {ci}, {sot}, {eot}…);
- direttive app-specific ({zoom-ipad}, {zoom-iphone}, {ipodid});
- quadre non bilanciate, accordi vuoti `[]`, possibili accordi malformati;
- solfeggio italiano nelle quadre ([RE], [SOL]…), righe di note senza quadre;
- caratteri invisibili (zero-width), TAB, CRLF, BOM;
- confronto deduplicazione: hash del corpo (solo righe di testo puro, senza accordi né direttive).

## 2. Copertura

- File .cho in radice censiti: **98**.
- Verificati: **97**.
- **Non verificabile da remoto: 1** — `Alleluia (Passeranna in cieli.cho`: il nome contiene una parentesi non chiusa e non è raggiungibile via API GitHub (percorso non trovato). Da verificare in locale.
- Limitazione dichiarata: `pacchetti/` contiene 2 file `.cantori` (archivi zip) non ispezionabili da remoto: la misura di "stale" (contenuto dei pacchetti vs .cho) non è stata eseguita. La deduplicazione via hash è stata fatta solo sui .cho di radice.

## 3. Conformità

- **File puliti (nessun difetto rilevato): 83 su 97 (~86%).**
- **File con difetti bloccanti (sintassi che rompe il rendering): 7.**
- **File con anomalie minori/da segnalare: ~10** (oltre ai bloccanti).
- Alias deprecati ({soc}/{eoc}/{c} e simili): presenti nella grande maggioranza dei file (forma storica accettata dall'app ma deprecata dallo standard v6; nessuna azione obbligatoria).
- Direttive app ({zoom-ipad}, {zoom-iphone}, {ipodid}): presenti in più file, funzionanti con Cantori ma non standard.

## 4. Difetti bloccanti (7 file)

| File | Difetto |
|---|---|
| **Agnello di Dio (Buttazzo).cho** | **DUPLICATO CONFERMATO**: il corpo (hash 941838027) è identico a **Adoro Te.cho**. Il file non contiene il brano dichiarato dal nome. Difetto più grave della libreria. |
| Lo Spirito del Signore - Frisina.cho | r23: quadre non bilanciate 3/4 (`[C]cuore Dm]per` — manca `[` prima di Dm). |
| Buon Natale in allegria.cho | r34: direttiva `{c: strum:}` con contenuto extra sulla riga (malformata). |
| Chi ci separera'.cho | accordo malformato `[AmMI-]`. |
| Le tue mani.cho | accordo malformato `[D07]`. |
| Acqua siamo noi.cho | accordo malformato `[Cmj7]`. |
| Servo per amore.cho | accordi spezzati dentro le parole nel ritornello (`cro[G]oce` → "crooce", `amo[G]o re` → "amoo re"): il testo renderizzato è corrotto. |

## 5. Anomalie minori e refusi segnalati (non corretti)

- Magnificat.cho: `{artist }` senza valore.
- Eccomi - Frisina.cho, La vera gioia - Frisina.cho: caratteri zero-width (invisibili) nel testo.
- SuAlidAquila or.cho: ultima strofa al futuro ("ti rialzerò… ti reggerò… vivrai") mentre il resto del brano è alla terza persona ("ti rialzerà"); probabile refuso da verificare contro l'originale.
- Symbolum 80.cho: r6 `oltre Ia [C]speranza` — "Ia" con i maiuscolo al posto di "la"; usa `{ci:}` per i ritornelli.
- Sara Natale se.cho, Se m'accogli.cho, Sono qui a lodarti.cho: `{Artist: }` vuoto.
- Te lodiamo Trinità.cho: solfeggio `[(LA)]` in una quadra; "Trinita"/"adoriamo" senza accenti resi con accordi in mezzo (verificare resa).
- Resurrezione.cho: r finale `T[D]e[G]e….` → resa "Tee", probabile refuso di syllabification.
- Refusi testo segnalati nei controlli: "i suoii/miserri/suaa" in Acclamate (Sal. 99); "tTu" in E sono solo un uomo.
- Nomi file anomali (nessun impatto sul contenuto, ma creano problemi di accesso/API):
  - `Gloria in  excelsis deo.cho` (doppio spazio);
  - `Benedici ll Signore...cho` (typo nel nome);
  - `Ecco quel quel che...cho` (parola duplicata nel nome);
  - `Alleluia (Passeranna in cieli.cho` (parentesi aperta non chiusa → file non raggiungibile da remoto).

## 6. Deduplicazione (hash corpo)

Confronto su tutti i 97 file verificati: **unica coppia duplicata** = "Agnello di Dio (Buttazzo).cho" ≡ "Adoro Te.cho" (vedi §4). Nessun altro file con corpo identico.

Nota tecnica: hash = 0 significa "nessuna riga di solo testo" (brano quasi interamente sotto accordi): non confrontabile; solo hash ≠ 0 uguali indicano duplicato.

## 7. Proposte (nessuna azione eseguita)

1. **Bot dedup #71/#72**: automatizzare il confronto hash del corpo in CI a ogni push (o come sotto-servizio locale) per intercettare nuovi duplicati come quello di §4.
2. Ripristinare il contenuto corretto di "Agnello di Dio (Buttazzo).cho" (decisione e fonti: Giampaolo).
3. Normalizzare i 4 nomi file anomali di §5 (rename con git mv, in PR dedicata).
4. Migrare gradualmente gli alias {soc}/{eoc} verso {start_of_chorus}/{end_of_chorus} solo se l'app target conferma la compatibilità.

## 8. Limiti del pilota

- Verifica da remoto via API GitHub: pacchetti .cantori non ispezionati (§2); 1 file non raggiungibile (§2).
- Checker regex-based: alcuni falsi positivi/negativi possibili sugli accordi malformati; i casi citati sono stati controverificati a mano sul testo dei file.
- Nessuna verifica musicale (tonalità, correttezza armonica) né ortografica sistematica: i refusi citati sono emergenze occasionali.

---
*vibe-remoto — ramo claude/report-chordpro. Il merge spetta a Giampaolo dopo revisione (per regola ecosistema, la revisione di una PR aperta da vibe-remoto arriva dal lato locale).*
