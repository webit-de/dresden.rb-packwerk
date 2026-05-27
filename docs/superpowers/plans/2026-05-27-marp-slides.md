# Marp Slides Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Präsentationsfolien für den Dresden.rb Packwerk-Vortrag als HTML-Datei mit Marp und webit-Theme.

**Architecture:** Marp-Markdown (`slides/packwerk.md`) + Custom CSS-Theme (`slides/webit.css`) → eine einzelne `slides/packwerk.html`. Build via `npm run build`. Kein JavaScript-Framework, keine Dependencies außer `@marp-team/marp-cli`.

**Tech Stack:** Node.js, `@marp-team/marp-cli ^3`, Marp-Markdown, CSS Custom Properties, Google Fonts (Manrope)

---

## File Structure

| Datei | Zweck |
|-------|-------|
| `package.json` | npm-Setup, Build-Skripte, Marp CLI als devDependency |
| `.marprc.yml` | Marp-Konfiguration (Theme-Pfad, HTML erlaubt) |
| `slides/webit.css` | Custom Marp-Theme (Farben, Schrift, Folien-Typen) |
| `slides/cohesion.png` | Heruntergeladenes Cohesion-Diagramm von Shopify/packwerk |
| `slides/packwerk.md` | Alle 22 Folien in Marp-Markdown |
| `slides/packwerk.html` | Generierte HTML-Datei (committed) |

---

## Task 1: Build-Setup

**Files:**
- Create: `package.json`
- Create: `.marprc.yml`

- [ ] **Step 1: `package.json` anlegen**

```json
{
  "name": "dresden-rb-packwerk-slides",
  "private": true,
  "scripts": {
    "build": "marp slides/packwerk.md -o slides/packwerk.html",
    "watch": "marp --watch slides/packwerk.md -o slides/packwerk.html"
  },
  "devDependencies": {
    "@marp-team/marp-cli": "^3"
  }
}
```

- [ ] **Step 2: `.marprc.yml` anlegen**

```yaml
theme: ./slides/webit.css
html: true
```

- [ ] **Step 3: npm install ausführen**

```bash
npm install
```

Erwartete Ausgabe: `node_modules/` wird angelegt, `package-lock.json` wird erstellt.

- [ ] **Step 4: Commit**

```bash
git add package.json package-lock.json .marprc.yml
git commit -m "feat: add Marp build setup"
```

---

## Task 2: webit-Theme

**Files:**
- Create: `slides/webit.css`

- [ ] **Step 1: `slides/webit.css` anlegen**

