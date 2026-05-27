# Design: Packwerk Talk — Dresden.rb

## Kontext

- **Veranstaltung:** Dresden.rb Ruby User Group
- **Zielgruppe:** Techies / Ruby-Entwickler, kennen den Namen Packwerk, haben es aber nicht eingesetzt
- **Dauer:** ~30 Minuten
- **Format:** Vortrag mit Live-Demo
- **Sprecher:** Steve Reinke (webit) — produktiver Einsatz von Packwerk im eigenen Projekt

---

## Titelideen

**Technisch + neugierig:**
- „Modular Monoliths in Rails mit Packwerk"
- „Rails ohne Big Ball of Mud"
- „Grenzen ziehen im Rails-Monolithen"
- „Packwerk in der Praxis"

**Provokativ / Meetup-Style:**
- „Packwerk kann deinen Monolithen nicht retten"
- „Der Mythos des modularen Monolithen"
- „Wie Shopify versucht hat, Rails skalierbar zu halten"

---

## Roter Faden

Der Talk beantwortet eine größere Frage:

> „Wie hält man große Rails-Monolithen langfristig wartbar?"

Packwerk ist das Werkzeug innerhalb dieser Story — kein Feature-Vortrag, sondern ein Architektur-Talk.

**Hauptthese:**

> *„Rails skaliert technisch erstaunlich gut. Was nicht skaliert, sind implizite Abhängigkeiten zwischen Teams und Domänen."*

**Kernsatz:**

> *„Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."*

---

## Hauptbotschaft

Das Publikum verlässt den Raum mit dem Gefühl: „Ich sehe, wie Packwerk echte Architekturprobleme löst."

---

## Struktur

| Block | Inhalt | Zeit |
|---|---|---|
| 1. Motivation | Der Schmerz eines wachsenden Monolithen | 4–5 min |
| 2. Packwerk-Konzept | Was ist es, wie funktioniert es (begrifflich) | 5 min |
| 3. Wie setzen wir Packwerk bei webit! ein. | Package-Struktur + Erfahrungen aus der Praxis | 17–18 min |
| 4. Fazit & Q&A | Wann lohnt es sich, Ressourcen | 3 min |
| **Gesamt** | | **~29–31 min** |

---

## Block 1: Motivation (4–5 min)

**Ziel:** Das Publikum fühlt den Schmerz, bevor die Lösung gezeigt wird — und versteht, warum Disziplin allein keine Antwort ist.

