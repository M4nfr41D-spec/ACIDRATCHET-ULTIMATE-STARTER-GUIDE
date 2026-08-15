# ARBEITSANWEISUNG — ACIDRATCHET (für Claude Code)

**Repo:** https://github.com/M4nfr41D-spec/ACIDRATCHET-ULTIMATE-STARTER-GUIDE
**Live (GitHub Pages):** https://m4nfr41d-spec.github.io/ACIDRATCHET-ULTIMATE-STARTER-GUIDE/
**Autor:** Manfred Foissner (M4n@R4TCh3T) · Zielgeräte: iPhone / iPad, Safari

---

## 0. Erstes: Bestand aufnehmen, nicht raten

Bevor irgendetwas geändert wird:

```bash
find . -type f -not -path './.git/*' | sort
git log --oneline -15
```

Dann jede HTML/JS-Datei einmal öffnen und prüfen:
- welche `localStorage`-Keys geschrieben/gelesen werden
- welche Datei welche andere per `href` / `window.open` verlinkt
- ob `acidratchet-fx-core.js` überall dort eingebunden ist, wo `ARFX.*` benutzt wird

Es existiert lokal ein **ZIP mit der vollständigen FX-Ordnerstruktur**, das umfangreicher ist als das, was auf Pages liegt. Falls im Repo nicht enthalten: bei Manfred anfordern, bevor am FX-Teil gearbeitet wird.

---

## 1. Feste Regeln (gelten immer)

1. **Meisterwerk, no regression.** Änderungen sind **additiv**. DSP und Scheduler in `index.html` werden nicht angefasst — nicht „nebenbei aufgeräumt", nicht „nur kurz refactored".
2. **Nur betroffene Stellen ändern.** Keine Datei komplett neu schreiben.
3. **Dateinamen: keine Punkte außer der Endung.** iOS liest `v5.5.html` als Datei `v5` mit Endung `.5` und kann sie nicht öffnen. Richtig: `v5_5`.
4. **Single-File-Prinzip** für die Tools bleibt: eine HTML = ein lauffähiges Werkzeug. Geteilt wird nur `acidratchet-fx-core.js`.
5. **Vorm Behaupten messen.** `node --check` auf extrahierte Scripts, DOM-ID-Kollisionen prüfen, danach erst „geht".
6. **Alle Dateien müssen auf derselben Origin liegen** — die Übergabe zwischen den Tools läuft komplett über `localStorage`.

---

## 2. Bekannter technischer Stand (gemessen am Pages-Deploy, Stand 12.08.2026)

**Dateien:**
| Datei | Rolle |
|---|---|
| `index.html` | MF79 Synth, 128 Bänke, Drums, Voice B, Song-Kette, WAV-Recorder |
| `ACIDRATCHET_TD3MO_TRANSLATOR.html` | Pattern-Editor, 80-Classics-Library, GarageBand-MIDI-Export |
| `ACIDRATCHET_FX_LAB.html` | FX-Preset-Editor |
| `acidratchet-fx-core.js` | gemeinsame FX-DSP + Preset-Contract (`ARFX`), v0.1.0 |

**localStorage-Keys:**
`ar_banks` `{banks[128], names[128], cur}` · `ar_chain` `{chain:[idx], on}` · `ar_drums` · `ar_voiceB` · `ar_glue` · `ar_handoff` `{bank, patternId, sentAt}` · dazu `ARFX.HANDOFF_KEY` / `ARFX.ACTIVE_KEY` / `ARFX.SAMPLE_SLOT_KEY`.

**Bank-Objekt** (`snapPat()` in `index.html`):
```js
{ steps:[{on,note,accent,slide,rat}]×16, len, drums:{k,s,h,o}, b:[…VoiceB], bon }
```
- `drums` sind 16-Bit-Masken, Bit 0 = Step 1. Beispiel: `4369` = Kick auf 1/5/9/13.
- Notennummern: MIDI, `36` = C2, Default/Basis `33` = A1.
- `drums`, `b`, `bon` werden **nur übernommen, wenn BANK-LINK aktiv ist** (`applyBankExtras`).
- **Klangregler (Cutoff, Reso, Drive …) werden nicht pro Bank gespeichert.**

**Bankbelegung:** 1–48 Werksbibliothek (`seedLibrary`) · 49–128 die 80 Classics aus dem Translator.

---

## 3. Aufgaben

### 3.1 Verlinkung der Tools untereinander
Aktuell einseitig: `index.html` → Translator + FX LAB; Translator → `index.html`; FX LAB → `index.html`. **Es fehlt:** Translator ↔ FX LAB, und eine einheitliche Navigation.

Gemeinsame Nav-Leiste in **alle** Tool-Dateien direkt nach `<body>` einsetzen, in der jeweiligen Datei die eigene Seite mit `class="on"` markieren. Vorlage liegt als `NAV_SNIPPET.html` bei (aus dem Chat). Sticky, horizontal scrollbar, damit sie auf dem iPhone nicht umbricht.

Danach prüfen: keine CSS-Klassenkollision mit bestehenden Styles (`.nav`, `.arnav`), keine doppelten DOM-IDs.

### 3.2 SONGBOOK integrieren
`ACIDRATCHET_SONGBOOK.html` (aus dem Chat, liegt bei) ins Repo-Root legen. Enthält 6 Sets à 5 zusammengehörende Patterns — INTRO · BUILD · DRIVE · CLIMAX · OUTRO, mit passenden Drums — und schreibt per Klick 5 aufeinanderfolgende Bänke plus `ar_chain`.

Zu klären beim Einbau:
- **Ziel-Bankbereich.** Default ist aktuell 97, das überschreibt die letzten 32 Classics. Entweder bewusst so lassen oder `NBANKS` erhöhen bzw. den Classics-Bereich verschieben. **Mit Manfred abstimmen, nicht selbst entscheiden.**
- Nav-Leiste (3.1) auch hier konsistent halten.

### 3.3 FX-Bridge verifizieren
`ARFX` ist bereits integriert (`index.html` bindet `acidratchet-fx-core.js` ein und konsumiert Handoff, Preset, Sample-Slots). Zu prüfen ist, ob der volle FX-Stand aus dem ZIP damit deckungsgleich ist — insbesondere ob `ENGINE_DEFAULTS`, Preset-Schema und Sample-Slots zwischen FX LAB und `index.html` dieselbe Version sprechen. Bei Versionsdrift: `ARFX.VERSION` als harte Prüfung einbauen, statt still auseinanderlaufen zu lassen.

### 3.4 Offene Wünsche (erst nach Rückfrage anfangen)
- **Klangregler pro Bank.** `snapPat()` additiv um ein `ctl`-Objekt erweitern (Cutoff, Reso, Env, Decay, Accent, Drive), `applyBankExtras()` entsprechend. Alte Bänke ohne `ctl` müssen weiter funktionieren. DSP bleibt unberührt.
- Weitere Song-Sets, Sets in anderen Tonarten.

---

## 4. Abschluss jeder Runde

```bash
# Syntax der eingebetteten Scripts prüfen
node --check <extrahiertes_script>.js

git add -A
git commit -m "<knapp, was und warum>"
git push
```

GitHub Pages baut nach dem Push automatisch. Danach **auf dem iPhone gegenprüfen** — nicht nur im Desktop-Browser: AudioWorklet, Touch-Targets und Safari-Eigenheiten fallen sonst durch.

---

## 5. Ton

Deutsch, knapp. Keine Wiederholungen, keine langen Messprotokolle bei kleinen Tasks. Ehrlich sagen, wenn etwas nicht geht oder nicht sicher ist — lieber nachfragen als raten.
