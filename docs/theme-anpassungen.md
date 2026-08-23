# Eigene Anpassungen am Theme

Die Website nutzt das Theme
[Stack](https://github.com/CaiJimmy/hugo-theme-stack) (v4.0.0-beta.5, GPL-3.0)
von Jimmy Cai. Dieses Dokument hält fest, **was daran geändert wurde und warum** –
damit später nachvollziehbar bleibt, welche Zeile welches Problem löst.

---

## Erst das Wichtigste: wo Anpassungen hingehören

Das Theme ist als **Hugo Module** eingebunden (`config/_default/module.toml`)
und liegt im Hugo-Cache, **nicht im Projekt**.

> Ein Ordner `themes/` wäre wirkungslos. Es gab hier einmal einen Klon des
> Themes – Änderungen darin hatten schlicht keine Wirkung, weil Hugo die
> Modulversion benutzt. Der Ordner steht deshalb in `.gitignore`.

Anpassungen gehören nach:

| Ort | wofür |
|---|---|
| `layouts/` | Templates, Partials, Shortcodes – überschreiben gleichnamige des Themes |
| `assets/scss/custom.scss` | Aussehen. Wird als Letztes geladen, gewinnt also im Zweifel |
| `assets/icons/` | eigene Icons, per Dateiname in `menu.toml` referenziert |

---

## Templates

| Datei | warum es sie gibt |
|---|---|
| `home.html` | Stack rendert auf `/` sonst die paginierte Blogliste. Hier kommt stattdessen der Inhalt aus `content/_index.md` – ohne Artikelkopf, Datum, Lesezeit und Lizenzhinweis. |
| `list.html` | Stacks Bereichsübersicht zeigt den Text aus `_index.md` **überhaupt nicht** an, nur Titel und Beitragsliste. Dieses Template gibt ihn aus. Außerdem entfernt: die Zeilen „SECTION" und „n PAGES". |
| `page/single.html` | Statische Seiten ohne Blog-Möbel: kein Datum, keine Lesezeit, kein Lizenzhinweis, Titel genau einmal. |
| `research/record.html` | Dasselbe für die Belegliste der Weiterbildungen. |

Die letzten beiden bauen auf den gemeinsamen Partials
`_partials/clean-page-main.html` (Aufbau) und `_partials/toc-setup.html`
(Inhaltsverzeichnis) auf, damit sie nicht auseinanderlaufen.

### Die Falle mit der Body-Klasse

**Stacks komplettes Artikel-Stylesheet hängt an `.article-page`.** Fehlt die
Klasse, bekommt `.article-content` keine Textfarbe – hell fällt das kaum auf,
im Dark Mode steht dann dunkler Text auf dunklem Grund.

Jedes eigene Template braucht deshalb:

```go-html-template
{{ define "body-class" }}article-page{{ end }}
```

Das war der Grund, warum Startseite und alle Bereichsübersichten zwischenzeitlich
im Dark Mode unlesbar waren.

### Layout per Frontmatter wählen

`layout: "record"` sucht `layouts/<section>/record.html`.

> **Nicht `type:` dafür benutzen.** `type: "page"` nimmt eine Seite aus
> `mainSections` heraus – und daraus baut Stack den Suchindex. Die Seite wäre
> dann nicht mehr auffindbar. Bei `content/about/` ist `type: "page"`
> unproblematisch, weil `about` ohnehin nicht in `mainSections` steht.

---

## Shortcodes

| Aufruf | wozu |
|---|---|
| `{{< category-board >}}` | Farbige Kacheln aller Kategorien eines Bereichs, mit Anzahl |
| `{{< meta >}}…{{< /meta >}}` | Gedämpfte Zusatzzeile für Datum, Ort, Förderer |
| `{{< topics >}}a, b, c{{< /topics >}}` | Schlagwort-Reihe für Themen ohne eigene Kategorie |

### category-board

Liest die Kategorien aus den Beiträgen des jeweiligen Bereichs aus – **es gibt
keine Liste zu pflegen**. Details in
[`kategorien-und-tags.md`](kategorien-und-tags.md).

Die Farben werden gleichmäßig über den Farbkreis verteilt. Stack leitet
Kategoriefarben sonst aus einem *Hash* des Namens ab, wodurch verschiedene
Kategorien zwangsläufig dieselbe Farbe bekommen – bei 16 Kategorien war das
mehrfach der Fall.

### meta

Warum ein Shortcode und nicht `{.meta}`: Goldmark hängt Blockattribute **nicht**
an einfache Absätze. Die Klasse wurde stillschweigend verschluckt.

---

## Aussehen (`custom.scss`)

Farben und Schrift stehen als Variablen ganz oben – wer etwas ändern will,
ändert dort eine Zeile.

| | hell | dunkel |
|---|---|---|
| Hintergrund | warmes Creme `#f8f1ea` | warmes Dunkelbraun `#262022` |
| Überschriften und Akzente | Burgunder `#7b2d3b` | helles Rose `#e8a7b0` |
| Überschriftenschrift | Georgia-Serife (Systemschrift) | dieselbe |

Kontraste geprüft: 9,2 : 1 hell, 5,1 : 1 dunkel – beides über der
Barrierefreiheitsschwelle von 4,5 : 1.

### Einheiten: `em`, nicht `rem`

> Stack setzt `html { font-size: 62.5% }`, die Wurzelschriftgröße ist also
> **10 px**. Eine Angabe wie `font-size: 0.9rem` ergibt dadurch **9 px**, nicht
> 14 px. In `custom.scss` deshalb immer `em` verwenden – das bezieht sich auf
> das Elternelement.

### Bilderreihen als Raster

Stack setzt mehrere Bilder in einem Absatz als „justierte" Reihe: die Breite
hängt vom Seitenverhältnis ab, ein Hochformat wird zum schmalen Streifen.
Ersetzt durch ein CSS-Grid mit gleich großen Kacheln, das automatisch umbricht.
Ein *einzelnes* Bild behält sein Seitenverhältnis.

> **Zusammengehörig:** Stack gibt `.gallery` einen negativen Seitenrand
> **und** `width: calc(100% + card-padding * 2)`, damit Bilder bis an den
> Kartenrand laufen. Wer nur die Ränder überschreibt (etwa mit `margin: 1.5em 0`),
> behält die Überbreite – die Reihe wird dann rechts abgeschnitten.

### Rechte Spalte

Suche und Archiv sind schmaler als im Theme (24 % ab 1024 px, 21 % ab 1280 px
statt 30 % / 25 %). Die Inhaltsspalte wächst dadurch von 655 auf 704 px.

---

## Datenschutz

`_partials/footer/components/custom-font.html` ist **absichtlich leer**.

Das Theme lädt dort per JavaScript die Schrift „Lato" von
`fonts.googleapis.com` nach – bei **jedem Seitenaufruf**, wodurch die
IP-Adresse jedes Besuchers an Google übertragen wird. Deutsche Gerichte haben
dazu bereits entschieden. Der Fließtext nutzt jetzt die Systemschrift.

Die Bild-Lightbox **PhotoSwipe** wird aus demselben Grund ebenfalls selbst
ausgeliefert. Sie lag ursprünglich auf `cdn.jsdelivr.net`, und zwar nicht erst
beim Klick: zwei der drei Dateien wurden **sofort beim Seitenaufruf** geholt
(im Browser gemessen: 11 ms und 14 ms nach dem Laden), auf 54 von 90 Seiten.

Jetzt liegen die drei Dateien unter `assets/photoswipe/` (PhotoSwipe 5.4.4,
MIT-Lizenz, zusammen ~74 KB) und `_partials/article/components/photoswipe.html`
verweist darauf.

> Bei einem Theme-Update pruefen, ob `data/external.toml` des Themes noch auf
> Version 5.4.4 zeigt - falls nicht, die drei Dateien erneuern.

**Die Seite ruft damit beim Aufruf keine fremden Server mehr auf.** Geprueft mit
`performance.getEntriesByType("resource")`: null externe Anfragen, vor und nach
dem Klick auf ein Bild.

---

## Icons

Eigene Icons liegen in `assets/icons/` und werden in `menu.toml` per Dateiname
ohne Endung referenziert (`icon = "research"`).

Für **Navigationsicons** im Theme-Stil: 24×24 viewBox, `stroke="currentColor"`,
`fill="none"`, `stroke-width="2"`.

> Icons mit fester Füllfarbe (`fill:#000000`) sind im Dark Mode schwarz auf
> dunkel. Für Marken-Icons wie LinkedIn und ORCID ist eine feste Farbe dagegen
> in Ordnung und sogar besser erkennbar – dort einfach prüfen, dass sie auf
> hellem *und* dunklem Grund funktionieren.

Schnell prüfen, ohne die Seite zu bauen:

```bash
magick -background '#1c1c1c' -density 300 assets/icons/research.svg -resize 120x120 /tmp/check.png
```
