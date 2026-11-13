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

**Radio**
- Live-Sender als Audio-Stream (MP3/AAC) oder HLS (`.m3u8`), mit Deutschlandfunk,
  DLF Kultur, DLF Nova und tagesschau24 als Startbelegung
- Sender hinzufügen, bearbeiten, löschen; Lautstärke wird gemerkt
- Radio pausiert automatisch, sobald ein Video startet — nie zwei Tonquellen gleichzeitig
- Hörzeit pro Sender; sie fließt in dieselbe Heatmap und Serie wie die Videozeit
- **Live-Mitschrift** über die Spracherkennung des Browsers (Chrome/Edge, `de-DE`):
  läuft übers Mikrofon, der Sender muss also über Lautsprecher laufen.
  Mitschrift lässt sich kopieren oder als `.txt` sichern.
  Nur `https://`-Streams — unverschlüsselte werden vom Browser blockiert.

**Geräte-Abgleich**
- Anmeldung per Magic Link (Supabase, kostenlos) — kein Passwort
- Laptop und Handy teilen dieselbe Bibliothek; Abgleich beim Start, nach jeder Änderung,
  minütlich und beim Zurückkehren zur App
- Zusammenführen statt Überschreiben: Inhalte vereinigt, Zähler **pro Gerät** geführt und
  addiert (bläht sich beim wiederholten Abgleich nicht auf), Löschungen wandern mit
- Funktioniert offline weiter und gleicht ab, sobald wieder Netz da ist
- Einrichtung: siehe `SYNC.md` (einmalig, ~3 Minuten)

**Gedächtnis**
- Alles liegt in `localStorage` (Schlüssel `deutschtube_v2`, Schema v4) — überlebt Neuladen und Neustart
- Automatische Migration aus der alten Einzeldatei-Version und aus `radiode_stations` der RadioDE-App
- JSON-Export / -Import zum Sichern und Umziehen auf andere Geräte
  (Import wahlweise *zusammenführen* oder *ersetzen*; vorher wird automatisch eine
  Rücksetz-Kopie unter `deutschtube_v2_autobackup` angelegt)

**Statistik**
- Wiedergaben und Wiedergabezeit pro Video (gezählt ab 5 s echter Wiedergabe)
- Zeit pro Thema und Hörzeit pro Radiosender
- Schwachstellen-Rangliste: 50 % Wiederholungsrate + 30 % ⚡-Anteil + 20 % Zeitintensität
- Aktivitäts-Heatmap der letzten 26 Wochen, Streak und längste Serie
- „Zu lange nicht angerührt“ — Themen für die nächste Wiederholung

## Shadowing

`🗣 Shadowing` in der Infoleiste (oder `U`) schaltet den Shadowing-Modus ein. Das Panel ersetzt
die Notizleiste; beim Ausschalten kommen die Notizen unverändert zurück.

- **A–E-Loop:** `A` setzt den Anfang auf die aktuelle Zeit, `E` das Ende und startet den Loop.
  Nach jedem Durchlauf folgt eine Pause (0 / 1 / 2 / 3 s, Taste `G`); der Zähler zeigt die Durchläufe.
  Zieht man die YouTube-Leiste weit aus dem Abschnitt heraus, geht der Loop aus.
- **Tempo:** Tasten `1`–`4`. Angeboten werden 0,75 / 0,85 / 1 / 1,15× — gibt YouTube diese Stufen
  nicht frei, stattdessen 0,5 / 0,75 / 1 / 1,25×.
- Im Shadowing-Modus startet am Videoende weder der Video-Loop noch das automatische nächste Video.
- Sprungungenauigkeit von YouTube: ca. 0,1–0,25 s — gut für Satzteile, nicht für einzelne Silben.

## Tastatur

| Taste | Aktion |
|---|---|
| `N` | Video hinzufügen (im Radio-Tab: Sender) |
| `T` | Thema anlegen |
| `/` | Suche |
| `S` | Statistik ⇄ Player |
| `R` | Radio ⇄ Player |
| `Leertaste` | Radio abspielen / pausieren (im Radio-Tab) |
| `J` / `K` | nächstes / vorheriges Video |
| `B` | Merkliste |
| `H` | als schwer markieren |
| `U` | Shadowing ein / aus |
| `A` / `E` | Shadowing: Anfang / Ende des Abschnitts setzen (E schaltet den Loop ein) |
| `L` | Shadowing: Loop ein / aus |
| `G` | Shadowing: Pause nach dem Abschnitt 0 → 1 → 2 → 3 s |
| `1`–`4` | Shadowing: Tempo |
| `Leertaste` | Shadowing: Video abspielen / pausieren (im Player-Tab) |
| `Esc` | schließen |

## Betrieb

Reine statische Seite, kein Build-Schritt.

```bash
python3 -m http.server 8787   # lokal: http://localhost:8787
```

YouTube-Embeds funktionieren nicht über `file://` — immer über http(s) öffnen.

## Deployment

Auf Vercel als **Other / static** importieren: kein Build Command, Output Directory = Repo-Wurzel.
