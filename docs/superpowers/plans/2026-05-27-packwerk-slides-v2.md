# Packwerk-Slides Umsetzungsplan (v2)

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Präsentationsfolien für den Dresden.rb Packwerk-Vortrag als vollständige HTML-Datei mit Marp und webit-Theme — Inhalt direkt aus `docs/superpowers/specs/2026-05-26-packwerk-talk-design.md`.

**Architecture:** `slides/packwerk.md` (Marp-Markdown, 22 Folien) + `slides/webit.css` (Custom Theme, bereits vorhanden) → `slides/packwerk.html` via `npm run build`. Keine Dependencies außer `@marp-team/marp-cli`. Kein JavaScript-Framework.

**Tech Stack:** Node.js, `@marp-team/marp-cli`, Marp-Markdown, CSS Custom Properties, Google Fonts (Manrope)

---

## File Structure

| Datei | Status | Zweck |
|---|---|---|
| `slides/packwerk.md` | Bearbeiten | 22 Folien, 4 Blöcke |
| `slides/webit.css` | ✓ vorhanden | Theme — nicht anfassen |
| `slides/cohesion.png` | ✓ vorhanden | Cohesion-Diagramm (Folie 5) |
| `slides/packwerk.html` | Generiert | HTML-Output, committed |
| `package.json` | ✓ vorhanden | Build-Skripte |
| `.marprc.yml` | ✓ vorhanden | Marp-Konfiguration |

**Folien-Inventar:**

| # | Typ | Titel | Block |
|---|---|---|---|
| 1 | `title` | Rails ohne Big Ball of Mud | — |
| 2 | Standard | Agenda | — |
| 3 | `section` | Motivation | 1 |
| 4 | Standard | Wer kennt das? (Einstiegsfrage) | 1 |
| 5 | Bild | Cohesion-Diagramm | 1 |
| 6 | Standard | Rails gibt Struktur — aber nicht genug | 1 |
| 7 | Standard | Warum nicht einfach Disziplin? | 1 |
| 8 | `section` | Packwerk-Konzept | 2 |
| 9 | Standard | Woher kommt Packwerk? | 2 |
| 10 | Standard | Drei Kernkonzepte | 2 |
| 11 | Standard | Das Werkzeug + package_todo.yml | 2 |
| 12 | Standard | Einordnung im Vergleich | 2 |
| 13 | `section` | Wie setzen wir Packwerk bei webit! ein? | 3 |
| 14 | Standard | JACK — Paketabhängigkeiten | 3 |
| 15 | Standard | Aufbau eines Domänenpakets | 3 |
| 16 | Standard | JACK bei webit! — Kontext | 3 |
| 17 | `quote` | Kernsatz | 3 |
| 18 | Standard | Was gut funktioniert hat | 3 |
| 19 | Standard | Herausforderungen | 3 |
| 20 | `section` | Fazit & Q&A | 4 |
| 21 | Standard | Wann lohnt sich Packwerk? | 4 |
| 22 | Standard | Ressourcen & Abschluss | 4 |

---

## Task 1: Rahmen — Titelfolie & Agenda (Folien 1–2)

**Files:**
- Modify: `slides/packwerk.md` (Anfang der Datei)

- [ ] **Step 1: Datei-Kopf und Titelfolie anlegen**

```markdown
---
marp: true
theme: webit
paginate: true
---

<!-- _class: title -->

<div class="tag">Dresden.rb · 2026</div>

# Rails ohne Big Ball of Mud

<div class="subtitle">Packwerk in der Praxis</div>
<div class="speaker">Steve Reinke & Christoph Wagner · webit!</div>

<div class="deco"><span></span><span></span></div>
```

- [ ] **Step 2: Agenda-Folie anlegen**

```markdown
---

## Agenda

1. **Motivation** — Warum reicht Rails-Struktur allein nicht?
2. **Packwerk-Konzept** — Was ist es, wie funktioniert es?
3. **Wie setzen wir Packwerk bei webit! ein?** — Praxis & Erfahrungen
4. **Fazit & Q&A**
```

- [ ] **Step 3: Build und prüfen**

```bash
npm run build
```

Erwartete Ausgabe: `slides/packwerk.html` wird erstellt, keine Fehler.

- [ ] **Step 4: Commit**

