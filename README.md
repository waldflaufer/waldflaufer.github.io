# waldflaufer.github.io

Persönliche Website von Christina Junger — <https://waldflaufer.github.io/>

Gebaut mit [Hugo](https://gohugo.io/) und dem Theme
[Stack](https://github.com/CaiJimmy/hugo-theme-stack) von Jimmy Cai,
veröffentlicht über GitHub Pages.

| | |
|---|---|
| Hugo | 0.155.1 extended, lokal in `hugo_extended/` (nicht im Repo) |
| Theme | Stack v4.0.0-beta.5, eingebunden als **Hugo Module** |
| Deployment | GitHub Actions, siehe `.github/workflows/deploy.yml` |

> Das Theme liegt im Hugo-Cache, **nicht im Projekt**. Ein Ordner `themes/`
> wäre wirkungslos. Anpassungen gehören nach `layouts/` oder
> `assets/scss/custom.scss` — siehe [Anpassungen am Theme](docs/theme-anpassungen.md).
>
> Weil das Theme ein Go-Modul ist, braucht der Build **Go**.

---

## Lokal arbeiten

```bash
./hugo_extended/hugo.exe server -D --bind 0.0.0.0 --port 1313
```

`-D` zeigt auch Entwürfe. Die Seite läuft auf <http://localhost:1313/>.

Gegenprobe — zeigt genau das, was online ginge:

```bash
./hugo_extended/hugo.exe --gc --minify -d /tmp/check
```

---

## Aufbau

```
content/
├── _index.md              →  /                 Startseite
├── about/index.md         →  /about/
├── research/              →  /research/        Forschung, Awards, Weiterbildung
├── playground/            →  /playground/      Making und Crafting
├── inspiration/           →  /inspiration/     Reisen und Entdeckungen
└── page/                  →  nur Technik: Archiv und Suche
```

**Der Ordnername ist die URL.** `content/playground/jetclay/` wird zu
`/playground/jetclay/`. Umbenennen heißt: Ordner umbenennen.

### Neuen Beitrag anlegen

1. Ordner unter `research/`, `playground/` oder `inspiration/` anlegen —
   kurz, klein, mit Bindestrichen
2. `index.md` hineinlegen, **Bilder direkt daneben** (Hugo nennt das *Page Bundle*)
3. Bilder komprimieren → [Medien komprimieren](docs/medien-komprimieren.md)
4. Kategorien und Tags vergeben → [Kategorien und Tags](docs/kategorien-und-tags.md)
5. `draft: true`, solange du schreibst

```yaml
---
title: "Fruchtleder dörren"
date: 2026-09-14
description: "Ein Satz, der neugierig macht."
image: "cover.jpg"
categories:
    - Food Preservation
tags:
    - project
draft: true
---
```

Weil die Bilder im selben Ordner liegen, ziehen sie beim Verschieben mit.

---

## Veröffentlichen

```bash
git add -A
git status --short      # durchsehen
git commit -m "…"
git push origin main
```

**Einmalig nötig:** *Settings → Pages → Build and deployment → Source* muss auf
**GitHub Actions** stehen. Steht dort „Deploy from a branch", baut GitHub
stattdessen mit Jekyll und zeigt diese README als Startseite an.

Entwürfe, Fehlersuche und die Prüfschritte vor dem Hochladen:
→ [Veröffentlichen](docs/veroeffentlichen.md)

---

## Dokumentation

| Datei | Inhalt |
|---|---|
| [docs/medien-komprimieren.md](docs/medien-komprimieren.md) | Fotos und Videos verkleinern, Befehle und Fallstricke |
| [docs/kategorien-und-tags.md](docs/kategorien-und-tags.md) | Wann welche Kategorie, wann welcher Tag |
| [docs/theme-anpassungen.md](docs/theme-anpassungen.md) | Eigene Templates, Shortcodes, Farben, Datenschutz |
| [docs/veroeffentlichen.md](docs/veroeffentlichen.md) | Deployment, Entwürfe zurückhalten, Fehlersuche |

---

## Was bewusst nicht im Repo liegt

`public/` und `resources/` (Build-Ergebnisse) · `hugo_extended/` (57 MB
Binary) · `themes/` (ungenutzt) · `_local-media/` (unkomprimierte Originale) ·
`_notes/`, `CLAUDE-md`, `tmp/` (privat) · einzelne Beitragsordner in Arbeit.

Details in `.gitignore` und in [Veröffentlichen](docs/veroeffentlichen.md).

---

## Lizenz

**Texte, Fotos und Projekte: © Christina Junger.** Alle Rechte vorbehalten,
sofern beim einzelnen Beitrag nichts anderes steht.

Die Datei `LICENSE` stammt aus dem Starter-Template und lautet auf
*MIT © Jimmy Cai* — sie gilt für das Template, nicht für meine Inhalte.
Das Theme selbst steht unter GPL-3.0.

> Noch zu entscheiden: ob die eigene Konfiguration und die eigenen Layouts
> unter eine eigene Lizenz gestellt werden.
