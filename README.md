# waldflaufer.github.io

Persönliche Website von Christina Junger – <https://waldflaufer.github.io/>

Gebaut mit [Hugo](https://gohugo.io/) und dem Theme
[Stack](https://github.com/CaiJimmy/hugo-theme-stack) von Jimmy Cai,
veröffentlicht über GitHub Pages.

---

## Was hier wie zusammenhängt

| | |
|---|---|
| **Hugo** | 0.155.1 extended. Liegt lokal in `hugo_extended/` (nicht im Repo). |
| **Theme** | Stack v4.0.0-beta.5, eingebunden als **Hugo Module** – nicht als Ordner unter `themes/`. |
| **Deployment** | GitHub Actions, siehe `.github/workflows/deploy.yml` |

> **Wichtig zum Theme:** Es wird über `config/_default/module.toml` als Modul
> geladen und liegt im Hugo-Cache, **nicht** im Projekt. Ein Ordner `themes/`
> wäre wirkungslos – Änderungen dort hätten keinerlei Effekt.
> Anpassungen gehören nach `layouts/` oder `assets/scss/custom.scss`.
>
> Weil das Theme ein Go-Modul ist, braucht der Build **Go**. Deshalb
> installiert der Workflow sowohl Hugo als auch Go.

---

## Lokal arbeiten

```bash
./hugo_extended/hugo.exe server -D --bind 0.0.0.0 --port 1313
```

`-D` zeigt auch Entwürfe (`draft: true`). Die Seite läuft dann auf
<http://localhost:1313/>.

Produktionsbuild zum Gegenprüfen – zeigt genau das, was online ginge:

```bash
./hugo_extended/hugo.exe --gc --minify -d /tmp/check
```

---

## Aufbau der Inhalte

```
content/
├── _index.md              →  /                      Startseite
├── about/index.md         →  /about/
├── research/
│   ├── _index.md          →  /research/             Forschung, Awards, Weiterbildung
│   └── training-record/   →  vollständige Belegliste
├── playground/
│   ├── _index.md          →  /playground/           Making und Crafting
│   └── <projekt>/index.md
├── inspiration/
│   ├── _index.md          →  /inspiration/          Reisen und Entdeckungen
│   └── <eintrag>/index.md
└── page/                  →  nur Technik: Archiv und Suche
```

**Der Ordnername ist die URL.** `content/playground/jetclay/` wird zu
`/playground/jetclay/`. Umbenennen heißt: Ordner umbenennen.

### Einen neuen Beitrag anlegen

1. Ordner unter `research/`, `playground/` oder `inspiration/` anlegen –
   kurzer Name, klein, mit Bindestrichen
2. `index.md` hineinlegen, Bilder direkt daneben (Hugo nennt das *Page Bundle*)
3. Bilder komprimieren → [`docs/medien-komprimieren.md`](docs/medien-komprimieren.md)
4. Kategorien und Tags vergeben → [`docs/kategorien-und-tags.md`](docs/kategorien-und-tags.md)
5. `draft: true`, solange du schreibst

Weil die Bilder im selben Ordner liegen, ziehen sie beim Verschieben oder
Umbenennen einfach mit.

---

## Eigene Erweiterungen am Theme

Alles unter `layouts/` überschreibt oder ergänzt das Theme.

### Templates

| Datei | wozu |
|---|---|
| `home.html` | Startseite. Statt der Blogliste des Themes wird der Inhalt aus `content/_index.md` gerendert – ohne Artikelkopf, Datum und Lesezeit. |
| `list.html` | Bereichsübersichten. Das Theme zeigt den Text aus `_index.md` **gar nicht** an; dieses Template holt ihn zurück. |
| `page/single.html` | Statische Seiten ohne Blog-Möbel: kein Datum, keine Lesezeit, kein Lizenzhinweis. |
| `research/record.html` | Dasselbe für die Belegliste der Weiterbildungen. |

Beide letzteren nutzen die Partials `_partials/clean-page-main.html` und
`_partials/toc-setup.html`, damit sie nicht auseinanderlaufen.

> **Body-Klasse nicht vergessen:** Stacks komplettes Artikel-Stylesheet hängt
> an `.article-page`. Jedes eigene Template braucht deshalb
> `{{ define "body-class" }}article-page{{ end }}` – fehlt sie, bekommt der
> Text keine Farbe und ist im Dark Mode unlesbar.

### Shortcodes

| Aufruf | wozu |
|---|---|
| `{{< category-board >}}` | Farbige Kacheln aller Kategorien eines Bereichs, mit Anzahl. Baut sich automatisch aus den Beiträgen. |
| `{{< meta >}}…{{< /meta >}}` | Gedämpfte Zusatzzeile für Datum, Ort, Förderer. |
| `{{< topics >}}a, b, c{{< /topics >}}` | Schlagwort-Reihe für Themen ohne eigene Kategorie. |

### Aussehen

`assets/scss/custom.scss` – Farben und Schrift stehen ganz oben als Variablen:

- Hintergrund warmes Creme, im Dark Mode warmes Dunkelbraun
- Überschriften in Burgunder, im Dark Mode helles Rose, in einer Serifenschrift
- Bilderreihen als gleichmäßiges Raster statt der „justierten" Reihe des Themes
- Rechte Spalte schmaler als im Theme, damit der Inhalt mehr Platz hat

Kontraste sind in beiden Modi über der Barrierefreiheitsschwelle geprüft.

### Datenschutz

`_partials/footer/components/custom-font.html` ist **absichtlich leer**.
Das Theme lädt dort sonst per JavaScript die Schrift „Lato" von
`fonts.googleapis.com` – damit ginge die IP-Adresse jedes Besuchers an Google.

Verbleibende externe Abhängigkeit: die Bild-Lightbox **PhotoSwipe** kommt von
`cdn.jsdelivr.net`, wird aber erst geladen, wenn jemand ein Bild anklickt.

---

## Veröffentlichen

```bash
git add -A
git status --short      # durchsehen, was drankommt
git commit -m "…"
git push origin main
```

Der Push löst den Workflow aus. Unter *Actions* sieht man, ob er durchläuft.

**Einmalig nötig:** *Settings → Pages → Build and deployment → Source* muss auf
**GitHub Actions** stehen. Steht dort „Deploy from a branch", baut GitHub
stattdessen mit Jekyll und zeigt einfach diese README als Startseite an.

---

## Was bewusst nicht im Repo liegt

Siehe `.gitignore`:

| | Grund |
|---|---|
| `public/`, `resources/` | Build-Ergebnisse, entstehen neu |
| `hugo_extended/` | 57 MB Windows-Binary; der Workflow installiert Hugo selbst |
| `themes/` | ungenutzter Theme-Klon, siehe oben |
| `_local-media/` | unkomprimierte Originale und große Rohdateien |
| `_notes/`, `CLAUDE-md`, `tmp/` | private Notizen und Arbeitsdateien |
| einzelne Beitragsordner | Entwürfe, die noch nicht öffentlich sollen |

Der letzte Punkt ist der Trick für Beiträge in Arbeit: **`draft: true` allein
genügt nicht**, denn dieses Repo ist öffentlich – der Entwurfstext wäre auf
GitHub lesbar. Deshalb steht der Ordner zusätzlich in `.gitignore`. Lokal ist
er mit `hugo server -D` trotzdem sichtbar.

---

## Weiterführend

- [`docs/medien-komprimieren.md`](docs/medien-komprimieren.md) – Fotos und Videos verkleinern
- [`docs/kategorien-und-tags.md`](docs/kategorien-und-tags.md) – Kategorien und Tags vergeben

---

## Lizenz

**Texte, Fotos und Projekte: © Christina Junger.** Alle Rechte vorbehalten,
sofern beim einzelnen Beitrag nichts anderes steht.

Die Datei `LICENSE` stammt aus dem Starter-Template und lautet auf
*MIT © Jimmy Cai* - sie gilt fuer das Template, nicht fuer meine Inhalte.
Das Theme selbst steht unter GPL-3.0.

> Noch zu entscheiden: ob die eigene Konfiguration und die eigenen Layouts
> unter eine eigene Lizenz gestellt werden. Bis dahin bleibt die geerbte
> Datei unveraendert stehen.