1. **Einstiegsfrage** — „Wer hat schon mal in einem Rails-Projekt gearbeitet, wo niemand mehr wusste, was man noch anfassen darf?"
2. **Bild: Cohesion-Diagramm (Shopify/packwerk)** — zeigt visuell, wie Abhängigkeiten im Monolithen eskalieren
   ![Cohesion](https://raw.githubusercontent.com/Shopify/packwerk/main/docs/cohesion.png)
3. **Kernaussage** — „Rails gibt euch eine Struktur für Dateien. Wer mit wem reden darf — das regelt niemand."
4. **Einwand vorwegnehmen: „Warum nicht einfach Disziplin?"**
   - Menschen vergessen Regeln
   - Neue Entwickler kennen sie nicht
   - Zeitdruck gewinnt immer
   - → *„Code Reviews skalieren schlechter als automatische Regeln."*
5. **Überleitung** — „Wie macht man unsichtbare Abhängigkeiten sichtbar — und durchsetzbar?"

---

## Block 2: Packwerk-Konzept (5 min)

**Ziel:** Die Kernidee verstehen — ohne in der Doku zu ertrinken.

1. **Herkunft** — Shopify entwickelte es für ihr großes monolithisches System und veröffentlichte es im September 2020.

2. **Analogie in einem Satz** — „Packwerk ist ein Linter für Architekturgrenzen."

3. **Drei Kernkonzepte** (je ~30 Sekunden):
   - **Package** — Verzeichnis mit `package.yml`, definiert eine fachliche Einheit (z.B. `packs/billing`)
   - **Privacy** — Klassen können `private` sein: nur das eigene Package darf sie nutzen
   - **Dependency** — ein Package deklariert explizit, welche anderen Packages es nutzen darf

4. **Das Werkzeug** — statischer Analyse-Checker: liest Code, meldet Verstöße — keine Runtime-Magie, kein Framework-Lock-in
   *Tonspur:* Packwerk bindet den Code nicht enger an Rails — und auch nicht an Packwerk selbst.

5. **`package_todo.yml`** — bestehende Verstöße werden dokumentiert, nicht sofort behoben: pragmatischer Einstieg in Legacy-Projekten

6. **Einordnung im Vergleich:**

| Ansatz | Vorteil | Nachteil |
|---|---|---|
| Klassischer Rails-Monolith | Schnell | Entropie |
| Rails Engines | Stärkere Isolation | Schwergewichtiger |
| Microservices | Klare Grenzen | Operative Hölle |
| Packwerk | Leichte Boundary Enforcement | Nur statische Analyse |

---

## Block 3: Wie setzen wir Packwerk bei webit! ein. (17–18 min)

**Ziel:** Am echten Projekt zeigen, wie Packwerk strukturiert wird — und ehrlich berichten, was gut lief und was schwierig war.

### Package-Struktur (2–3 min)

JACK ist eine Plattform zur Planung und Auslieferung von Lebensmitteln für Großküchen — 7 Domain-Packages, strikt getrennt.

**Packages und ihre Abhängigkeiten (Folie):**

```
┌───────────────────────────────────────────────────────┐
│                   ROOT / app/                         │
│            (Controllers, Views, Jobs)                 │
└──────────────────────┬────────────────────────────────┘
                       │ nur über public/ und Events
       ┌───────────────┼──────────────────────┐
       ▼               ▼                      ▼
 order_management   portioning          commissioning
                        ↕                    ↕
                  material_tracking       logistics
                       │
          alle abhängig von
       events → architecture_core → core
```

**Aufbau eines Domain-Packages (Folie):**

Jedes Domain-Package enthält selbst mehrere Packwerk-Packages — Packwerk erzwingt also nicht nur Grenzen zwischen Domains, sondern auch zwischen den Schichten *innerhalb* einer Domain:

```
lib/packages/portioning/           ← Domain-Package
├── package.yml
├── public/                        ← sichtbar für alle anderen Packages
│
├── domain/                        ← eigenes Packwerk-Package
│   └── package.yml                  visible_to: [portioning]
│
├── infrastructure/                ← eigenes Packwerk-Package
│   └── package.yml                  visible_to: [portioning]
│
└── application/                   ← eigenes Packwerk-Package
    └── package.yml                  visible_to: [portioning]
```

Cross-Package-Referenzen nur über UUIDs — keine ActiveRecord-Assoziationen zwischen Packages.

### Erfahrungen (7–8 min)

**Einstieg mit dem Kernsatz** (groß auf der Folie, kurze Pause):

> *„Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."*

**Kontext** (30 sek) — Projektgröße und Einsatzdauer bei webit

**Was gut funktioniert hat** (2–3 min)
- `package_todo.yml` als schrittweiser Einstieg war entscheidend
- *(Platzhalter — eigene Learnings)*
- *(Platzhalter — eigene Learnings)*

**Was schwieriger war als erwartet** (2–3 min)
- Die erste Package-Struktur war falsch — Umschneiden kostet Zeit
- Team-Buy-in braucht Überzeugungsarbeit
- *(Platzhalter — eigene Learnings)*

**Ehrliche Nachteile**
- Package-Grenzen sind sozial, nicht nur technisch — das ist eine Kommunikationsaufgabe, kein Tooling-Problem
- `package_todo.yml` kann zur Ausrede werden, Violations nie wirklich zu beheben
- Zu viele Packages machen alles schlimmer — Micro-Packaging ist ein Anti-Pattern
- „Modular Monolith" kann Buzzword werden ohne die Disziplin dahinter
- Teams umgehen Regeln irgendwann — Werkzeug ersetzt keine Teamkultur

**Schluss des Blocks:** „Ich zeige euch das nicht um euch abzuschrecken — sondern damit ihr realistisch einsteigt."

---

## Block 4: Fazit & Q&A (3 min)

**Wann lohnt sich Packwerk?**
- Rails-Monolith mit mehreren Teams oder mehr als einem Entwickler pro Bereich
- Wenn Architektur-Diskussionen immer wieder dieselben Probleme zeigen
- Wenn langfristig Services angestrebt werden, aber der Zeitpunkt noch nicht gekommen ist

**Wann lohnt es sich nicht?**
- Kleine Teams mit einem Codebase-Owner
- Projekte in früher Phase — erst das Produkt finden, dann Struktur

**Ressource:** Shopify Engineering Blog + GitHub-Repo (ein Link, kein Folienstapel)

**Abschluss-Satz mit aktuellem Bezug:**
> *„Wenn AI schneller Code produziert, brauchen wir bessere Grenzen zwischen diesem Code."*

Dann Q&A öffnen.

---

## Was im Vortrag vermieden werden sollte

- Zu viel YAML — langweilt sofort
- Zu viele Internals (AST Parsing etc.) — nur kurz erwähnen
- Zu akademisch — Ruby User Groups mögen pragmatische Talks
- Folienstapel mit 10 Links am Ende
