# DeutschTube auf Vercel bringen

Diese App ist eine reine statische Seite — **kein Build-Schritt, keine Dependencies.**
Das macht das Deployment sehr einfach.

---

## Schritt 1 — Ordner an einen guten Platz legen

Der Ordner liegt aktuell in `replicate Langey/deutschtube/` und ist dort in der
`.gitignore` von *sprintdeutsch* ausgeschlossen, damit er das andere Repo nicht stört.
Verschiebe ihn am besten neben deine anderen Projekte, z. B.:

```
~/Documents/Claude/Projects/deutschtube/
```

## Schritt 2 — GitHub-Repo anlegen

Im Terminal, im Ordner `deutschtube`:

```bash
cd ~/Documents/Claude/Projects/deutschtube
git init
git add .
git commit -m "DeutschTube: kuratierte Video-Bibliothek mit Statistik"
git branch -M main
```

Dann auf https://github.com/new ein leeres Repo namens **deutschtube** anlegen
(ohne README, ohne .gitignore) und verbinden:

```bash
git remote add origin https://github.com/Homerpeco/deutschtube.git
git push -u origin main
```

## Schritt 3 — Auf Vercel importieren

1. https://vercel.com/new öffnen
2. Repo **deutschtube** auswählen → *Import*
3. Einstellungen:
   - **Framework Preset:** `Other`
   - **Root Directory:** `./`
   - **Build Command:** leer lassen (bzw. Override ausschalten)
   - **Output Directory:** leer lassen (bzw. `.`)
   - **Install Command:** leer lassen
4. *Deploy*

Nach ca. 20 Sekunden ist die App unter `https://deutschtube.vercel.app` erreichbar
(falls der Name vergeben ist, vergibt Vercel eine Variante — unter
*Settings → Domains* kannst du sie ändern).

## Schritt 4 — Deine bestehende Bibliothek mitnehmen

Wichtig, falls du in der alten lokalen Version schon Videos gesammelt hast:
`localStorage` gilt **pro Herkunft (Origin)**. `http://localhost:8787` und
`https://deutschtube.vercel.app` sind zwei verschiedene Origins.

1. Alte Version starten (`start-deutschtube.command`)
2. Oben rechts auf **Daten → ⬇ Sicherung herunterladen**
   *(die alte Version hat den Knopf noch nicht — dann stattdessen in der
   Browser-Konsole auf der alten Seite ausführen:)*

```js
copy(JSON.stringify({app:'DeutschTube',version:2,
  topics:JSON.parse(localStorage.deutschtube_topics||'[]'),
  videos:JSON.parse(localStorage.deutschtube_videos||'[]'),
  daily:{},openTopics:{},ui:{}}))
```

   Danach in eine Datei `alt.json` einfügen und speichern.
3. Auf `deutschtube.vercel.app` → **Daten → ⬆ Sicherung einlesen** → Datei wählen →
   *Zusammenführen*

Ab dann läuft alles über die Vercel-URL.

## Danach: Updates

```bash
git add . && git commit -m "…" && git push
```

Vercel deployt automatisch bei jedem Push auf `main`.

---

## Gedächtnis — wie es funktioniert und was du wissen musst

| | |
|---|---|
| **Wo** | `localStorage`, Schlüssel `deutschtube_v2`, gebunden an Browser + Domain |
| **Wann gespeichert** | bei jeder Änderung, plus alle 10 Sekunden während der Wiedergabe |
| **Was** | Themen, Videos, Notizen, Status, Wiedergaben, Sekunden, Tagesaktivität |
| **Sicherung** | *Daten → Sicherung herunterladen* → eine JSON-Datei |
| **Gerätewechsel** | Sicherung auf Gerät B einlesen (*Zusammenführen* addiert die Statistik) |
| **Rücksetz-Kopie** | vor Import und Reset automatisch unter `deutschtube_v2_autobackup` |

**Einzige echte Einschränkung:** Es gibt keinen Server. Wenn du den Browser-Speicher
löschst („Websitedaten löschen“) oder in ein anderes Profil wechselst, ist die
Bibliothek dort leer. Deshalb erinnert dich die App alle drei Wochen an eine Sicherung.
Lege die Sicherungsdatei einfach in deinen iCloud- oder Dropbox-Ordner — dann hast du
faktisch eine Synchronisation, nur eben manuell.

Wenn dir das irgendwann zu umständlich wird, lässt sich echte Geräte-Synchronisation
über Vercel Blob nachrüsten (du hast `@vercel/blob` in SprintDeutsch bereits installiert).
Sag einfach Bescheid.
