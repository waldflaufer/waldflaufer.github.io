# Veröffentlichen

Die Website liegt auf GitHub Pages und wird bei jedem Push automatisch neu
gebaut. Dieses Dokument beschreibt den Ablauf, die Stolperstellen und wie man
Beiträge zurückhält, die noch nicht öffentlich sein sollen.

---

## Der normale Ablauf

```bash
git add -A
git status --short      # durchsehen: kommt nur mit, was mitkommen soll?
git commit -m "Beschreibung der Änderung"
git push origin main
```

Der Push löst den Workflow aus. Unter **Actions** im Repository sieht man, ob
er durchläuft; nach etwa einer Minute ist die Seite aktualisiert.

> Beim ersten Aufruf danach mit **Strg + F5** neu laden, sonst zeigt der
> Browser die zwischengespeicherte alte Fassung.

---

## Einmalige Einstellung, ohne die nichts funktioniert

**Settings → Pages → Build and deployment → Source** muss auf
**„GitHub Actions"** stehen.

Steht dort „Deploy from a branch", ignoriert GitHub den Workflow, lässt
stattdessen sein eigenes **Jekyll** über das Repo laufen und rendert einfach
die `README.md` als Startseite.

**Erkennungsmerkmal:** Im Quelltext der Live-Seite steht dann
`<meta name="generator" content="Jekyll v3.x">`, und unter *Actions* laufen
zwei Pipelines nebeneinander – „Deploy site to GitHub Pages" (die richtige)
und „pages build and deployment" (Jekyll).

> Wenn GitHub nach dem Umstellen Vorlagen anbietet („GitHub Pages Jekyll",
> „Static HTML") mit einem *Configure*-Knopf: **nicht anklicken.** Das legt
> eine zweite Workflow-Datei an, die mit `deploy.yml` konkurriert.

---

## Was der Workflow tut

`.github/workflows/deploy.yml`:

1. Hugo extended installieren (Version steht als `HUGO_VERSION` im Workflow)
2. Repository auschecken
3. **Go einrichten** – nötig, weil das Theme ein Hugo Module ist
4. Modulcache wiederherstellen
5. `hugo --gc --minify` mit der von GitHub gelieferten `baseURL`
6. Ergebnis als Artefakt hochladen und veröffentlichen

> Die Reihenfolge ist wichtig: **Checkout vor Go.** Sonst sucht `setup-go`
> nach `go.sum`, bevor das Repository da ist, und der Modulcache greift nie.

Hugo aktualisieren heißt: `HUGO_VERSION` im Workflow ändern – und lokal
dieselbe Version in `hugo_extended/` legen, damit lokal und online
übereinstimmen.

---

## Beiträge zurückhalten

`draft: true` reicht **nicht**, wenn der Entwurf wirklich privat bleiben soll:

| | verhindert Veröffentlichung? | verhindert Lesen auf GitHub? |
|---|---|---|
| `draft: true` | ja | **nein** – das Repo ist öffentlich |
| Eintrag in `.gitignore` | ja | ja |

Für alles, woran noch gearbeitet wird, deshalb **beides**:

```yaml
draft: true
```

und in `.gitignore`:

```
content/research/mein-entwurf/
```

Lokal bleibt der Beitrag mit `hugo server -D` trotzdem vollständig sichtbar.

### Ganze Bereiche zurückhalten

Zum Beispiel alle Playground-Beiträge, aber die Übersichtsseite behalten:

```
content/playground/*
!content/playground/_index.md
```

**Freigeben** heißt dann: diese zwei Zeilen aus `.gitignore` löschen, dann
`git add -A` und pushen.

---

## Vor dem Veröffentlichen prüfen

```bash
./hugo_extended/hugo.exe --gc --minify -d /tmp/check
```

Ohne `-D` – zeigt also genau das, was online ginge.

**Kaputte Links finden:**

```bash
cd /tmp/check && grep -rhoE '(href|src)=("?)/[^"'"'"' >]*' --include=*.html . | sed -E 's/^(href|src)="?//' | sed 's/[#?].*//' | sort -u | while read u; do case "$u" in ""|"/") continue;; esac; p=".${u%/}"; [ -e "$p" ] || [ -e "$p/index.html" ] || [ -e ".$u" ] || echo "BROKEN: $u"; done
```

Das Muster berücksichtigt, dass `--minify` die Anführungszeichen entfernt –
ein Muster mit `href="` findet die Hälfte nicht.

**Alle veröffentlichten Seiten auflisten:**

```bash
grep -o '<loc>[^<]*</loc>' /tmp/check/sitemap.xml | sed 's|</\?loc>||g'
```

Gut, um zu sehen, ob wirklich nur das online geht, was online soll.

**Keine großen Dateien:**

```bash
find content -type f -size +2M -printf "%s %p\n" | sort -rn | head
```

---

## Wenn etwas schiefgeht

| Symptom | Ursache |
|---|---|
| Seite zeigt die README | Pages-Source steht auf „Deploy from a branch" |
| Änderung nicht sichtbar | Browser-Cache – Strg + F5 |
| Workflow rot, `hugo: not found` | Download-Adresse oder `HUGO_VERSION` stimmt nicht |
| Workflow rot beim Theme | Go fehlt oder steht vor dem Checkout |
| Push abgelehnt (`rejected`) | Remote ist weiter – erst `git pull --rebase` |
| Push abgelehnt (Datei > 100 MB) | große Datei nach `_local-media/` verschieben, **vor** dem Commit |

> Eine einmal committete große Datei bleibt für immer in der Git-Historie,
> auch nach dem Löschen. Sie wieder herauszubekommen erfordert das Umschreiben
> der Historie. Deshalb vor dem Commit prüfen, nicht danach.