```bash
git add slides/packwerk.md slides/packwerk.html
git commit -m "feat: add title and agenda slides"
```

---

## Task 2: Block 1 — Motivation (Folien 3–7)

**Spec-Referenz:** `Block 1: Motivation (4–5 min)`

**Files:**
- Modify: `slides/packwerk.md`

- [ ] **Step 1: Sektion-Folie Motivation**

```markdown
---

<!-- _class: section -->

# Motivation

## Der Schmerz eines wachsenden Monolithen
```

- [ ] **Step 2: Einstiegsfrage (Folie 4)**

```markdown
---

## Wer kennt das?

> „Wer hat schon mal eine Codeänderung gemacht, bei der plötzlich Dinge umgefallen sind, mit denen du nichts zu tun haben wolltest?"
```

- [ ] **Step 3: Cohesion-Diagramm (Folie 5)**

```markdown
---

![bg contain](cohesion.png)
```

- [ ] **Step 4: Kernaussage (Folie 6)**

```markdown
---

## Rails gibt Struktur — aber nicht genug

> **„Rails gibt euch eine Struktur für Dateien.**
> **Wer mit wem reden darf — das regelt niemand."**

MVC und Convention over Configuration lösen nicht das Problem von Abhängigkeiten.
```

- [ ] **Step 5: Disziplin-Einwand + Überleitung (Folie 7)**

```markdown
---

## Warum nicht einfach Disziplin?

- Menschen vergessen Regeln
- Neue Entwickler kennen sie nicht
- Zeitdruck gewinnt immer

> **„Code Reviews skalieren schlechter als automatische Regeln."**

→ Wie macht man Abhängigkeiten **sichtbar — und durchsetzbar?**
```

- [ ] **Step 6: Build und prüfen**

```bash
npm run build
```

Prüfen: 7 Folien vorhanden, Cohesion-Bild auf Folie 5 sichtbar, Sektion-Folie anthrazit mit grüner Linie oben.

- [ ] **Step 7: Commit**

```bash
git add slides/packwerk.md slides/packwerk.html
git commit -m "feat: add Block 1 Motivation slides (3-7)"
```

---

## Task 3: Block 2 — Packwerk-Konzept (Folien 8–12)

**Spec-Referenz:** `Block 2: Packwerk-Konzept (5 min)`

**Files:**
- Modify: `slides/packwerk.md`

- [ ] **Step 1: Sektion-Folie Packwerk-Konzept**

```markdown
---

<!-- _class: section -->

# Packwerk-Konzept

## Was ist es, wie funktioniert es?
```

- [ ] **Step 2: Herkunft + Analogie (Folie 9)**

```markdown
---

## Woher kommt Packwerk?

Shopify entwickelte es für ihren großen Rails-Monolithen — nicht wegen Ruby-Syntax, sondern wegen Team- und Skalierungsproblemen.

Veröffentlicht **September 2020.**

> **„Packwerk ist ein Linter für Architekturgrenzen."**
```

- [ ] **Step 3: Drei Kernkonzepte (Folie 10)**

```markdown
---

## Drei Kernkonzepte

**Paket** — Verzeichnis mit `package.yml`, definiert eine fachliche Einheit
`packs/billing`, `packs/users`, …

**Privacy** — Klassen können `private` sein: nur das eigene Paket darf sie nutzen

**Dependency** — ein Paket deklariert explizit, welche anderen Pakete es nutzen darf
```

- [ ] **Step 4: Das Werkzeug + package_todo.yml (Folie 11)**

```markdown
---

## Das Werkzeug

**Statischer Analyse-Checker:** liest Code, meldet Verstöße
Keine Runtime-Magie · Kein Framework-Lock-in

*Packwerk bindet den Code nicht enger an Rails — und auch nicht an Packwerk selbst.*

**`package_todo.yml`**

Bestehende Verstöße werden dokumentiert, nicht sofort behoben — pragmatischer Einstieg in Legacy-Projekten.
```

- [ ] **Step 5: Vergleichstabelle (Folie 12)**

```markdown
---

## Einordnung im Vergleich

| Ansatz | Vorteil | Nachteil |
|--------|---------|----------|
| Rails-Monolith | Schnell | Entropie |
| Rails Engines | Stärkere Isolation | Schwergewichtiger |
| Microservices | Klare Grenzen | Operative Hölle |
| **Packwerk** | **Leichte Boundary Enforcement** | **Nur statische Analyse** |
```

