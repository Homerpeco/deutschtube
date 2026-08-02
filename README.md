# DeutschTube

Eine handverlesene Video-Bibliothek für Deutsch als Fremdsprache — kein Algorithmus,
nur die Videos, die ich selbst verlinke.

Teil des Lern-Ökosystems zusammen mit
[SprintDeutsch](https://sprintdeutsch.vercel.app/) und
[Karteikasten](https://karteikasten-vercel.vercel.app/).

## Funktionen

**Bibliothek**
- Themen (Themas) mit Farbe, Notiz und Reihenfolge; Rechtsklick zum Bearbeiten/Verschieben
- Videos per URL, Video-ID, Shorts-Link oder als Massen-Import (eine URL pro Zeile)
- Titel werden automatisch von YouTube geholt (oEmbed) — nichts abtippen
- Suche + Filter (offen / in Arbeit / gesehen / Merkliste / schwer)
- Notizen pro Video, Merkliste, ⚡-Markierung „schwer“
- Autoplay zum nächsten Video, Loop-Modus fürs Shadowing
- Deep-Link `?v=VIDEO_ID`

**Gedächtnis**
- Alles liegt in `localStorage` (Schlüssel `deutschtube_v2`) — überlebt Neuladen und Neustart
- Automatische Migration aus der alten Einzeldatei-Version
- JSON-Export / -Import zum Sichern und Umziehen auf andere Geräte
  (Import wahlweise *zusammenführen* oder *ersetzen*; vorher wird automatisch eine
  Rücksetz-Kopie unter `deutschtube_v2_autobackup` angelegt)

**Statistik**
- Wiedergaben und Wiedergabezeit pro Video (gezählt ab 5 s echter Wiedergabe)
- Zeit pro Thema
- Schwachstellen-Rangliste: 50 % Wiederholungsrate + 30 % ⚡-Anteil + 20 % Zeitintensität
- Aktivitäts-Heatmap der letzten 26 Wochen, Streak und längste Serie
- „Zu lange nicht angerührt“ — Themen für die nächste Wiederholung

## Tastatur

| Taste | Aktion |
|---|---|
| `N` | Video hinzufügen |
| `T` | Thema anlegen |
| `/` | Suche |
| `S` | Statistik ⇄ Player |
| `J` / `K` | nächstes / vorheriges Video |
| `B` | Merkliste |
| `H` | als schwer markieren |
| `Esc` | schließen |

## Betrieb

Reine statische Seite, kein Build-Schritt.

```bash
python3 -m http.server 8787   # lokal: http://localhost:8787
```

YouTube-Embeds funktionieren nicht über `file://` — immer über http(s) öffnen.

## Deployment

Auf Vercel als **Other / static** importieren: kein Build Command, Output Directory = Repo-Wurzel.
