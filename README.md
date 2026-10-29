# Power Alert ESP32 — canale di aggiornamento

Questo repository **non contiene il progetto**: contiene solo quello che un
apparecchio Power Alert deve poter scaricare per aggiornarsi da solo.

È pubblico per una ragione sola, e tecnica: l'ESP32 scarica con una `GET`
semplice, **senza intestazione di autorizzazione**. Su GitHub i file di un
repository privato — compresi gli allegati delle release — richiedono un
token, e un token dentro un firmware finisce in chiaro nel `.bin`: chiunque lo
estragga leggerebbe tutto, e revocarlo vorrebbe dire rifare l'aggiornamento su
ogni apparecchio già installato.

Il progetto (schemi, sbroglio, distinta, documenti) sta in un repository
privato a parte.

## Che cosa c'è

| | |
|---|---|
| `aggiornamento.json` | il manifest che l'apparecchio legge, ogni sei ore |
| *Releases* | i binari, uno per versione, come allegati |

## Il manifest

```json
{
  "version": "2.1.0",
  "url": "https://github.com/danielemichelotti/poweralert-esp32-ota/releases/download/v2.1.0/power_monitor_esp32.ino.bin",
  "notes": "Che cosa cambia, in una riga.",
  "size": 1234567,
  "sha256": "…"
}
```

L'apparecchio confronta `version` con la propria, e se è più nuova scarica
`url`. `notes` finisce nel messaggio Telegram e nella pagina web, quindi si
scrive per chi legge, non per chi sviluppa.

⛔ `sha256` **non è ancora verificato dal firmware**: c'è perché il controllo
si possa aggiungere senza cambiare il formato, e perché chi scarica a mano
possa confrontarlo. L'integrità del download la garantisce intanto l'ESP32
stesso, che rifiuta un'immagine troncata o non valida.

## L'indirizzo da mettere nell'apparecchio

```
https://raw.githubusercontent.com/danielemichelotti/poweralert-esp32-ota/main/aggiornamento.json
```

## Come si pubblica una versione

Dal repository del progetto:

```bash
python strumenti/pubblica_firmware.py percorso/del/power_monitor_esp32.ino.bin
```

Lo script legge la versione da `config.h` — che è l'unica fonte di verità —
crea la release, carica il binario e riscrive questo manifest. Non si modifica
`aggiornamento.json` a mano: una versione scritta qui che non esiste come
release manda ogni apparecchio a scaricare un indirizzo che risponde 404.
