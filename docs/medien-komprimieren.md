# Fotos und Videos komprimieren

Warum überhaupt: Die Website liefert Bilder **unverändert** aus – Hugo verkleinert
hier nichts automatisch. Ein Handyfoto mit 4,5 MB ist also ein Foto mit 4,5 MB,
das jeder Besucher herunterlädt. Und GitHub blockt einzelne Dateien über 100 MB
komplett; die veröffentlichte Seite darf insgesamt nicht über 1 GB liegen.

Zum Vergleich, was das ausmacht:

| Datei | vorher | nachher |
|---|---|---|
| `travelling.jpg` (4080×3072) | 3,84 MB | **0,18 MB** |
| `20220831_123031.jpg` (4032×3024) | 4,65 MB | **0,47 MB** |
| Gesamter `content/`-Ordner | 1,8 GB | **182 MB** |

---

## Vorbereitung

**ImageMagick** ist bereits installiert (Version 7). Prüfen mit:

```bash
magick -version
```

Falls es einmal fehlt: <https://imagemagick.org/script/download.php#windows>,
beim Installieren „Add application directory to your system path" ankreuzen.

> **Wichtig:** Immer `magick` benutzen, **niemals** `convert`.
> Unter Windows ist `convert` ein Systemwerkzeug zum Formatieren von
> Festplatten. Der Befehl heißt bei ImageMagick 7 `magick`.

---

## Schritt 1: Immer zuerst sichern

Komprimieren überschreibt die Originale. Also vorher kopieren – nach
`_local-media/`, das steht in `.gitignore` und landet nie auf GitHub:

```bash
mkdir -p _local-media/originals && cd content && find . -type f \( -iname "*.jpg" -o -iname "*.png" \) -exec cp --parents {} ../_local-media/originals/ \; && cd ..
```

Das hat sich schon einmal ausgezahlt: Beim ersten Durchlauf ist eine Datei
verloren gegangen (siehe „Windows-Falle" unten) und war aus der Sicherung
in Sekunden zurück.

---

## Schritt 2: Fotos verkleinern

Für **einen einzelnen Ordner** (der Normalfall, wenn du einen neuen Beitrag
angelegt hast):

```bash
cd content/playground/mein-neues-projekt && magick mogrify -auto-orient -strip -resize "2000x2000>" -quality 82 -interlace Plane -sampling-factor 4:2:0 *.jpg
```

Was die Optionen tun:

| Option | Bedeutung |
|---|---|
| `-auto-orient` | Dreht das Bild nach dem EXIF-Drehflag. **Muss vor `-strip` stehen**, sonst liegen hochkant aufgenommene Fotos hinterher quer. |
| `-strip` | Entfernt EXIF-Daten: Kameramodell, Zeitstempel, ggf. GPS-Koordinaten. Spart Platz und ist gut für die Privatsphäre. |
| `-resize "2000x2000>"` | Verkleinert auf maximal 2000 px Kantenlänge. Das `>` heißt: **nur verkleinern, nie vergrößern**. Kleine Bilder bleiben unangetastet. |
| `-quality 82` | JPEG-Qualität. 82 ist der Punkt, an dem man den Unterschied nicht mehr sieht, die Datei aber deutlich schrumpft. |
| `-interlace Plane` | Progressives JPEG – baut sich beim Laden grob-zu-fein auf. |
| `-sampling-factor 4:2:0` | Farbunterabtastung, wie sie jede Kamera ohnehin verwendet. |

2000 px sind großzügig: Die Inhaltsspalte der Website ist rund 700 px breit,
und selbst die Lightbox braucht nicht mehr.

### Nur behalten, was wirklich kleiner wird

Bei schon optimierten Bildern kann das Neukodieren die Datei **größer** machen.
Diese Schleife prüft das und lässt solche Dateien in Ruhe:

```bash
for f in *.jpg; do o=$(stat -c%s "$f"); magick "$f" -auto-orient -strip -resize "2000x2000>" -quality 82 -interlace Plane -sampling-factor 4:2:0 /tmp/out.jpg; n=$(stat -c%s /tmp/out.jpg); if [ "$n" -lt "$o" ]; then mv /tmp/out.jpg "$f"; echo "$f: $o -> $n"; else rm /tmp/out.jpg; echo "$f: unveraendert"; fi; done
```

### PNG-Fotos zu JPEG machen

PNG ist für Screenshots, Zeichnungen und Grafiken mit Text richtig.
Für **Fotos** ist es die falsche Wahl – reines Neukodieren bringt dort fast
nichts (gemessen: −7 %), die Umwandlung nach JPEG dagegen sehr viel (−88 %).

```bash
magick foto.png -auto-orient -strip -resize "2000x2000>" -quality 82 foto.jpg
```

Danach `foto.png` löschen **und den Verweis im `index.md` auf `.jpg` ändern** –
sonst zeigt das Bild ins Leere.

### Die Windows-Falle

Windows unterscheidet **nicht** zwischen `Bild.JPG` und `Bild.jpg`. Ein Skript,
das aus `Bild.JPG` ein `Bild.jpg` erzeugt und danach das Original löscht,
löscht damit die gerade erzeugte Datei.

Wenn Dateien mit großgeschriebener Endung dabei sind: erst umbenennen, dann
komprimieren.

```bash
for f in *.JPG; do mv "$f" "${f%.JPG}-tmp.jpg"; mv "${f%.JPG}-tmp.jpg" "${f%.JPG}.jpg"; done
```

---

## Schritt 3: Kontrollieren

```bash
find content -type f \( -iname "*.jpg" -o -iname "*.png" \) -size +1M -printf "%s %p\n" | sort -rn | head
```

Kommt nichts zurück, ist keine Datei über 1 MB – dann passt es.

---

## Videos

Für Videos braucht es **ffmpeg**, das ist derzeit **nicht installiert**:
<https://www.gyan.dev/ffmpeg/builds/> (Variante „release essentials", entpacken,
den `bin`-Ordner in den PATH aufnehmen).

