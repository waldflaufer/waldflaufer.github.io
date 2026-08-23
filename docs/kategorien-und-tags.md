# Kategorien und Tags

Kurzfassung:

> **Kategorie = was es ist.** Groß geschrieben. Eine, höchstens drei pro Beitrag.
> **Tag = welche Art von Beitrag.** Klein geschrieben. Aus einer festen Liste.

Beides ist englisch, wie die ganze Website.

---

## Warum die Trennung

Die beiden beantworten unterschiedliche Fragen:

- **Kategorie** – *„Worum geht es handwerklich?"* → Sewing, Woodturning, 3D Printing
- **Tag** – *„Was für ein Beitrag ist das?"* → project, workshop, event

Deshalb sehen sie auch unterschiedlich aus: Kategorien erscheinen als farbige
Kacheln oben auf jedem Beitrag und im Kategorie-Board auf `/playground/` und
`/inspiration/`. Tags sind unauffälliger und dienen dem Querschnitt.

---

## Kategorien

**Schreibweise: Title Case**, also jedes Hauptwort groß – `Pattern Drafting`,
nicht `pattern drafting`.

Das ist keine Kosmetik. Hugo behandelt `3D Printing` und `3D printing` als
**zwei verschiedene Kategorien** und legt zwei getrennte Seiten an. Genau das
war schon einmal der Fall.

Bestehende Kategorien – bitte diese wiederverwenden, statt neue Varianten
anzulegen:

```
3D Printing · Art · Cosplay · Craftsmanship · Drawing · Electronics ·
Embroidery · Epoxy Resin · Graffiti Art · Inkscape · Instrument Making ·
Laser Cutting · Pattern Drafting · Research · Scrapbooking · Sewing ·
Travel · Woodcraft · Woodturning
```

### Wann lege ich eine neue an?

Wenn du erwartest, dass **mindestens zwei Beiträge** hineinpassen werden.
Eine Kategorie mit genau einem Beitrag ist eine Seite, auf der nichts steht.

Fürs Dörren zum Beispiel: `Food Preservation` trägt weiter als `Fruit Leather` –
darunter passen später auch getrocknete Kräuter oder Apfelringe.

Ist es wirklich einmalig, lass die Kategorie weg und beschreib es im Text.

### Wie viele pro Beitrag?

**Eine bis drei.** Der Ukulelen-Beitrag zeigt, wie es gut geht:

```yaml
categories:
    - Instrument Making
    - Woodcraft
    - Craftsmanship
```

Drei Kategorien, drei sinnvolle Zugänge. Bei fünf oder mehr verliert die
Einordnung ihren Wert – dann steht der Beitrag überall und nirgends.

---

## Tags

**Schreibweise: klein.** Die Liste ist bewusst kurz und soll kurz bleiben:

| Tag | wofür |
|---|---|
| `project` | etwas, das ich gebaut oder gemacht habe |
| `workshop` | ich habe etwas gelernt, meist bei jemandem |
| `event` | eine einmalige Veranstaltung |
| `research` | gehört zur wissenschaftlichen Arbeit |
| `inspiration` | etwas, das mich angeregt hat |

**Immer Einzahl.** Es gab schon einmal `project` *und* `projects` nebeneinander –
zwei Tag-Seiten für dieselbe Sache.

Wenn ein neuer Tag sich aufdrängt: erst schauen, ob nicht doch eine Kategorie
das Richtige ist. Tags beschreiben die *Art*, nicht das *Thema*.

---

## So sieht ein vollständiges Frontmatter aus

```yaml
---
title: "Fruchtleder dörren"
date: 2026-09-14
description: "Ein Satz, der neugierig macht - erscheint unter dem Titel."
image: "cover.jpg"
categories:
    - Food Preservation
tags:
    - project
draft: true      # solange du schreibst
---
```

`image` ist das Titelbild in Übersichtslisten. Die Datei liegt im selben
Ordner wie die `index.md`.

---

## Das Kategorie-Board pflegt sich selbst

Auf `/playground/` und `/inspiration/` steht:

```markdown
{{< category-board >}}
```

Der Shortcode liest die Kategorien **aus den Beiträgen des jeweiligen Bereichs**
aus und baut daraus die Kacheln – mit Anzahl, alphabetisch sortiert.

Es gibt **keine Liste, die du pflegen musst.** Schreibst du einen Beitrag mit
`categories: - Food Preservation`, erscheint die Kachel beim nächsten Build von
selbst. Löschst du den letzten Beitrag einer Kategorie, verschwindet sie wieder.

Die Farben werden gleichmäßig über den Farbkreis verteilt, damit keine zwei
Kacheln gleich aussehen. Kommt eine dazu, ordnet sich das automatisch neu.
(Das Theme würde die Farbe sonst aus einem Hash des Namens ableiten – dabei
bekommen verschiedene Kategorien zwangsläufig dieselbe Farbe.)

---

## Wenn du eine Kategorie umbenennen willst

Sie steht in jedem betroffenen `index.md`. Erst schauen, wo:

```bash
grep -rl "Alte Kategorie" content/
```

Dann in allen Dateien ersetzen:

```bash
grep -rl "Alte Kategorie" content/ | xargs sed -i 's/- Alte Kategorie/- Neue Kategorie/'
```

Danach einmal bauen und prüfen, dass die alte Kategorieseite verschwunden ist:

```bash
./hugo_extended/hugo.exe --gc --minify -d /tmp/check && ls /tmp/check/categories/
```

---

## Kurz vorm Veröffentlichen

Diese Zeile listet auf, was tatsächlich vergeben ist – gut, um Tippfehler und
Dubletten zu finden:

```bash
for f in $(find content -name index.md); do awk '/^---$/{c++;next} c==1&&/^categories:/{m="KAT";next} c==1&&/^tags:/{m="TAG";next} c==1&&/^[a-zA-Z_]+:/{m="";next} c==1&&m!=""&&/^ *- /{gsub(/^ *- /,"");print m"  "$0}' "$f"; done | sort | uniq -c | sort -k2
```

Taucht etwas doppelt in leicht unterschiedlicher Schreibweise auf, ist es ein
Fehler – nicht zwei Kategorien.