- [ ] **Step 6: Build und prüfen**

```bash
npm run build
```

Prüfen: 12 Folien vorhanden, Tabelle auf Folie 12 korrekt dargestellt.

- [ ] **Step 7: Commit**

```bash
git add slides/packwerk.md slides/packwerk.html
git commit -m "feat: add Block 2 Packwerk-Konzept slides (8-12)"
```

---

## Task 4: Block 3 — webit! Einsatz (Folien 13–19)

**Spec-Referenz:** `Block 3: Wie setzen wir Packwerk bei webit! ein. (17–18 min)`

**Files:**
- Modify: `slides/packwerk.md`

- [ ] **Step 1: Sektion-Folie webit!**

```markdown
---

<!-- _class: section -->

# Wie setzen wir Packwerk bei webit! ein?

## Paketstruktur & Erfahrungen aus der Praxis
```

- [ ] **Step 2: JACK Paketabhängigkeiten (Folie 14)**

```markdown
---

## JACK — Paketabhängigkeiten

JACK ist eine Plattform zur Planung und Auslieferung von Lebensmitteln für Großküchen.

```
ROOT / app/  (Controllers, Views, Jobs)
        │  nur über public/ und Events
        ├──▶ order_management
        ├──▶ portioning  ↔  material_tracking
        └──▶ commissioning  ↔  logistics

        alle abhängig von:
        events → architecture_core → core
```
```

- [ ] **Step 3: Aufbau Domänenpaket (Folie 15)**

```markdown
---

## Aufbau eines Domänenpakets

Packwerk erzwingt Grenzen zwischen Domänen — und zwischen den **Schichten innerhalb** einer Domäne:

```
lib/packages/portioning/
├── public/           ← sichtbar für alle anderen Pakete
├── domain/           ← eigenes Paket · visible_to: [portioning]
├── infrastructure/   ← eigenes Paket · visible_to: [portioning]
├── application/      ← eigenes Paket · visible_to: [portioning]
└── core_extension/   ← geteilte Basistypen für andere Pakete
```

Cross-Paket-Referenzen nur über UUIDs — keine ActiveRecord-Assoziationen.
```

- [ ] **Step 4: JACK Kontext (Folie 16)**

```markdown
---

## JACK bei webit! — Kontext

- **23 Pakete** insgesamt
- **5 Domänenpakete** (order_management, portioning, commissioning, material_tracking, logistics)
- je mit Unterpaketen: `domain/`, `infrastructure/`, `application/`, `core_extension/`
- **3 Basis-Pakete:** `core`, `architecture_core`, `events`
- Packwerk **von Beginn an** im Einsatz
```

- [ ] **Step 5: Kernsatz-Folie (Folie 17)**

```markdown
---

<!-- _class: quote -->

# „Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."
```

- [ ] **Step 6: Positive Erfahrungen (Folie 18)**

```markdown
---

## Was gut funktioniert hat

- Lose Kopplung der Domänenaspekte durch Pakete mit klar definierter public API
- Strikte Architekturregeln durch Unterpakete — Domäneschicht frei von externen Abhängigkeiten (DDD)
- Aufgaben gut parallelisierbar durch klare Paketgrenzen
- Refactoring innerhalb eines Pakets ohne Außenwirkung — dank stabiler public API
```

- [ ] **Step 7: Herausforderungen (Folie 19)**

```markdown
---

## Herausforderungen

- Die richtige Paketstruktur und -größe zu finden ist nicht trivial — falsche Einteilung kostet beim Umschneiden Zeit
- Teamkonsens zu Architekturregeln notwendig — fehlendes Wissen erzeugt Reibungsverluste
- Das Aggregieren und Aufbereiten von Informationen für die UI über mehrere Pakete hinweg ist aufwändiger als im klassischen Monolithen
```

- [ ] **Step 8: Build und prüfen**

```bash
npm run build
```

Prüfen: 19 Folien vorhanden, Kernsatz-Folie (17) zentriert ohne Titelunterstreichung, Code-Blöcke lesbar.

- [ ] **Step 9: Commit**

```bash
git add slides/packwerk.md slides/packwerk.html
git commit -m "feat: add Block 3 webit! Einsatz slides (13-19)"
```