```css
/* @theme webit */
@import url('https://fonts.googleapis.com/css2?family=Manrope:wght@300;400;500&display=swap');

:root {
  --webit-green: #26D07C;
  --webit-anthrazit: #3A3A3A;
  --webit-gray: #4D4D4D;
  --webit-light-gray: #C0C0C0;
  --webit-bg: #FFFFFF;
  --webit-code-bg: #f0fdf4;
}

/* ─── Basis-Folie ─────────────────────────────────────── */
section {
  background: var(--webit-bg);
  color: var(--webit-gray);
  font-family: 'Manrope', sans-serif;
  font-weight: 300;
  font-size: 22px;
  padding: 48px 64px 40px;
  box-sizing: border-box;
  width: 1280px;
  height: 720px;
  border-top: 3px solid var(--webit-anthrazit);
  border-bottom: 3px solid var(--webit-green);
  display: flex;
  flex-direction: column;
}

section h1 {
  font-weight: 500;
  font-size: 1.6em;
  color: var(--webit-anthrazit);
  border-bottom: 2px solid var(--webit-green);
  padding-bottom: 8px;
  margin-bottom: 24px;
  margin-top: 0;
}

section h2 {
  font-weight: 500;
  font-size: 1.15em;
  color: var(--webit-anthrazit);
  margin-top: 20px;
  margin-bottom: 8px;
}

section ul {
  padding-left: 1.4em;
}

section ul li {
  margin-bottom: 10px;
  line-height: 1.5;
}

section ul li::marker {
  color: var(--webit-green);
  font-size: 1.1em;
}

section strong {
  color: var(--webit-anthrazit);
  font-weight: 500;
}

section blockquote {
  border-left: 4px solid var(--webit-green);
  margin: 16px 0;
  padding: 8px 20px;
  color: var(--webit-anthrazit);
  font-style: normal;
}

/* Seitenzahl */
section::after {
  content: attr(data-marpit-pagination);
  position: absolute;
  bottom: 12px;
  right: 20px;
  color: var(--webit-light-gray);
  font-size: 0.65em;
  font-weight: 300;
}

/* Code */
section code {
  background: var(--webit-code-bg);
  color: var(--webit-green);
  padding: 1px 5px;
  border-radius: 3px;
  font-size: 0.85em;
}

section pre {
  background: var(--webit-code-bg);
  border-left: 3px solid var(--webit-green);
  padding: 16px 20px;
  border-radius: 4px;
  overflow: hidden;
}

section pre code {
  background: transparent;
  color: var(--webit-anthrazit);
  padding: 0;
  font-size: 0.78em;
  line-height: 1.5;
}

section table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.88em;
}

section th {
  background: var(--webit-anthrazit);
  color: #FFFFFF;
  font-weight: 500;
  padding: 8px 12px;
  text-align: left;
}

section td {
  padding: 7px 12px;
  border-bottom: 1px solid var(--webit-light-gray);
}

section tr:last-child td {
  border-bottom: none;
}

section tr:nth-child(even) td {
  background: #f7f7f7;
}

/* ─── Titelfolie ──────────────────────────────────────── */
section.title {
  justify-content: space-between;
}

section.title h1 {
  font-size: 2.4em;
  border-bottom: none;
  margin-top: auto;
  margin-bottom: 16px;
  line-height: 1.2;
}

section.title .tag {
  display: inline-block;
  background: var(--webit-green);
  color: #FFFFFF;
  font-size: 0.75em;
  font-weight: 500;
  padding: 4px 12px;
  border-radius: 2px;
  margin-bottom: 24px;
}

section.title .subtitle {
  color: var(--webit-gray);
  font-size: 1em;
  font-weight: 300;
  margin-bottom: 4px;
}

section.title .speaker {
  color: var(--webit-light-gray);
  font-size: 0.85em;
  font-weight: 300;
  margin-bottom: auto;
}

section.title .deco {
  display: flex;
  gap: 6px;
  margin-bottom: 8px;
}

section.title .deco span:first-child {
  width: 40px;
  height: 3px;
  background: var(--webit-green);
  display: block;
}

section.title .deco span:last-child {
  width: 24px;
  height: 3px;
  background: var(--webit-anthrazit);
  display: block;
}

/* ─── Sektion-Folie ───────────────────────────────────── */
section.section {
  background: var(--webit-anthrazit);
  color: #FFFFFF;
  border-top: 4px solid var(--webit-green);
  border-bottom: none;
  justify-content: center;
}

section.section h1 {
  color: #FFFFFF;
  font-size: 2em;
  border-bottom: none;
  margin-bottom: 8px;
}

section.section h2 {
  color: #9D9D9D;
  font-size: 1em;
  font-weight: 300;
  margin-top: 0;
}

section.section .block-nr {
  font-family: monospace;
  font-size: 0.75em;
  color: var(--webit-green);
  text-transform: uppercase;
  letter-spacing: 0.1em;
  margin-bottom: 12px;
}

section.section::before {
  content: '';
  display: block;
  width: 40px;
  height: 3px;
  background: var(--webit-green);
  margin-bottom: 24px;
}

/* ─── Zitat-Folie ─────────────────────────────────────── */
section.quote {
  justify-content: center;
  align-items: center;
  text-align: center;
}

section.quote h1 {
  font-size: 1.7em;
  color: var(--webit-anthrazit);
  border-bottom: none;
  font-weight: 500;
  line-height: 1.4;
  max-width: 900px;
}
```

