# ACIDRATCHET FX LAB V0.1

## Inhalt

- `index.html` — ACIDRATCHET-MF79 v5.5 FX Bridge als GitHub-Pages-Startdatei
- `ACIDRATCHET-MF79_v5.5_FX_BRIDGE.html` — identische, versionierte Kopie
- `ACIDRATCHET_FX_LAB.html` — eigenständiger Deep-Editor
- `acidratchet-fx-core.js` — gemeinsamer DSP- und Preset-Core
- `acidratchet-fx-preset.schema.json` — Preset-Vertrag V1
- `ACIDRATCHET_TD3MO_TRANSLATOR.html` — bestehender Translator für den gemeinsamen Ordner

## Architektur

ACIDRATCHET bleibt das Performance-Instrument. Das FX LAB übernimmt Deep Editing, Presetverwaltung sowie Voice-/Sample-Aufnahmen. Beide Oberflächen laden exakt denselben `acidratchet-fx-core.js`.

Direkter Handoff und Presets funktionieren nur, wenn `index.html`, `ACIDRATCHET_FX_LAB.html` und `acidratchet-fx-core.js` unter derselben Web-Adresse beziehungsweise Origin liegen.

## Umgesetzter V0.1-Umfang

### Gemeinsame FX-Engines

1. Echo Lab — tempo-synchron, Feedback, Tone, Feedback-Drive, Low-Cut
2. Space Lab — generierte Stereo-Impulse-Response, Size, Decay, Damping, Pre-Delay
3. Corrosion Lab — Drive, Fold, Bit Depth, Asymmetry, Low-/High-Cut
4. Pulse/Ratchet Lab — BPM-synchrones Gate, Rate, Depth, Floor, Shape, Tone

### Performance-Oberfläche in ACIDRATCHET

- Preset-Auswahl
- vier Makros: Character, Motion, Depth, Mix
- getrennte Sends für Voice A, Voice B, Drums und Samples
- Bypass
- Hold-Taster für maximale Depth/Mix
- vier Sample-Pads
- Sample-Quantisierung: sofort, nächster Step, nächster Takt
- Effekte werden vor dem bestehenden Master-HPF/Compressor/Glue-Pfad summiert und damit vom WAV-Recorder erfasst

### FX LAB

- Deep Edit je Engine
- internes Preview mit 303-artiger Testline und Kick
- Preset speichern, duplizieren, importieren und exportieren
- direkter Handoff an ACIDRATCHET
- vier Voice-/Sample-Slots
- Mikrofonaufnahme oder Audioimport
- Samples in IndexedDB, Presets und Slot-Referenzen als kleine Metadaten

## Deployment

Den gesamten Ordnerinhalt gemeinsam in das Root-Verzeichnis der GitHub Page kopieren. `index.html` ist die Startdatei.

Für Mikrofonaufnahme wird eine HTTPS-Adresse benötigt. GitHub Pages erfüllt diese Voraussetzung. Unter einer lokalen `file://`-Adresse kann die Mikrofonfreigabe je nach Browser blockiert sein.

## Prüfstatus

- Syntaxprüfung aller JavaScript-Dateien: bestanden
- DOM-ID-Prüfung: keine doppelten IDs
- Prüfung aller statischen `getElementById`-Referenzen: vollständig
- Mock-Web-Audio-Aufbau aller vier Engines und acht Werkspresets: bestanden
- Reale Hörprüfung auf iPhone/iPad/Edge/Chrome: noch offen