---

## Task 5: Block 4 — Fazit & Q&A (Folien 20–22)

**Spec-Referenz:** `Block 4: Fazit & Q&A (3 min)`

**Files:**
- Modify: `slides/packwerk.md`

- [ ] **Step 1: Sektion-Folie Fazit**

```markdown
---

<!-- _class: section -->

# Fazit & Q&A

## Wann lohnt es sich?
```

- [ ] **Step 2: Wann lohnt sich Packwerk? (Folie 21)**

```markdown
---

## Wann lohnt sich Packwerk?

**Ja, wenn:**
- Langfristige Lebensdauer eines Rails-Monolithen, vermutlich insbesondere, wenn mehrere Teams daran arbeiten
- Wenn Architektur-Smells auffallen, z.B. durch [Rubrowser](https://github.com/emad-elsaid/rubrowser)
- Wenn langfristig Services angestrebt werden, aber der Zeitpunkt noch nicht gekommen ist

**Eher nicht, wenn:**
- Projekte in früher Phase — erst das Produkt finden, dann Struktur
```

- [ ] **Step 3: Ressourcen & Abschluss (Folie 22)**

```markdown
---

## Ressourcen & Abschluss

**GitHub:** [Shopify/packwerk](https://github.com/Shopify/packwerk)
**Blog:** [Shopify Engineering Blog](https://shopify.engineering/enforcing-modularity-rails-apps-packwerk)

> „KI schreibt Code schneller, als Menschen ihn in der Tiefe reviewen und prüfen können. Gut strukturierter Code macht das leichter. Packwerk setzt diese Struktur durch."

**Fragen?**
```

- [ ] **Step 4: Build und prüfen**

```bash
npm run build
```

Prüfen: genau 22 Folien vorhanden (Seitenzahl rechts unten).

- [ ] **Step 5: Commit**

```bash
git add slides/packwerk.md slides/packwerk.html
git commit -m "feat: add Block 4 Fazit slides (20-22)"
```

---

## Task 6: Vollständige Verifikation

**Files:**
- Read: `slides/packwerk.html`

- [ ] **Step 1: HTML öffnen**

```bash
xdg-open slides/packwerk.html
```

- [ ] **Step 2: Folien-Checkliste**

| # | Folie | Erwartung |
|---|---|---|
| 1 | Titelfolie | Weißer Hintergrund, grünes Tag, Anthrazit-Titel, Deko-Linien unten |
| 2 | Agenda | 4 nummerierte Punkte |
| 3 | Sektion Motivation | Anthrazit-BG, grüne Linie oben, weißer Titel |
| 4 | Einstiegsfrage | Blockquote mit grünem linken Rand |
| 5 | Cohesion-Diagramm | Bild vollständig sichtbar |
| 6 | Rails gibt Struktur | Blockquote, Fließtext darunter |
| 7 | Disziplin | 3 Bullets, Blockquote, Überleitung |
| 8 | Sektion Packwerk | Anthrazit-BG |
| 9 | Woher kommt Packwerk? | Shopify + Linter-Analogie |
| 10 | Drei Kernkonzepte | Paket / Privacy / Dependency |
| 11 | Das Werkzeug | `package_todo.yml` in Code-Formatierung |
| 12 | Vergleichstabelle | 4 Zeilen, Packwerk fett |
| 13 | Sektion webit! | Anthrazit-BG |
| 14 | JACK Abhängigkeiten | ASCII-Diagramm lesbar |
| 15 | Domänenpaket | ASCII-Verzeichnisbaum lesbar |
| 16 | JACK Kontext | 23 Pakete, 5 Domänenpakete, 3 Basis-Pakete |
| 17 | Kernsatz | Zentriert, keine Titelunterstreichung |
| 18 | Positive Erfahrungen | 4 Bullets |
| 19 | Herausforderungen | 3 Bullets |
| 20 | Sektion Fazit | Anthrazit-BG |
| 21 | Wann lohnt sich Packwerk? | Ja/Eher-nicht-Struktur, Rubrowser-Link |
| 22 | Ressourcen & Abschluss | GitHub-Link, Blog-Link, Abschluss-Satz, „Fragen?" |

- [ ] **Step 3: Final Commit**

```bash
git add slides/packwerk.html
git commit -m "chore: verify and finalize slides"
```