- [ ] **Step 2: Commit**

```bash
git add slides/webit.css
git commit -m "feat: add webit Marp theme"
```

---

## Task 3: Cohesion-Bild herunterladen

**Files:**
- Create: `slides/cohesion.png`

- [ ] **Step 1: Bild herunterladen**

```bash
curl -L -o slides/cohesion.png \
  https://raw.githubusercontent.com/Shopify/packwerk/main/docs/cohesion.png
```

Erwartete Ausgabe: `slides/cohesion.png` vorhanden, Dateigröße > 0.

```bash
ls -lh slides/cohesion.png
```

- [ ] **Step 2: Commit**

```bash
git add slides/cohesion.png
git commit -m "feat: add cohesion diagram image"
```

---

## Task 4: Folien-Inhalt (packwerk.md)

**Files:**
- Create: `slides/packwerk.md`

- [ ] **Step 1: `slides/packwerk.md` anlegen**

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

---

## Agenda

1. **Motivation** — Warum reicht Rails-Struktur allein nicht?
2. **Packwerk-Konzept** — Was ist es, wie funktioniert es?
3. **Wie setzen wir Packwerk bei webit! ein?** — Praxis & Erfahrungen
4. **Fazit & Q&A**

---

<!-- _class: section -->

# Motivation

## Der Schmerz eines wachsenden Monolithen

---

## Wer kennt das?

> „Wer hat schon mal eine Codeänderung gemacht, bei der plötzlich Dinge umgefallen sind, mit denen du nichts zu tun haben wolltest?"

---

![bg contain](cohesion.png)

---

## Rails gibt Struktur — aber nicht genug

> **„Rails gibt euch eine Struktur für Dateien.**
> **Wer mit wem reden darf — das regelt niemand."**

MVC und Convention over Configuration lösen nicht das Problem von Abhängigkeiten.

---

## Warum nicht einfach Disziplin?

- Menschen vergessen Regeln
- Neue Entwickler kennen sie nicht
- Zeitdruck gewinnt immer

> **„Code Reviews skalieren schlechter als automatische Regeln."**

→ Wie macht man Abhängigkeiten **sichtbar — und durchsetzbar?**

---

<!-- _class: section -->

# Packwerk-Konzept

## Was ist es, wie funktioniert es?

---

## Woher kommt Packwerk?

Shopify entwickelte es für ihren großen Rails-Monolithen — nicht wegen Ruby-Syntax, sondern wegen Team- und Skalierungsproblemen.

Veröffentlicht **September 2020.**

> **„Packwerk ist ein Linter für Architekturgrenzen."**

---

## Drei Kernkonzepte

**Paket** — Verzeichnis mit `package.yml`, definiert eine fachliche Einheit
`packs/billing`, `packs/users`, …

**Privacy** — Klassen können `private` sein: nur das eigene Paket darf sie nutzen

**Dependency** — ein Paket deklariert explizit, welche anderen Pakete es nutzen darf

---

## Das Werkzeug

**Statischer Analyse-Checker:** liest Code, meldet Verstöße
Keine Runtime-Magie · Kein Framework-Lock-in

*Packwerk bindet den Code nicht enger an Rails — und auch nicht an Packwerk selbst.*

**`package_todo.yml`**

Bestehende Verstöße werden dokumentiert, nicht sofort behoben — pragmatischer Einstieg in Legacy-Projekten.

---

## Einordnung im Vergleich

| Ansatz | Vorteil | Nachteil |
|--------|---------|----------|
| Rails-Monolith | Schnell | Entropie |
| Rails Engines | Stärkere Isolation | Schwergewichtiger |
| Microservices | Klare Grenzen | Operative Hölle |
| **Packwerk** | **Leichte Boundary Enforcement** | **Nur statische Analyse** |

---

<!-- _class: section -->

