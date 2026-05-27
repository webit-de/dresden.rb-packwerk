# Design: Packwerk-Folien — Marp + webit-Theme

## Kontext

Ergänzung zu `2026-05-26-packwerk-talk-design.md` — beschreibt die technische und visuelle Umsetzung der Präsentationsfolien für den Dresden.rb Packwerk-Vortrag als HTML-Datei.

---

## Entscheidungen

| Frage | Entscheidung |
|---|---|
| Format | Marp (Markdown → HTML) |
| Output | Eine einzelne `.html`-Datei |
| Theme-Ansatz | Separate `webit.css` + `packwerk.md` (Ansatz B) |
| Build | npm + `@marp-team/marp-cli` als devDependency |
| Farben | Nur webit-Grün + Anthrazit (kein Lila, kein Pink) |

---

## Repository-Struktur

```
dresden.rb-packwerk/
├── slides/
│   ├── packwerk.md          # Folien-Inhalt (Marp-Markdown)
│   ├── webit.css            # Custom Marp-Theme
│   └── packwerk.html        # Generierte HTML-Datei (committed)
├── .marprc.yml              # Marp-Konfiguration
├── package.json             # npm + Marp CLI
└── docs/
```

---

## Build-Setup

**`package.json`**
```json
{
  "name": "dresden-rb-packwerk-slides",
  "scripts": {
    "build": "marp slides/packwerk.md -o slides/packwerk.html",
    "watch": "marp --watch slides/packwerk.md -o slides/packwerk.html"
  },
  "devDependencies": {
    "@marp-team/marp-cli": "^3"
  }
}
```

**`.marprc.yml`**
```yaml
theme: ./slides/webit.css
html: true
```

---

## Theme-Spezifikation (`webit.css`)

### Farbpalette

| Variable | Wert | Verwendung |
|---|---|---|
| `--webit-green` | `#26D07C` | Primärakzent, Bullets, Linien, Tags |
| `--webit-anthrazit` | `#3A3A3A` | Titel, obere Linie, Sektion-BG |
| `--webit-gray` | `#4D4D4D` | Fließtext |
| `--webit-light-gray` | `#C0C0C0` | Trennlinien, Seitenzahl |
| `--webit-bg` | `#FFFFFF` | Hintergrund |
| `--webit-code-bg` | `#f0fdf4` | Code-Hintergrund |

### Schrift

- **Quelle:** Google Fonts — Manrope (weights: 300, 400, 500)
- **Titel:** Manrope 500 (Medium), `#3A3A3A`
- **Body:** Manrope 300 (Light), `#4D4D4D`

### Folien-Typen

#### Standard-Inhaltsfolie (default)
- Hintergrund: `#FFFFFF`
- Obere Linie: 3px `#3A3A3A`
- Untere Linie: 3px `#26D07C`
- Titelzeile: Manrope 500, `#3A3A3A`, mit `2px solid #26D07C` als Unterstreichung
- Bullets: grüne Kreise `#26D07C`
- Seitenzahl: rechts unten, `#C0C0C0`

#### Titelfolie (`_class: title`)
- Hintergrund: `#FFFFFF`
- Obere Linie: 3px `#3A3A3A`
- Untere Linie: 3px `#26D07C`
- Grünes Tag-Element für Veranstaltung/Datum
- Haupttitel: Manrope 500, groß, `#3A3A3A`
- Untertitel und Sprecher: Manrope 300, `#4D4D4D` / `#9D9D9D`
- Zwei dekorative Linien unten links: Grün + Anthrazit

#### Sektion-Folie (`_class: section`)
- Hintergrund: `#3A3A3A` (Anthrazit)
- Obere Linie: 4px `#26D07C`
- Block-Nummer: `#26D07C`, Monospace, Uppercase
- Titel: Manrope 500, `#FFFFFF`
- Untertitel: Manrope 300, `#9D9D9D`
- Dekorative Linie: 40px `#26D07C`

#### Code-Folie (Standard + Code-Block)
- Code-Block: Hintergrund `#f0fdf4`, Text `#26D07C` (Inline) oder Anthrazit (Block)
- Monospace-Schrift

---

## Folien-Inventar (~20 Folien)

| # | Typ | Titel / Inhalt | Block |
|---|---|---|---|
| 1 | `title` | Rails ohne Big Ball of Mud | — |
| 2 | Standard | Agenda (4 Blöcke) | — |
| 3 | `section` | Block 1: Motivation | 1 |
| 4 | Standard | Einstiegsfrage | 1 |
| 5 | Bild | Cohesion-Diagramm (Shopify/packwerk) — `cohesion.png` | 1 |
| 6 | Standard | Kernaussage | 1 |
| 7 | Standard | Warum nicht einfach Disziplin? + Überleitung | 1 |
| 8 | `section` | Block 2: Packwerk-Konzept | 2 |
| 9 | Standard | Herkunft: Shopify + Analogie | 2 |
| 10 | Standard | Drei Kernkonzepte | 2 |
| 11 | Standard | Das Werkzeug + `package_todo.yml` | 2 |
| 12 | Standard | Vergleichstabelle | 2 |
| 13 | `section` | Block 3: Wie setzen wir Packwerk bei webit! ein. | 3 |
| 14 | Standard | JACK: Paketabhängigkeiten (Diagramm) | 3 |
| 15 | Standard | Aufbau Domänenpaket (Unterpakete) | 3 |
| 16 | Standard | Kontext: 23 Pakete, von Beginn an | 3 |
| 17 | Standard | Kernsatz (groß) | 3 |
| 18 | Standard | Positive Erfahrungen | 3 |
| 19 | Standard | Herausforderungen | 3 |
| 20 | `section` | Block 4: Fazit & Q&A | 4 |
| 21 | Standard | Wann lohnt es sich / wann nicht | 4 |
| 22 | Standard | Ressource + Abschluss-Satz | 4 |

**Gesamt: 22 Folien** (4 Sektion-Trenner + 17 Inhaltsfolien + 1 Bild-Folie)

**Bild-Folie (Cohesion-Diagramm):**
- URL: `https://raw.githubusercontent.com/Shopify/packwerk/main/docs/cohesion.png`
- Wird beim Build heruntergeladen oder lokal als `slides/cohesion.png` gecacht

---

## Inhalts-Quelle

Alle Texte direkt aus `docs/superpowers/specs/2026-05-26-packwerk-talk-design.md` — kein neuer Inhalt, nur Übertragung in Marp-Markdown-Format mit dem webit-Theme.

---

## Was nicht enthalten ist

- Speaker Notes (können in Marp als `<!-- -->` Kommentare ergänzt werden, sind aber kein Ziel dieses Plans)
- Animationen / Transitions (Marp unterstützt nur CSS-basierte Transitions, vorerst nicht geplant)
- Bilder / Logos (webit-Logo kann nachträglich in die Titelfolie eingefügt werden)
