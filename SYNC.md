# Geräte-Abgleich einrichten

Ziel: Du legst am Laptop ein Video an — auf dem Handy ist es da. Und umgekehrt.

Dafür braucht die App einen Ort, an dem deine Bibliothek liegt. Wir nehmen **Supabase**:
kostenlos, kein Server-Code, und die Anmeldung läuft über einen Link per E-Mail — kein Passwort.

Du machst das **einmal**, danach nie wieder. Rechne mit 3 Minuten.

---

## Schritt 1 — Kostenloses Supabase-Projekt anlegen

1. Auf https://supabase.com gehen → **Start your project** → mit GitHub anmelden
   (du hast ja schon ein GitHub-Konto).
2. **New project**
   - *Name:* `deutschtube`
   - *Database Password:* irgendetwas Langes — **du brauchst es nie wieder**, aber speichere
     es trotzdem in deinem Passwort-Manager.
   - *Region:* `Central EU (Frankfurt)` — am nächsten an dir.
3. **Create new project** und ein, zwei Minuten warten.

## Schritt 2 — Tabelle anlegen

Links im Menü auf **SQL Editor** → **New query** → das Folgende komplett hineinkopieren
und auf **Run** klicken:

```sql
create table if not exists public.dt_state (
  user_id    uuid primary key references auth.users on delete cascade,
  data       jsonb       not null default '{}'::jsonb,
  updated_at timestamptz not null default now()
);

alter table public.dt_state enable row level security;

drop policy if exists "own row" on public.dt_state;
create policy "own row" on public.dt_state
  for all
  using  (auth.uid() = user_id)
  with check (auth.uid() = user_id);
```

Es sollte „Success. No rows returned“ erscheinen.

> Die Zeile mit `row level security` ist die wichtige: sie sorgt dafür, dass **nur du**
> an deine Daten kommst, auch wenn der Schlüssel aus Schritt 3 öffentlich ist.

## Schritt 3 — Die zwei Werte holen

Links unten **Project Settings** (Zahnrad) → **API**. Dort stehen:

| Feld in Supabase | Was du brauchst |
|---|---|
| **Project URL** | `https://xxxxxxxx.supabase.co` |
| **Project API keys → `anon` `public`** | ein langer Text, beginnt mit `eyJ…` |

⚠️ Nimm **nicht** den `service_role`-Schlüssel. Der ist geheim. Der `anon`-Schlüssel ist
ausdrücklich für den Einsatz im Browser gedacht und durch Schritt 2 abgesichert.
Die App weist den falschen Schlüssel auch aktiv zurück.

## Schritt 4 — Adresse der App erlauben

**Authentication** → **URL Configuration**:

- *Site URL:* `https://deutschtube.vercel.app`
- *Redirect URLs:* `https://deutschtube.vercel.app` hinzufügen

Ohne das führt der Anmeldelink ins Leere.

## Schritt 5 — In der App eintragen

1. `https://deutschtube.vercel.app` öffnen → oben rechts auf **Daten**
2. Projekt-URL und Anon Key einsetzen → **Speichern und verbinden**
3. E-Mail eingeben → **Anmeldelink senden**
4. Die Mail öffnen und auf den Link tippen — **auf demselben Gerät**

Fertig. Oben rechts steht jetzt ein grüner Punkt mit „Synchronisiert“.

## Schritt 6 — Zweites Gerät

Auf dem Handy dieselbe Seite öffnen, dieselben zwei Werte eintragen, **dieselbe E-Mail**
verwenden. Nach der Anmeldung zieht sich das Handy deine Bibliothek.

> **Schick mir die beiden Werte**, dann baue ich sie fest ein — dann entfällt Schritt 5.2
> auf jedem weiteren Gerät und du musst dich nur noch anmelden.

---

## Wie der Abgleich arbeitet

**Wann:** beim Start, nach jeder Änderung (gebündelt nach 4 Sekunden), jede Minute im
Hintergrund, beim Zurückkehren zur App und sobald du wieder online bist.

**Was passiert beim Zusammentreffen zweier Stände:** zusammengeführt, nicht überschrieben.

| Fall | Ergebnis |
|---|---|
| Video nur auf dem Laptop | wandert aufs Handy |
| Thema auf beiden umbenannt | die jüngere Änderung gewinnt |
| Video auf beiden geschaut | Zeiten und Aufrufe werden **addiert** |
| Video auf einem Gerät gelöscht | verschwindet auch auf dem anderen |
| Status „offen“ vs. „gesehen“ | der weitere Fortschritt gewinnt |

Zähler werden **pro Gerät** geführt. Deshalb kann die App Zeiten korrekt addieren, ohne
dass sie beim wiederholten Abgleichen anwachsen — ein klassischer Fehler bei
selbstgebautem Sync. Getestet mit sechs Hin-und-Her-Runden: die Werte bleiben stabil.

## Offline

Alles funktioniert weiter ohne Netz — die App arbeitet lokal und gleicht ab, sobald du
wieder verbunden bist. Die Sicherungsdatei unter **Daten** bleibt als zweites Netz bestehen.

## Kosten

Der kostenlose Supabase-Tarif reicht hier um Größenordnungen: deine Bibliothek ist ein
paar hundert Kilobyte. Projekte ohne Zugriff pausieren nach längerer Inaktivität und
lassen sich mit einem Klick wieder wecken — bei täglicher Nutzung passiert das nicht.

## Wenn etwas klemmt

| Symptom | Ursache |
|---|---|
| Anmeldelink führt auf eine Fehlerseite | Schritt 4 fehlt oder die URL stimmt nicht genau |
| „Sync-Fehler“ oben rechts | meist Schritt 2 nicht ausgeführt — SQL noch einmal laufen lassen |
| Mail kommt nicht an | Spam-Ordner; Supabase drosselt außerdem auf wenige Mails pro Stunde |
| Handy zeigt alte Daten | einmal auf den Punkt oben rechts tippen → **Jetzt abgleichen** |