# Wie setzen wir Packwerk bei webit! ein?

## Paketstruktur & Erfahrungen aus der Praxis

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

---

## JACK bei webit! — Kontext

- **23 Pakete** insgesamt
- **5 Domänenpakete** (order_management, portioning, commissioning, material_tracking, logistics)
- je mit Unterpaketen: `domain/`, `infrastructure/`, `application/`, `core_extension/`
- **3 Basis-Pakete:** `core`, `architecture_core`, `events`
- Packwerk **von Beginn an** im Einsatz

---

<!-- _class: quote -->

# „Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."

---

## Was gut funktioniert hat

- Lose Kopplung der Domänenaspekte durch Pakete mit klar definierter public API
- Strikte Architekturregeln durch Unterpakete — Domäneschicht frei von externen Abhängigkeiten (DDD)
- Aufgaben gut parallelisierbar durch klare Paketgrenzen
- Refactoring innerhalb eines Pakets ohne Außenwirkung — dank stabiler public API

---

## Herausforderungen

- Die richtige Paketstruktur und -größe zu finden ist nicht trivial — falsche Einteilung kostet beim Umschneiden Zeit
- Teamkonsens zu Architekturregeln notwendig — fehlendes Wissen erzeugt Reibungsverluste
- UI-Aggregation über mehrere Pakete hinweg aufwändiger als im klassischen Monolithen

---

<!-- _class: section -->

# Fazit & Q&A

## Wann lohnt es sich?

---

## Wann lohnt sich Packwerk?

**Ja, wenn:**
- Langfristige Lebensdauer eines Rails-Monolithen, vermutlich insbesondere, wenn mehrere Teams daran arbeiten
- Wenn Architektur-Smells auffallen, z.B. durch [Rubrowser](https://github.com/emad-elsaid/rubrowser)
- Wenn langfristig Services angestrebt werden, aber der Zeitpunkt noch nicht gekommen ist

**Eher nicht, wenn:**
- Projekte in früher Phase — erst das Produkt finden, dann Struktur

---

## Ressourcen & Abschluss

**GitHub:** [Shopify/packwerk](https://github.com/Shopify/packwerk)
**Blog:** [Shopify Engineering Blog](https://shopify.engineering/enforcing-modularity-rails-apps-packwerk)

> „KI schreibt Code schneller, als Menschen ihn in der Tiefe reviewen und prüfen können. Gut strukturierter Code macht das leichter. Packwerk setzt diese Struktur durch."

**Fragen?**
```

- [ ] **Step 2: Commit**

```bash
git add slides/packwerk.md
git commit -m "feat: add slide content (22 slides)"
```

---

## Task 5: Build & Verify

**Files:**
- Create: `slides/packwerk.html`

- [ ] **Step 1: HTML generieren**

```bash
npm run build
```

Erwartete Ausgabe: `slides/packwerk.html` wird erstellt.

```bash
ls -lh slides/packwerk.html
```

- [ ] **Step 2: Visuell prüfen**

```bash
xdg-open slides/packwerk.html
```

Checklist:
- [ ] 22 Folien vorhanden (Seitenzahl rechts unten)
- [ ] Titelfolie: weißer Hintergrund, grünes Tag, Anthrazit-Titel
- [ ] Sektion-Folien: Anthrazit-Hintergrund, grüne Linie oben, Titel weiß
- [ ] Standard-Folien: weiß, grüne Linie oben, grüne Linie unten, Titel mit grüner Unterstreichung
- [ ] Zitat-Folie (Kernsatz): zentriert, kein Border unten beim Titel
- [ ] Cohesion-Diagramm (Folie 5) sichtbar
- [ ] Code-Blöcke: grüner Hintergrund, lesbare Schrift
- [ ] Tabelle auf Folie 12 korrekt dargestellt
- [ ] Google Fonts (Manrope) geladen

- [ ] **Step 3: Commit**

```bash
git add slides/packwerk.html
git commit -m "feat: add generated HTML slides"
```