### Erst die Frage: braucht es überhaupt ein Video?

Meistens nicht. Eine gute Fotostrecke erzählt einen Arbeitsablauf oft besser,
weil man sie **beim Scrollen mitliest** – Videos startet kaum jemand.

Ein Video lohnt nur, wenn **die Bewegung selbst der Inhalt ist**: die
Tondüse beim Drucken, die Stickmaschine, eine Pirouette.

### Die Faustregeln

| Fall | Vorgehen | Zielgröße |
|---|---|---|
| Kurzer Clip, Bewegung ist der Punkt | Im Repo behalten, stumm, als Schleife | **< 2–3 MB** |
| 10 s – 1 min mit echtem Inhalt | Hart komprimieren | **< 10 MB** |
| Länger, mit Ton, Musik oder Menschen | **YouTube** und einbetten | 0 MB im Repo |

### Kurzer Clip fürs Repo

```bash
ffmpeg -i original.mp4 -vf "scale='min(1280,iw)':-2" -c:v libx264 -crf 28 -preset slow -an -movflags +faststart clip.mp4
```

`-an` wirft die Tonspur weg (bei einer stummen Schleife unnötig),
`-crf 28` ist die Qualitätsstufe – höher heißt kleiner,
`+faststart` sorgt dafür, dass die Wiedergabe startet, bevor alles geladen ist.

Einbinden als stumme Schleife, die sich wie ein bewegtes Bild verhält:

```html
<video muted loop playsinline preload="metadata" width="640">
  <source src="clip.mp4" type="video/mp4">
</video>
```

> Auf den MIME-Typ achten: `type="video/mp4"`. In einem Beitrag stand einmal
> `type="placeholder/mp4"` – damit spielt das Video in keinem Browser.

### Längeres Video über YouTube

Hochladen (gern „nicht gelistet", dann findet es nur, wer den Link hat) und im
Beitrag einbinden. Das Theme bringt den Shortcode mit:

```markdown
{{< youtube VIDEO_ID >}}
```

Die ID ist der Teil hinter `watch?v=` in der YouTube-Adresse.

---

## Was tun mit den Originalen

Die unkomprimierten Originale bleiben in `_local-media/originals/`. Der Ordner
ist in `.gitignore`, geht also nie nach GitHub – er liegt nur lokal auf deiner
Platte.

Sehr große Rohdateien, die gar nicht auf die Website sollen, gehören nach
`_local-media/` statt nach `content/`. So lag dort einmal ein 1,4-GB-Video,
das nirgends eingebunden war und allein den ganzen Push blockiert hätte.

---

## Merkzettel

1. Fotos in den Beitragsordner legen
2. Sichern (falls noch nicht geschehen)
3. `magick mogrify …` im Beitragsordner ausführen
4. Prüfen: keine Datei über 1 MB
5. Im `index.md` einbinden
